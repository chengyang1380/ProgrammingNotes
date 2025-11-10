# Explore concurrency in SwiftUI

![On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/DJI_0046.jpg)

On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.

## Introduction

![Screenshot 2025-11-03 at 17.47.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-03_at_17.47.19.png)

從 Swift 6.2 開始

- 會在所有的 module 中幫所有類型加上 implicitly `@MainActor`
- 無論是否啟用這種新模式，本次旅程中介紹的所有內容都適用

![Screenshot 2025-11-03 at 18.02.51.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-03_at_18.02.51.png)

而這次介紹的旅程，包含三個景點

1. Main-actor Meadows: 了解 SwiftUI 如何將 main actor 作為 app compile time 和 runtime 的默認選擇
2. Concurrency Cliffs: 了解 SwiftUI 從 main thread 減少工作量，避免 app hitch，也同時保護我們受 data-race 的 bug 影響
3. Code camp: 深入思考 concurrent code 和 SwiftUI API 之間的關係

## Main-actor Meadows

![Screen Recording 2025-11-03 at 18.11.49-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen_Recording_2025-11-03_at_18.11.49-1.gif)

首先這 app 功能是從圖片中可以偵測互補色，並且由使用者自己決定顯示互補色數量，最後按下按鈕後會出現結果

![Screen Recording 2025-11-03 at 18.11.49-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen_Recording_2025-11-03_at_18.11.49-2.gif)

向下滾動可以查看所有提取的配色方案，然後可以選擇最喜歡的導出

Swift 使用 data isolation 去理解和驗證，所有 mutable states 的安全性，細節可參考

