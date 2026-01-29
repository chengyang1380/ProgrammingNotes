# Explore structured concurrency in Swift

![**Glenorchy Wharf & Viewpoint, New Zealand**](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/DJI_0070.jpg?raw=true)

**Glenorchy Wharf & Viewpoint, New Zealand**

## Structured Concurrency

從 Swift 5.5 開始引入了新的 concurrent programs，這個 structured concurrency 是來自 structured programming，非常直觀

首先簡單介紹什麼是 structured programming

![截圖 2026-01-21 14.48.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_14.48.20.png?raw=true)

平常我們寫的 `if-then` 使用 structured control-flow，只在指定 block 在有條件的由上到下執行。

在 Swift 中把 then block 稱為 static scope，這代表名稱是可見，block 中定義的 variables 生命週期跟 block 一樣，block 結束就會一同結束，所以具有 static scope 的 structured programming ，能使 control-flow 及 variables 更好理解。

![截圖 2026-01-21 14.54.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_14.54.26.png?raw=true)

structured control-flow 是 sequenced 的，這讓我們從上到下閱讀 code 更容易

![截圖 2026-01-21 15.00.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.00.36.png?raw=true)

我們來看一些例子，這是最常見的情境之一，把一堆圖片按順序調整成縮圖

![截圖 2026-01-21 15.12.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.12.21.png?raw=true)

仔細看會有些痛點

1. 要注意看這個 function 有沒有 return a value，我們要透過 completion handler 傳遞給 caller，最怕就是忘記寫 completion handler，compiler 不會檢查
2. 另外要注意 error handling 的部分，不能用 structured control-flow 處理錯誤，現在在要交給 caller 處理錯誤，而不是在 function 內直接處理
    
    ![截圖 2026-01-21 15.14.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.14.47.png?raw=true)
    
3. 在圖片縮圖處理是 recursion，要等 `prepareThumbnail(of:)` 完成後，才能執行，目前需要 nested 在裡面，但在 nested 裡無法使用
    
    ![截圖 2026-01-21 15.20.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.20.49.png?raw=true)
    

現在我們來改造，使用 async/await 語法重寫，且基於 structured programming，

![截圖 2026-01-21 15.25.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.25.00.png?raw=true)

這樣的改動，主要分為

1. 在 callback completion handler 的部分改成 `async throws -> [String: UIImage]` 
2. 任何需要非同步的地方使用 `await` 
3. 可能會有 error 的地方加上 `try`
4. 這樣就不需要有 nested code

而且 compiler 會檢查有沒有忘記 return or throws

關於 async/await 的細節可參考 [WWDC21 Meet async/await in Swift](https://developer.apple.com/videos/play/wwdc2021/10132/)

雖然上述的 code 很棒，但假如遇到情境是上千張圖片要轉成縮圖呢？

一張一張處理絕對不是好方法，另外如果每張縮圖的尺寸必須從另一個 URL 下載，而不是固定大小怎麼辦？這樣狀況使用 concurrency，來多次下載。

Task 是 Swift 的新功能，專門處理這種問題

![截圖 2026-01-21 15.45.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_15.45.34.png?raw=true)

- A task provides a new async context for executing code concurrently：專門來與 async function 協同工作，這 task 也提供一個新的執行上下文來運行 asynchronous code，每個 task 相對於其他執行上下文採取 concurrently 執行，而且他們在安全高效的情況下，將被自動安排為 parallel 執行
- Swift checks your usage of tasks to help prevent concurrency bugs：由於 Task 已深度 integrated into Swift，因此 compiler 可以 prevent some concurrency bugs。
- When calling an async function a task is not created：使用 async function 不會 create task，需明確 create task

Swift 中有幾種不同風格的 Task，因為 structured concurrency 的重點在於靈活性和簡單性之間有個平衡，接下來就是介紹和討論各種任務

## Async-Let Tasks

在這句 `let result = …` 之前與之後有 preceding statements 及 following statements，在 code 執行到 `URLSession.shared.data(...)` 時，它的 initializer 會被 evaluate 以產生一個 value，用這範例來說，URL 下載需要一段時間，data 下載完成後，在繼續後面的語句 (following statements) 之前，Swift 會將該 value 綁定到 variable name，請注意，這裡只有一個執行流程，如同每個步驟的箭頭所示。

![截圖 2026-01-21 16.16.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_16.16.49.png?raw=true)

而這個下載的事情是非同步的，現在只要在 let 綁定之前加上 `async` 就可以了，這稱為 `async-let` ，在 concurrency binding 的 evaluate 與 sequential binding 有很大不同，接下來看看如何 work 的

![截圖 2026-01-21 16.43.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_16.43.31.png?raw=true)

首先在 evaluate 的 concurrency binding 前，Swift 首先會先 create 一個新的 child task，因為每個 task 都代表 program 的一個 execution context，這一步會同時出現兩個箭頭，第一個箭頭用於 child task，他將立即開始下載，第二個箭頭指向 parent task，會立即把 variable result 綁定在 placeholder value，該 parent task 與上述語句 (preceding statements) 的任務相同。

![截圖 2026-01-21 16.51.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_16.51.24.png?raw=true)

當 parent task 繼續執行，遵循 concurrency 綁定的語句 (following statements)，但是在達到需要 result 的 actual value 時，parent task 將等待 child task 完成。 

![截圖 2026-01-21 16.55.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_16.55.02.png?raw=true)

那在這個範例是一個 URL download 的過程，所以也有可能會有 error，所以記得要有 `try` ，以上就是 `async-let` 的工作原理。

現在的情境改成會 download full-sized 圖片及 metadata，之後再透過這兩個結果，生成 thumbnail

![截圖 2026-01-21 17.12.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.12.58.png?raw=true)

為了讓這兩個 download 同時進行，我們就透過剛剛的方式，在 let 前面加上 `async` ，這樣等同把下載的事情發生在 child task，就不用朝 concurrent binding 右側加上 `try await`，只有在使用 concurrently bound 的 variables 時，parent task 才會觀察到這些影響

![截圖 2026-01-21 17.15.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.15.38.png?raw=true)

所以在讀取 metadata 及 image 前的 expression 記得要加上 `try await`，另外也要注意，使用這些 concurrently bound variable，不需要 method call 或任何其他更改，這些 variable 的類型與他們在順序綁定中的類型相同

![截圖 2026-01-21 17.18.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.18.46.png?raw=true)

### Task Tree

前面一直敘述的 child task，實際上是稱為 task tree 的層次結構的一部分，這顆 tree 不僅僅是一個實作細節，它是 structured concurrency 的重要組成部分，他會影響 task 的 attributes，例如 cancellation, priority 和 task-local variables，每當從一個 async function 調用另一個 function 時，都會使用相同的 task 來執行呼叫。

因此 function `fetchOneThumbnail(withID:)` 裡的 child task 會繼承該 task 的所有 attributes，當使用 async-let create 一個新的 structured task 時，他會成為當前 function 正在運行 task 的 child task。

注意 task 不是指定 function 的 child，這邊容易混肴，我們的 function 不一定是 task，可以想像這個 async function 只是「路徑」而 task 才是「跑者」，所以呼叫一個 async function 並不會自動建立一個新的 task，當你在 Function A 裡面寫 `await FunctionB()` 時，Swift 實際上是讓**同一個 Task**（原本在執行 A 的那個）暫停手邊的工作，轉而去執行 Function B，所以使用 async-let 或 Task Group 時，才是明確的告訴系統 create **Fresh Execution Context**，才是建立 task，但他們的生命週期只限定在 function 的範圍內。

![截圖 2026-01-21 17.31.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.31.54.png?raw=true)

Tree 由每個 parent task 與其 child task 之間的 links 組成，links 強制執行一條規則，該規則說明 parent task 只有在其所有 child task 都完成了，才能完成自身的 work，即使面對異常的 control-flow（例如阻止等待某個 child task），這條規則依然成立。

![截圖 2026-01-21 17.41.25.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.41.25.png?raw=true)

例如，在這段 code 裡，在讀取 image data task 之前，會先等待 metadata task，如果第一個等待的 task 因拋出 error 而完成，則 `fetchOneThumbnail` function 會立即完成，透過拋出的那個 error 來 exit

![截圖 2026-01-21 17.48.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.48.26.png?raw=true)

metadata 因拋出 error，要結束，但是 download data 的 task 怎麼辦？不管他，會變成 leaked task，會繼續佔用資源、記憶體之類的，而且沒人接收結果，但別擔心，在異常退出時，Swift 會自動將為等待的 task 標記為已取消，然後再退出 function 之前，等待它完成，將 task 標記為已取消不會停止該任務，他只是通知 task 不在需要它的結果。

![截圖 2026-01-21 17.51.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.51.26.png?raw=true)

事實上，當一個 task 被取消時，所有該 task 繼承的 child task，也會自動取消，所以假設 URLSession 的實現裡，也 create 自己的 structured task 來 download 的話，這些 task 也會被標記為取消，function `fetchOneThumbnail` 最後也會退出，透過拋出 error 的方式，就是他 create 所有 structured task，直接或間接完成後，這種保證是 structured concurrency 的基礎，它透過幫助你管理 task 的生命週期，來防止意外的 leaking task，可以想像是 ARC 如何自動管理記憶體一樣。

![截圖 2026-01-21 17.57.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-21_17.57.06.png?raw=true)

### Cancellation is cooperative

Task 什麼時候結束呢？

如果 task 處於重要的事務之間或需抱持網路連接，只是停止任務是不正確的，這就是為什麼 Swift 中的 task 取消是 cooperative 的。

- Tasks are **not** stopped immediately when cancelled：Code 必須明確檢查取消，並以任何適當的方式結束執行
- Cancellation can be checked from anywhere：可以從任何 function 檢查當前 task 的 cancellation status，無論是否是 async function。
- Design your code with cancellation in mind：在實作 API 時，記得考慮取消，特別是需長時間運行的，user 可能會從可以取消的 task 中呼叫我們的 code，並且他們會預期運行盡快停止。

那要如何檢查呢？

依照之前的 thumbnail 例子來看，有兩種做法

`try Task.checkCancellation()` ，如 果當前 task 已被取消，則這段 code 會 throw error

![截圖 2026-01-22 10.42.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-22_10.42.00.png?raw=true)

另外一種方式，取得當前 task 的 cancellation status 作為 bool `if Task.isCancelled { ... }` 

![截圖 2026-01-22 10.44.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-22_10.44.21.png?raw=true)

但要注意因為這個 function 是要 return 一個 [String: UIImage]，執行這操作，確保 API 有明確說明可能只會返回部分結果（不完整的結果），否則假如 user 觸發 task cancelled，可能會有 bug 出現。

## Group tasks

接下來接紹 group tasks，它比 async-let 提供更多靈活性，且一樣是 structured concurrency。

當有固定數量的 task 時 async-let work 的很順利，但假如是 dynamic 數量的話該怎麼做？

一樣是 thumbnail 的例子，原本我們在 `fetchOneThumbnail` 裡使用了2個 async-let 的做法，沒問題，但在 `fetchThumbnails` 裡要使用 for-loop 來獲取 id 並取得 thumbnail，這會變成一次只能1張縮圖，很明顯這樣是一個 dynamic 數量的狀況

![截圖 2026-01-22 11.03.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-22_11.03.13.png?raw=true)

這情況就很適合 task group，它是一種 structured concurrency 並提供 dynamic 形式，可以使用 `withThrowingTaskGroup` function

![截圖 2026-01-22 11.08.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-22_11.08.20.png?raw=true)

 其中我們可以加上 `group.async { ... }` ，在 group 中 create child task，加入 group 後，這些 child task 可以以任何順序立即開始執行，當 group object 超出 scope 之後，會隱式等待其中的所有 task 完成，這也是前面敘述的 task tree 規則的結果，group task 也是 structured。

![截圖 2026-01-22 11.37.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-22_11.37.24.png?raw=true)

此時我們已經實現我們想要的 concurrency，每個執行都是同時進行，而且我們不只能在 group task 中使用 async-let，也可以在 async-let 中建立 group task。

![截圖 2026-01-23 10.24.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_10.24.34.png?raw=true)

但這個 code 其實還沒有完成，假如執行的話，compiler 會提醒我們有 data race 的問題，問題在我們試圖把每個 child task 中，將 thumbnail 放進 dictionary (`thumbnails: [String: UIImage]`)，這是一種 concurrency 常見的錯誤。

![截圖 2026-01-23 10.39.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_10.39.40.png?raw=true)

在過去我們必須自己調查出這些錯誤，但是 Swift 提供 static checking，來防止這種錯誤。

- Task creation takes a `@Sendable`  closure：當 create 一個新 task 時，該 task 執行的工作的地方會在 `@Sendable` 類型的 closure 中。
- Cannot capture mutable variables：而在這 `@Sendable` closure 裡，會限制上下文中不能 capture mutable variables。
- Should only capture value types, actors, or classes that implement their own synchronization：假如想在 task 啟動後修改 variables ，那在 task 中 capture 的 value，必須可以安全共享，例如：他們是 value type，像 Int 和 String 或因為他們是設計可以從多個 thread access 的，或者有實現自行同步的 actor 和 class。

這個 safety share 的部分，有一個專門的 Session

[WWDC21 Protect mutable state with Swift actor](https://developer.apple.com/videos/play/wwdc2021/10133/)

為了避免 data race，我們可以讓每個 child task return a value，這個設計交給了 parent task，它是唯一處理 result 的角色，所以在我們的情況下，指定每個 child task 必須 return 一個 String ID 及 UIImage 的 tuple，這樣就不是直接改寫 dictionary 了

![截圖 2026-01-23 14.19.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_14.19.24.png?raw=true)

那麼最後就是要把 group task 的結果拿出來使用，所以對於 parent task 可以使用 for-await loop，把每個 child task 的結果拿出來，寫入我們的 dictionary，而這邊會依照他完成的順序執行，因為這邊是依序完成，所以 parent task 可以安全的 access dictionary，並不會 data race 的問題。

![截圖 2026-01-23 14.21.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_14.21.41.png?raw=true)

這是一個使用 for-await loop 來 access asynchronous sequence 的例子，如果我們的 type 有 conform `AsyncSequence` protocol，那也可以使用 for-await loop。

細節可以參考 

[WWDC21 Meet AsyncSequence](https://developer.apple.com/videos/play/wwdc2021/10058/)

雖然 task group 也是 structured concurrency 的一種形式，但在 task tree 的規則上， task group 和 async-let task 是有些小差異，關鍵在正常完成時

假設在 iterating group task 的 result 過程中，遇到一個以 error 方式完成的 child task，這會造成 group 中的所有 task 會以 implicitly canceled，這和 async-let 一樣，然後會自動把 group 的 child task 標記為 canceled，parent task 會等這些 child task 清理完畢後，才拋出 error 並結束。

但假如在正常完成，沒有 error 的情況下，不會自動取消，例如在 group 裡有 10 個 task，但我們只關心前 3 個 task 的 result，但這時 Swift 不會取消那些剩下的 task，系統會 implicit await，等那些 child task 完成後才會離開這個 function，假如是 async-let 沒有去 await 就離開 scope 的話，Swift 不會去理他，這就是他們之間最大的差異，在 task group 預設會希望他們全部做完才能離開 scope，而 async-let 離開 scope 後就是離開。

這種設計是為了方便實作 **Fork-Join（分叉-合併）** 模式：

- **Fork：**你在 Group 裡一口氣新增了 100 個下載任務。
- **Join：**你不需要手動寫 100 次 `await`，也不需要擔心如果 Function 跑完了任務會被砍掉。
- **便利性：**只要 closure 結束，Swift 就會自動幫你「Join（合併/等待）」所有任務。你只要確保沒有 throw error，系統就會保證所有任務都**跑完**而不是被**取消**

另外還可以使用 group 的 `cancelAll` function，在退出 scope 之前手動取消所有 task，請記住，無論如何取消 task，取消都會自動沿著 tree 向下傳遞。

async-let 和 group task 都是 Swift 中提供 scoped structured tasks

## Unstructured Tasks

unstructured task  需要更多手動管理

### Not all tasks fit a structured pattern

有些情況 task 不屬於清晰的層次結構，像是

- Some tasks need to launch from non-async contexts：最明顯的是，如果想嘗試啟動 non async code 裡的 async task，像在想在 UIKit 裡的 viewDidLoad 觸發一個 async task 就是一個好例子。
- Some tasks live beyond the confines of a single scope：想要 task 的 lifetime，不在單個 scope 或單個 function 時，接著會用 UICollectionViewDelegate 當作例子說明。

在 UIKit 裡的 UICollectionViewDelegate 處理 async task 也是一個好例子

在 [WWDC21 Protect mutable state with Swift actor](https://developer.apple.com/videos/play/wwdc2021/10133/) 討論的那樣，Swift 透過 `@MainActor` ，讓 UI 確保在 main thread 上

![截圖 2026-01-23 17.32.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_17.32.35.png?raw=true)

在這個 delegate 是 sync code，不能呼叫 async function，我們要為此開一個 task，但這個 task 會是一個新的延伸，而這邊又是一個 response delegate 行為，所以我們是希望這個 task 還是要以 UI 優先，要在 main actor 上執行，我們不想把該 task 的 lifetime 綁定在單一個 delegate method 的 scope 中。

![截圖 2026-01-23 17.33.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_17.33.56.png?raw=true)

這情況就適合使用一個 unstructured task，透過一個 `Task { ... }` 來建立 async task，當 code 跑到該 task 時，不會立即阻塞 main thread 上的這個 delegate method，然後後續 thumbnail task 完成後，也會在 main thread 上執行

![截圖 2026-01-23 17.44.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_17.44.44.png?raw=true)

- Inherit actor isolation and priority of the origin context：以這種方式 create task，使我們介於 structured 和 unstructured code，而直接 create 的 task 依然 inherits 其啟動上下文的 actor，並且也 inherits origin task 的 priority 等特性，這和 group task 及 async-let 一樣
- Lifetime is note confined to any scope：但新 task 是沒有 unscoped，它的 lifetime 不受啟動範圍的限制
- Can be launched anywhere, even non-async functions：而 origin 不需要來自 async code，可以在任何地方 create 一個 unscoped task
- Must be manually cancelled or awaited：為了獲得所有這些靈活性，我們需要手動管理structured concurrency 會自動處理的事情，像是取消和錯誤不會自動傳播，並且不會 implicitly await task 結果，除非我們採取明確的方式來做。

所以依照剛剛的例子來說，假如在 thumbnail 準備好之前，我們在 collection view scroll 到 view 之外，我們照理來說也應該要取消該 task，由於我們在處理一個 unscoped task，因此取消不是自動的，我們來實作取消功能。

我們先新增一個 property 來存放 task，它是一個 dictionary `[IndexPath: Task<Void, Never>]` ，在 create task 時把它放進去，這樣後續就很容易取消

![截圖 2026-01-23 18.11.48.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-23_18.11.48.png?raw=true)

而且應該在 task 完成後，從 dictionary 中刪除 task (加上 `defer { thumbnailTasks[item] = nil }` ，這樣如果 task 完成就不會嘗試取消他，請注意，可以在這個 async task 裡可以 access 同個 dictionary，不管在內部或者外部，不會得到 compiler 標記的 data race，因為我們的 delegate 是在 main actor，所以我們的 task 也 inherit 這點，所以他們永遠不會 parallel 執行，我們可以安全的 access 在 main actor 綁定的 store property，在此 task 無需擔心 data race。

![截圖 2026-01-26 10.44.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_10.44.50.png?raw=true)

上述的 delegate 是 willDisplay，我們可以從另外一個 delegate didEndDisplay 去取消 task

![截圖 2026-01-26 10.57.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_10.57.18.png?raw=true)

所以現在我們已經知道如何 create unstructured task，而且知道他是獨立 scope 執行，但同時從原始的上下文中 inherit 特徵，但有時候不想從原始的上下文中 inherit 任何內容，我們接著介紹 Detached tasks

## Detached tasks

為了獲得最大的 flexibility，我們就需要使用它 detached task

- Unscoped lifetime, manually cancelled and awaited：它依然是 unstructured task，一樣 lifetime 不受其 originating scope or context 約束
- Do not inherit anything from their originating context：但是它也不會從其 originating context 提取任何內容在自己身上，例如繼承任何特性，by default 它不會被限制在同一個 actor 上，也不必以相同的 priority 執行
- Optional parameters control priority and other traits：它獨立運行，但還是有 priority 之類的設定參數可以使用，可控制它執行方式和位置

假如我們從 server 獲得 thumbnail 後，把它寫入 local disk cache，所以曾經拿過的 thumbnail 不會再跟 server 拿，這是一個 cache 機制，cache 不需要在 main actor 上做，所以這情境很適合使用 detached task 來做 cache 功能，在這也可以設定 priority，因為這個 cache 不會影響 UI，放在 `.background` 上做即可

![截圖 2026-01-26 13.46.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_13.46.22.png?raw=true)

假如在 thumbnail 的部分執行多個 background task，但想要管理它們，該怎麼做？剛剛的 detached task 也可以利用 structured concurrency task，我們可以把所有不同類型的 task 結合在一起

首先設置一個 task group 並 create 每個 background job，這樣 background job 就不是一個獨立 task 了，它們變成 group 裡的 child task

![截圖 2026-01-26 14.04.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_14.04.59.png?raw=true)

這樣做有些好處，假如未來需要取消 background task，使用 task group 就可以取消所有的 child task，只要對 top level 的 detached task 取消就好，他會自動散播到 child task，此外 child task 會自動 inherit 其 parent task 的 priority，這就表示在 top level 的 detached task 有設定好 background priority 的話，所有 child task 都會在 background

![截圖 2026-01-26 14.18.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_14.18.02.png?raw=true)

## Flavors of tasks

- async-let tasks：允許固定數量的 child task，自動管理取消和傳播錯誤
- Group tasks：當我們需要 dynamic 數量的 task，且受範圍限制的 child task，也自動管理取消和傳播錯誤
- Unstructured tasks：如果需要中斷一些範圍不明確但與其 originating task 相關的 work，但就需要手動管理它們
- Detached tasks：需要最大的 flexibility，一樣要手動管理，而且也不會 inherit 任何東西

![截圖 2026-01-26 14.27.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC21/Explore%20structured%20concurrency%20in%20Swift/%E6%88%AA%E5%9C%96_2026-01-26_14.27.03.png?raw=true)

## Concurrency in Swift

Tasks integrate with the rest of Swift concurrency

推薦接著看

[WWDC21 Meet async/await in Swift](https://developer.apple.com/videos/play/wwdc2021/10132/)

[WWDC21 Protect mutable state with Swift actor](https://developer.apple.com/videos/play/wwdc2021/10133/)

[WWDC21 Meet AsyncSequence](https://developer.apple.com/videos/play/wwdc2021/10058/)

[WWDC21 Swift concurrency: Behind the scenes](https://developer.apple.com/videos/play/wwdc2021/10254/)

## Reference

https://developer.apple.com/la/videos/play/wwdc2021/10134/

https://wwdcnotes.com/documentation/wwdcnotes/wwdc21-10134-explore-structured-concurrency-in-swift