[WWDC25 Embracing Swift Concurrency](https://developer.apple.com/videos/play/wwdc2025/268/)

![Screenshot 2025-11-04 at 10.18.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.18.29.png)

在 SwiftUI 中的 View 限定在 `@MainActor` 上，因為有 `protocol View` 的規範，而在 ColorExtractorView 上有一個註釋 `@MainActor` 在 compile time 時是隱含存在的，並不是寫出來的顯示 code，所以是會看不到。

而因為有 `@MainActor` 在 ColorExtractorView 上，所以他的 members 也都會被 implicitly isolated，而在 body 使用一些 model.xxx，也是會被保證 access 安全，都會被確保 MainActor isolation。

![Screenshot 2025-11-04 at 10.21.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.21.30.png)

因為有這特性，大多數只需要專注 building 我們的 app 的功能，無需考慮太多 concurrency 的問題

![Screenshot 2025-11-04 at 10.31.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.31.25.png)

而且這樣的特性不僅在 View 的方面上，在 data model (ColorScheme, ColorExtractor) 上也無需加上 `@MainActor` annotations，因為該 model 在 View 裡面 declaration 並 instantiate，所以 Swift 會確保這 model 被正確的 isolated

![Screenshot 2025-11-04 at 10.53.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.53.14.png)

還有像是在 View 裡使用 Task 切換 async context，這個 Task closure 也會在 main thread 上執行

AppKit 和 UIKit 中的 API 其實也完全被 `@MainActor` isolated，SwiftUI 和這些 framework 無縫互相操作，例如：

`protocol UIViewRepresentable: View`

![Screenshot 2025-11-04 at 10.56.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.56.12.png)

![Screenshot 2025-11-04 at 10.57.32.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_10.57.32.png)

SwiftUI 會為它的 API 自動加上 `@MainActor` 這 annotates，這些 annotates 是 run time 語義的延伸，接下來下一個範例可以進一步強化這概念

## Concurrency Cliffs

如果給 main thread 太多任務，可能造成 app frame drop 或 hitches，這時可以用 Task 和 structured concurrency 來處理，減少 main thread 工作量，這部分細節可參考

[WWDC25 Elevate an app with Swift Concurrency](https://developer.apple.com/videos/play/wwdc2025/270/)

![Screenshot 2025-11-04 at 11.04.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.04.33.png)

這部影片關注 SwiftUI 如何利用 Swift concurrency 來提升 app 效能

之前在 WWDC23 有提過，內置動畫使用可以用 background thread，來計算中間狀態

![Screenshot 2025-11-04 at 11.06.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.06.49.png)

![Screen Recording 2025-11-04 at 11.08.09.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen_Recording_2025-11-04_at_11.08.09.gif)

首先我們來看這顆按鈕動畫

![Screenshot 2025-11-04 at 11.12.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.12.52.png)

這個動畫的每一幀都需要 1 到 1.5 之間的不同縮放值，這類動畫值計算設計複雜的數學運算，逐幀計算開銷很大，因此很適合交給 background thread 來計算，讓 main thread 有更多資源處理其他問題

![Screenshot 2025-11-04 at 11.13.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.13.58.png)

所以有時候 SwiftUI 也會不一定在 main thread 上執行

SwiftUI 是 declarative，與 UIView 不同，需 conform View protocol 的 struct，並非佔據固定內存位置的 object。

At runtime，SwiftUI 會為 View 建立獨立的 representation，這種 representation 提供了多種 optimization 機會，一種重要的優化是在 View representation 在 background thread 的評估。

![Screenshot 2025-11-04 at 11.17.23.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.17.23.png)

SwiftUI 將這項技術保留用於，開發者需要執行大量計算的場景，例如：反覆計算一些的幾何，像是 Shape protocol 就是典型的例子。

The Shape protocol requires a method that returns a path，現在建立一個客製化的楔形形狀，用在色輪中表示提取的顏色，而這個 Path 是需要複雜計算的，所以適合在 background thread

![Screenshot 2025-11-04 at 11.27.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_11.27.17.png)

SwiftUI 為開發者執行的另一種自訂邏輯的 closure argument，例如下圖這個場景 `visualEffect` 這個 method 的 closure，這個視覺效果可能很複雜，宣染成本很高，因此 SwiftUI 可以選擇從 background thread call 此 closure。

![Screenshot 2025-11-04 at 14.59.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_14.59.17.png)

在介紹幾個從 background thread 執行 code 的 API

1. `Layout protocol` 可以在 main thread 以外的地方，呼叫他的 requirement method
2. 與 `visualEffect` 類似， `onGeometryChange` 的第一個參數是一個 closure，它也可以從 background thread 上呼叫執行

![Screenshot 2025-11-04 at 15.11.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.11.08.png)

這種在 runtime 優化 SwiftUI 透過 background thread 執行 code 的事情，可以透過 `Sendable` 向 compiler 和開發者表達，這種 runtime 行為 (語義 semantics）

在 main thread 的其他地方上執行 code，可以解緩 main thread 的壓力，讓 app 更流暢，而 `Sendable` 關鍵字用於提醒開發者當需要從 `@MainActor` 共享數據時，可能存在 data-race conditions。

可以把 `Sendable` 想像成懸崖邊小路上的警告標誌，上面「寫著危險！別在這奔跑」，但別緊張，其實 Swift 會發現 code 中任何潛在的 race conditions ，會透過 compiler error 提醒開發者。

![Screenshot 2025-11-04 at 15.18.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.18.30.png)

避免 data-race conditions 的最佳策略就是，不在 concurrency tasks 之間共享數據，當 SwiftUI API 要求開發者注釋 Sendable function 時，這 framework 會將所需的大部分 variables 作為 function 的參數，這個有個簡單範例

在 `ColorExtactorView` 裡的 `EqutalWidthVStackView` ，他是一個 custom layout，但我們不用關注如何 layout，重點在能夠使用 SwiftUI 傳入的參數 (`subviews: SubViews`)，完成所有複雜計算，而不接觸任何外部變數

![Screenshot 2025-11-04 at 15.27.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.27.15.png)

但假如真的需要 access 一些 variables 是在 sendable function 之外的，該怎麼辦？例如在 SchemeContentView 中，需要此 visualEffect 中的 pulse 狀態，但這時 Swift 就提醒存在 data-race condition

![Screenshot 2025-11-04 at 15.31.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.31.55.png)

我們來看看這 compiler error 怎麼辦

首先 pulse variables 是 self.pulse 的縮寫，這是在 Sendable closure 中共享 `@MainActor` isolated variables 的常見情境。

![Screen-Recording-2025-11-04-at-15.38.36.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen-Recording-2025-11-04-at-15.38.36.gif)

此時的 self 是 View，他在 Main actor，但我們最終目標是把它傳到 Sendable closure ，而他在 Background thread，處裡方法有2種：

1. 在 Swift 中，將 self variable 傳送到 background thread，這要求 self 的 type 為 Sendable
2. 除非 pulse property 並不在限定的任何 Actor 上

![Screenshot 2025-11-04 at 15.45.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.45.58.png)

但仔細看我們的 code，就知道 self 是一個 View，他被 `@MainActor` 保護，因此 compiler 會認為他是 Sendable type，這樣 Swift 可以妥善處理這種情況，對 self 的引用，從 `@MainActor` isolation 跨越到了 Sendable closure 是沒問題的。

![Screenshot 2025-11-04 at 15.48.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.48.35.png)

所以實際上 Swift 警告的是，嘗試 access pulse property 的行為，但我們知道作為 View 的 member，pulse 會被 `@MainActor` isolated，因此 compiler 表示，即使可以將 self 傳遞到那，但是 access 由 `@MainActor` isolated 的 property 是不安全的。

![Screenshot 2025-11-04 at 15.52.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_15.52.35.png)

要處理這問題，有幾種方式

![Screenshot 2025-11-04 at 16.17.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.17.50.png)

**Avoid accessing isolated properties**

可以避免透過 View 來引用讀取這個 property，因為在處理 visual effect，不需 View 的所有 property，只需要知道 pulse，所以可以在 closure 中使用 capture list 拷貝 pulse variable ，這樣不需要將 self 發送到這個 closure 中，而且由於 pulse 是一個 Bool 是一個 value type 且是 Sendable 的，這方法很適合，最後這 copy 就只存在在這個 function 裡

![Screenshot 2025-11-04 at 16.12.13.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.12.13.png)

**Make the properties nonisolated**

假設無法在 Sendable closure 中 access pulse variable，因為他受 global actor 保護，另一種實現策略是，將所有讀取操作聲明為 `nonisolated` 。

## Code Camp

### SwiftUI’s action callbacks are synchronous

![Screenshot 2025-11-04 at 16.23.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.23.41.png)

有一點 SwiftUI 開發經驗的人，都會發現很多 UI 操作都是 synchronous 的，像是 Button，假如需要處理 asynchronous 需要使用 Task 切換 async context，但為什麼不要讓 Button action 直接變成 async closure？

原因如下

![Screenshot 2025-11-04 at 17.22.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.22.27.png)

### **Synchronous UI update prior to asynchronous update provides better app experience**

同步更新是良好的用戶體驗，如果 App 包含長時間的執行任務，且用戶需要等待結果，體驗就不好，所以耗時的任務應該加上一些動畫，顯示這耗時的過程，例如下面的 code，點擊後 `model.isExtracting` 會被切換，就會開啟動畫

![Screenshot 2025-11-04 at 16.30.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.30.00.png)

### **Async functions require extra care when working with animations**

像下面這案例，在往下 scroll 時，歷史紀錄上有一個圖表，他也有動畫

![Screen Recording 2025-11-03 at 18.11.49-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen_Recording_2025-11-03_at_18.11.49-2.gif)

此效果可以透過 `.onScrollVisibilityChange(threshold:)` 來處理

![Screenshot 2025-11-04 at 16.33.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.33.54.png)

作為 UI framework，為了 every frame 都能打造流暢的 interactions，SwiftUI 需面對設備要求特定 screen refresh rate，當希望 code 對 scroll 等連續手勢做出反應時，這是重要的背景知識，讓我們看看這範例

透過綠色標記 onScrollVisibilityChange 開始時間，而藍色標記作為狀態變動觸發動畫的時間

![Screenshot 2025-11-04 at 16.38.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.38.19.png)

這種變動是否與手勢 callback 在同一幀發生，會在視覺上產生很大的差異

假設想再動畫動之前，加上一些 async work，這邊用橘色標記 async work 開始的時刻，而在 Swift 中 await function 會建立一個 suspension point。

![Screenshot 2025-11-04 at 16.40.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_16.40.57.png)

![Screen-Recording-2025-11-04-at-16.50.43.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screen-Recording-2025-11-04-at-16.50.43.gif)

依這個範例來看，Task 可以接收 async function 作為參數，當 compiler 看見 await 時，會將 async function 分成兩個部分，執行第一部分後，Swift runtime 可以暫停這個 function 2，並在 CPU 上執行一些其他工作，這可以持續任意時間，然後 runtime 恢復原始 async function 時，會執行後半段

![Screenshot 2025-11-04 at 17.07.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.07.06.png)

回到我們原本的例子，我們其中用的 `await someConcurrentWork()` ，這種暫停可能意味著我們的 task closure 要很久才能 resume，這可能就超過設備規定的刷新截止時間。

對用戶來說這意味著，動畫看起來不順暢也不同步，因此 async function 中的變動，不是好方法。

SwiftUI 默認是 synchronous callback，這有助於避免 async code 的意外 suspension，在 synchronous action closure 中更新 UI 才是好方法，當然 code 裏面始終可以選擇使用 Task 來選擇加入 asynchronous context。

![Screenshot 2025-11-04 at 17.25.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.25.28.png)

對於時間敏感的邏輯，例如動畫，需要 SwiftUI 的輸入和輸出是同步的，可觀察 properties 的 synchronous 變化和 synchronous callbacks，這是與 framework interaction 的最自然方式。

好的用戶體驗不一定要大量 custom concurrent logic，synchronous code 也是許多 app 絕佳的起點和端點。

![Screenshot 2025-11-04 at 17.36.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.36.46.png)

另一方面，如果 app 會執行大量 concurrency work，嘗試找到 UI code 和非 UI code 之間的邊界，最好將 async work 的 logic 與 View logic 分離，最好可以使用一段 state (ColorExtractorModel)，這就像是 MVVM 架構裡的 ViewModel 一樣（但是 Apple 在這沒有提及 MVVM），它作為橋樑，這個 state 將 UI code 與 async code 分開，他可以啟動 async task，然後當一些 async work 完成，也可以對 state 執行 synchronous 變動，以便 UI 可以對這個變更做出反應並更新畫面，這樣的 UI 邏輯大部分是 synchronous 的。

![Screenshot 2025-11-04 at 17.39.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.39.14.png)

而且這樣做會發現 async code 更好寫測試，因為他現在獨立於 UI 邏輯

![Screenshot 2025-11-04 at 17.48.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.48.37.png)

View 依然可以使用 Task 來切換 async context，但盡量讓這個 async context code 單純簡單，它的作用是將 UI event 通知給 model。

找到需要耗時的 UI code 與長時間執行的 async code 之間的邊界，是改善 app structure 的好方法，這可以幫助 View 抱持 synchronous and responsive。

## Next steps

![Screenshot 2025-11-04 at 17.54.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Explore%20concurrency%20in%20SwiftUI/Screenshot_2025-11-04_at_17.54.56.png)

良好的組織非 UI code 也很重要

- Explore Swift 6.2’s default actor isolation setting，可以嘗試一下在現有的 app，可以刪除大多數 `@MainActor` annotations。
- Learn more about Mutex and adopt Sendable to your model objects，Mutex 是史 class 變成 Sendable 的重要工具，可以查看官方文件，看具體該怎麼做。
- Identify UI and non-UI code boundaries，挑戰自己，為 app 中的 async code 寫一些 unit tests，可以看看是否在不 import SwiftUI 的情況下做到這點。

## Resources

https://developer.apple.com/videos/play/wwdc2025/266