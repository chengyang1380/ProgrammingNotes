# What’s new in Xcode 26

![On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/DJI_0033.jpg)

On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.

我們會先了解 Xcode 下載大小的優化和性能方面的提升，再來是探索 workspace 及 source editor 的改進，還有 AI coding 的功能，還有介紹新的 debugging 和效能功能，還有 building 的新動態，最後還有一些測試體驗的更新

## Optimizations

這幾年 Xcode 不斷瘦身下載包，今年成功瘦身 24%，有些工具可以自己手動選擇是否要下載，例如 Metal toolchain，或者 intel simlator。

另外還有優化 text input，最多可以將 complex expressions 延遲降低 50%

還大大優化了 Xcode 載入的效能，現在 workspace 提升了 40%，對於大型專案一定有感

## Workspace and editing

### intuitive editor tabs

![Screenshot 2025-09-12 at 14.36.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_14.36.50.png)

現在開啟專案後多了一個 Start Page

![Screenshot 2025-09-12 at 14.46.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_14.46.16.png)

令外還有固定 tab，讓他始終顯示，後續可以按照需要選擇設置單個 tab，或每個文件自成一個 tab 或者整理成一個 set。

### Multiple word search

無論對專案多熟，搜尋的功能至關重要，這次新版的 Xcode 推出全新搜尋模式「Multiple word search」（多詞搜尋），它使用搜尋引擎的技術，在 project 中查找滿足條件的詞語組合。

![Screenshot 2025-09-12 at 14.52.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_14.52.02.png)

這邊有個例子，是想要搜尋有關 clipped resizable images，這時 Xcode 就會按相關性進行排序

這些組合可以跨越多行 code，而且任意順序排序的搜尋詞都能找到

![Screenshot 2025-09-12 at 14.54.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_14.54.17.png)

### Coding by voice

輔助功能也有突破，現在可以語音控制來 coding，用自然發音的方式讀出來即可，他自己會知道空格應該在哪，expressions 是否應與符號（大小括號之類的）對應，還有 camel-cased 等等。

假如需要看 demo 的話，[影片](https://developer.apple.com/videos/play/wwdc2025/247)裡的 03:50 開始

### #Playground

![Screenshot 2025-09-12 at 15.16.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.16.11.png)

和 preview 一樣，可以直接在 document 中新增 playground 來用，而 code 的執行結果會自動顯示在對應的 canvas tab。

這邊有一個例子是，Landmarks app 中有一個 bug，導致地標在地圖中顯示的位置不正確，所以打算用 Playground debug 一下 landmark struct，看能不能找出問題。

![Screenshot 2025-09-12 at 15.19.23.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.19.23.png)

首先要先 `import Playgrounds` ，這樣就可以開始用 playground macro 了，在 macro 裡，我們把資料給顯示結構出來。

![Screen Recording 2025-09-12 at 15.22.55.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-12_at_15.22.55.gif)

有些 property types 旁還有 Quick Look 的 icon，這時可以點擊，仔細檢查 coordinates，這樣就很一目瞭然知道這座標是錯誤的

![Screenshot 2025-09-12 at 15.25.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.25.33.png)

還可以在 playground 其中宣告一個 region 可以快速瀏覽結果

![Screenshot 2025-09-12 at 15.45.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.45.37.png)

我們來想想問題在哪，從文件中載入地標時，我們使用 regular expression 來 parse string 中的位置座標，可能是這個 expression 有問題？

![Screenshot 2025-09-12 at 15.46.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.46.46.png)

我們可以再用另一個 Playground 調查一下，假如在同個 document 裡新增 playground 會多出一個 tab。

![Screenshot 2025-09-12 at 15.53.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_15.53.39.png)

這個 playground，要 debug 這個 regular expression 的 function。

某些 type，像是 regular expression 的匹配結果，會在 canvas 呈現自訂可視化效果，在這例子中可以發現 Xcode highlight 字串中匹配的範圍，仔細看紅色匡起來的地方，系統未能捕捉到這裡的減號，難怪會出 bug

![Screen Recording 2025-09-12 at 15.55.14.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-12_at_15.55.14.gif)

最後也很簡單，我們調整 `scanForFloatingPointNumbers()` ，隨著 code 的變更，仔細看 canvas 的部分也會即時更新。

![Screenshot 2025-09-12 at 16.00.40.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_16.00.40.png)

這種 macro 很適合幫助理解現有的 code 以及嘗試新想法，這個全新的 macro `#Playground` [已開源](https://forums.swift.org/t/playground-macro-and-swift-play-idea-for-code-exploration-in-swift/79435)

### Icon Composer

![Screenshot 2025-09-12 at 16.03.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_16.03.53.png)

這個全新的 icon 工具是 Xcode 26 隨附的全新 app，他可以幫忙建構設計精美、講究細緻、富有層次感的icon，而且讓 icon 完美適配不同平台和軟體版本

![Screen Recording 2025-09-12 at 16.06.16.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-12_at_16.06.16.gif)

在不同平台上 icon 多少有差異，而且細分還有不同 mode，會有不同外觀（例如 dark mode, tint mode），但現在可以直接借助 Icon Composer 單個文件中實現解決

其他更多 Icon Composer 的細節可參考

WWDC25 [Say hello to the new look of app icons](https://developer.apple.com/videos/play/wwdc2025/220/)

WWDC25 [Create icons with Icon Composer](https://developer.apple.com/videos/play/wwdc2025/361/)

[Creating your app icon using Icon Composer](https://developer.apple.com/documentation/Xcode/creating-your-app-icon-using-icon-composer)

### String Catalogs

app 支持更多語言是非常有幫助的，String Catalogs 讓 localization 變得更容易

![Screenshot 2025-09-12 at 17.56.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_17.56.59.png)

今年的一些新功能，可以幫助開發者和翻譯人員簡化流程，現在 localization 的 string 有 type-safe 的特性，現在會自動產生 swift symbol，在 auto-complete 會顯示建議

![Screenshot 2025-09-12 at 18.00.24.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.00.24.png)

為了輔助翻譯人員，String Catalogs 現在可以自動生成描述 String 上下文的 comment

Xcode 會在 project 中智能分析，看這 localization string 應用的位置和方式，然後利用 on-device model 來生成 comment

想進一步瞭解的話，可參考

WWDC25 [Code-along: Explore localization with Xcode](https://developer.apple.com/videos/play/wwdc2025/225/)

## Intelligence

![Screenshot 2025-09-12 at 18.05.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.05.28.png)

Xcode 現在可以使用 ChatGPT 等大語言模型了，由於 model 與 Xcode 深度整合，model 可以能更方便的閱讀我們的 code，解答具體的 project 問題，或協助修改 code

![Screenshot 2025-09-12 at 18.07.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.07.12.png)

現在還出了一個輕量級的工具 Coding Tools，以便為所選的 code 自動應用修改或詢問，下面我們來講一些範例

![Screenshot 2025-09-12 at 18.10.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.10.30.png)

例如這情境是專案不太熟悉，不確定某個 code 在哪實現功能，Xcode 會給 model 發送資料，這時 model 就會描述相關 source code 及用途

![Screenshot 2025-09-12 at 18.11.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.11.21.png)

在 Conversation 中點擊 info button，可以看到 Xcode 發送的資料

![Screenshot 2025-09-12 at 18.12.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.12.57.png)

Response 中還有包含 links 的按鈕，點擊後就會跳轉對應文件

![Screenshot 2025-09-12 at 18.15.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.15.05.png)

現在我們要為 app 新增功能，將地標新增評分功能，注意在 prompt 這邊用了 `@` ，代表希望 model 重點參考這 object 或 source code 或 document file 之類的，假設已經想好具體要怎麼修改，這樣子使用很有幫助

![Screenshot 2025-09-12 at 18.18.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.18.25.png)

也可以 attach file，以供 model 查詢參考，圖片也可以

![Screenshot 2025-09-12 at 18.26.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.26.14.png)

默認情況下，系統會自動提供 context 並修改 code，但也可以根據具體需求自主決定，這顆按鈕，控制是否允許 Xcode 在查詢中提供 project 相關訊息，例如假設問的是有關 Swift 的一般問題（與 project 的無關），就可以關閉 Project Context 功能，避免 project 太多訊息傳遞給 model，當然大多數最好還是打開這功能

![Screenshot 2025-09-12 at 18.28.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-12_at_18.28.14.png)

而這顆按鈕是代表，自動 apply 回覆中給出的 code，假如關閉這功能後，可以自行查看各項修改，最後自己決定是否要 apply 這變更

![Screen Recording 2025-09-22 at 17.39.07.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-22_at_17.39.07.gif)

在這可以查看 code 的修改摘要

接下來繼續對話，可以仔細看這次對話用了 **it**，但 Model 照樣明白什麼意思，因為接著對話會繼續保留先前的查詢及回覆的 context

![Screenshot 2025-09-22 at 17.45.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-22_at_17.45.07.png)

默認情況下，code 會自動 apply 修改，但在 apply 每個更改之前 Xcode 會保留 code 的 snapshot，這樣可以透過對話中的修改紀錄，輕鬆查看修改並執行返回操作

![Screen Recording 2025-09-22 at 17.50.33.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-22_at_17.50.33.gif)

剛剛上述做法，在修改中可能影響整個專案的 code，但想專注特定一部分的 code，可以在 source editor 中使用 Coding Tools

這邊想查看 Landmark 這個 model，想產一個假資料看看

首先到定義 struct Landmark 的地方，line 6，這時可以看到左邊出現一個 Coding Tools 的 icon，點擊它

![Screen Recording 2025-09-22 at 17.56.35-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-22_at_17.56.35-1.gif)

接著我們選 Generate a Playground，這時候他就會開始跑

![Screen Recording 2025-09-22 at 17.56.35-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-22_at_17.56.35-2.gif)

最後滑到底，會看見 Coding Tool 幫我們產的 code，在右邊也有 Canvas  

![Screen Recording 2025-09-22 at 17.56.35-3.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screen_Recording_2025-09-22_at_17.56.35-3.gif)

除了解釋或修改 code，現在 Xcode 也非常擅長解決 code 中存在的問題

例如，這邊要用 `ForEach(activities) { ...` ，但是 Activity 沒有 conform `Identifiable` ，現在 Xcode 多一個功能是 generate fix

![Screenshot 2025-09-22 at 18.14.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-22_at_18.14.36.png)

按下之後就會叫出 coding assistant，由於 Model 知道這個 type 的聲明，所以可以知道這個錯誤要去哪個檔案修正

![Screenshot 2025-09-23 at 10.26.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_10.26.15.png)

Xcode 還擅長處理 deprecate API 之類的問題，這些通常都是警告，不是立即的問題，但現在用有 AI 的 Xcode 可以快速處理

在 Xcode 中新增 Model 方法如下，最簡單的就是 ChatGPT 點擊幾下就可以登入使用，沒登入的情況下，會有請求的數量限制

![Screenshot 2025-09-23 at 10.36.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_10.36.55.png)

也可以選擇其他 provider，例如 Anthropic，除此之外還有另外一個 Locally Hosted 的選項可以選，就可以使用 Mac 或特定網路上的本地 Model 了，這要歸功於 Ollama 和 LM Studio 等工具

![Screenshot 2025-09-23 at 11.09.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_11.09.16.png)

這邊可以隨心所欲的加 provider，沒有數量限制

配置好一系列的 Model 後，在每次新對話就可以選擇所需的 Model 進行

![Screenshot 2025-09-23 at 11.14.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_11.14.16.png)

**額外說明**

在 Xcode 26.0 (17A321) 正式版，還多了一個 **Claude in Xcode** 的選項

另外目前 (2025/09/23) 還沒辦法在 Xcode 26 整合用 Copilot，還是需要透過第三方套件 **GitHub Copilot for Xcode，**[相關討論](https://github.com/github/CopilotForXcode/issues/355)

但 Gemini 可以，做法大概如下：

1. URL 設定 [https://generativelanguage.googleapis.com/v1beta/openai](https://generativelanguage.googleapis.com/v1beta/openai)
2. API Key 的部分，到 [ai studio](https://aistudio.google.com/apikey)，這邊 create API Key 即可

相關做法可參考[此篇文章](https://zottmann.org/2025/06/13/how-to-use-google-gemini.html)，比較舊的做法還需要用 Proxy Workaround 的方式，會比較麻煩，現在已經不用了

![Screenshot 2025-09-23 at 11.19.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_11.19.14.png)

## Debugging and performance

### Improved Swift Concurrency Debugging

![Screenshot 2025-09-23 at 11.44.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_11.44.03.png)

首先是在 Swift Concurrency async/await  的 debugging 更方便了，在看 Task 執行的 code 時，Xcode 能跟隨執行流程深入 asynchronous function 內部，即使需要切換 thread 也無妨

而且現在 debugger 的 UI 會顯示 Task ID

接下來細講畫面上的東西

![Screenshot 2025-09-23 at 14.02.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.02.46.png)

左側會顯示 backtrace，右側是 Program Counter 的註解

![Screenshot 2025-09-23 at 14.04.32.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.04.32.png)

而 variables view 直觀顯示 concurrency type 的內容，像是 Tasks, TaskGroups 還有 actor。

在這例子還可以看到 child tasks 的 property 還有 priority

Improved Swift Concurrency Debugging 的小總結

- Stepping into (or out of) an await call in the debugger can now follow a task onto a new thread
- New data formatter for Swift concurrency types

有關更多 Swift concurrency 的新功能可參考 [WWDC25 What’s new in Swift](https://developer.apple.com/videos/play/wwdc2025/245/)

### Usage descriptions

在寫 iOS app，有些功能需要先取得用戶同意，例如相機、位置，假如 code 沒有加向使用者請求權限的使用說明，會 crash

![Screenshot 2025-09-23 at 14.10.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.10.37.png)

但現在新的 Xcode 不僅可以理解這種情況，還能解釋缺少什麼，而且還能快速修正種問題，點擊 Add 後直接跳轉到 Signing & Capabilities 編輯地方進行修改

*Xcode 26 後權限說明可以在 Signing & Capabilities 修改，Xcode 會更新底層的 info plist*

![Screenshot 2025-09-23 at 14.13.51.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.13.51.png)

### Instruments

**Processor Trace** 

![Screenshot 2025-09-23 at 14.31.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.31.25.png)

以前主要使用 Time Profiler 和 CPU Profiler 來分析 CPU 使用情況，這種方法適合用來分析長時間運行的狀況

因為他們是基於採樣 (sample) 的分析，顧名思義就是說這款工具會定期對 CPU 進行採樣，同時希望採集的 call stack 樣本，能夠大致反映相關 CPU 的使用情況，但本質來講，採樣只能在一定程度上預估整體工作負載，因此他更適合用於長時間運行的工作負載分析，像下面的圖，不能捕捉到橘色的部分

![Screenshot 2025-09-23 at 14.24.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.24.37.png)

![Screenshot 2025-09-23 at 14.28.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.28.49.png)

而現在搭配 Apple silicon 的設備，能夠捕捉處理器跟蹤文件 (Processor Trace），這其中存儲著 CPU 所運行 code 的相關訊息，包括 code 執行的分支和跳轉到的指令

CPU 會將這些訊息傳輸到文件系統上的某個區塊，以便使用 Processor Trace 工具進行分析

這個處理器追蹤功能不會進行定期採樣，而是在所有執行的 thread 上捕捉有關 CPU 各個底層分支決策的訊息，而且執行的開銷微乎其微

![Screenshot 2025-09-23 at 14.36.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.36.01.png)

而這也讓 Processor Trace 的時間線能夠直觀呈現，執行工作流的完整訊息，傳統的 sampling profilers 可能會錯過採樣區間之外的關鍵 code path，能夠反映執行的每支分支以及呼叫的 function，也包括 compiler-generated code，像是 Swift 的 ARC memory management

![Screenshot 2025-09-23 at 14.38.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.38.33.png)

Processor Trace 小總結

Processor Trace 從根本上改變了 software performance 衡量的工作流程，以及極低的開銷來補抓到所有 thread 上每次 call function

- Lightweight, hardware-based branch trace
- Provides per-function time and cycle-based metrics
- Introduced with Xcode 16.3
- Available on M4 and iPhone 16

**CPU Counters**

![Screenshot 2025-09-23 at 14.44.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.44.05.png)

剛剛說的 Processor Trace 非常適合理解 code 執行及佔用 CPU 時間的具體過程，另外還有一個大幅更新的工具 CPU Counters，他可以幫你理解自己的 code，具體如何佔用 CPU 的

這款工具有助於進行 microarchitecture 優化，預設模式是將相關 Counters 歸類分組

![Screenshot 2025-09-23 at 14.47.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.47.43.png)

為了知道 CPU 如何處理你的 code，這邊提供了一種引導方法，例如 CPU Bottlenecks mode

![Screenshot 2025-09-23 at 14.50.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.50.21.png)

CPU Bottlenecks 可將 CPU 的持續指令處理過程分解為：有用的工作 (useful work) 或卡住的工作 (bottlenecked work) ，而卡住的工作又再細分三個原因

![Screenshot 2025-09-23 at 14.56.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.56.46.png)

1. Instruction Processing Bottleneck - CPU 必續等待執行單位 (execution units) 或 memory 變成可用狀態 
2. Instruction Delivery Bottleneck - CPU 應該來不及分發指令
3. Discarded Bottleneck - CPU 對之後的工作負載預測不準確，需要重回正軌

![Screenshot 2025-09-23 at 14.54.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_14.54.36.png)

除了 bottleneck 分析方法外，還有 Instruction Characteristics 和 Metrics mode 一種更傳統的 counters 方法，能夠給出資源消耗的絕對值，這樣可以直接對 branch, cache behavior 和 numerical operation 進行分析，從而集中調整關鍵 instruction sequences

![Screenshot 2025-09-23 at 15.03.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.03.00.png)

CPU Counters 還有詳細的文件，可以幫助了解 mode 和 counter 的含義

![Screenshot 2025-09-23 at 15.10.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.10.19.png)

要更了解 Processor Trace 和 CPU Counters 請看

[WWDC25 Optimize CPU performance with Instruments](https://developer.apple.com/videos/play/wwdc2025/308/)

![Screenshot 2025-09-23 at 15.10.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.10.44.png)

### SwiftUI performance

![Screenshot 2025-09-23 at 15.16.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.16.37.png)

Lists 的更新速度最快可以提升到 16 倍多，而且無需在 App 中進行任何修改

有時候 SwiftUI 的 View 會不停更新，為此 Xcode 26 提供新一代 SwiftUI 工具，能捕捉相關的詳細訊息，就可以更容易知道怎麼又更新 View 的

![Screenshot 2025-09-23 at 15.16.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.16.06.png)

這 timeline 可以知道 SwiftUI 什麼時候在 main thread 上執行，以及某個 View 什麼時候需要很長時間才能更新，從而可能導致 hitch 或 hang

![Screenshot 2025-09-23 at 15.21.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.21.10.png)

而 View Body Updates 的 summary 可以得知自己 App 中的每個 View 更新的次數

![Screenshot 2025-09-23 at 15.23.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_15.23.05.png)

如果某個 View 的更新次數遠遠多於預期的，可以打開 cause-and-effect graph

![Screenshot 2025-09-23 at 16.33.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.33.53.png)

就可查看背後原因

![Screenshot 2025-09-23 at 16.34.22.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.34.22.png)

要進一步瞭解 SwiftUI 的性能改進可參考

[WWDC25 What’s new in SwiftUI](https://developer.apple.com/videos/play/wwdc2025/256/)

有關如何使用 SwiftUI 工具可參考

[WWDC25 Optimize SwiftUI performance with Instruments](https://developer.apple.com/videos/play/wwdc2025/306/)

### Power Profiler

有時候 app 很耗電，我們需要需要找出原因，通常是要去找 CPU 消耗較大的地方，然後能夠重現問題，最後紀錄 power metrics，這時就要用到 Power Profiler

它記錄 power metrics，這些 metrics 還能視覺化呈現

![Screenshot 2025-09-23 at 16.40.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.40.12.png)

Power Profiler track 顯示系統功耗以及相關的設備散熱狀態和充電狀態，這些有助於識別異常的功耗峰值

![Screenshot 2025-09-23 at 16.41.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.41.35.png)

Process track 顯示了當前 app 對各種設備能源組件的影響，像是 CPU, GPU, 顯示和網路子組件

Power Profiler 支援兩種 tracing mode，有線和無線紀錄

有線紀錄，需要將運行 Instruments 的設備 (Mac) 與目標設備 (iPhone) 直接連接在一起

另外一個是被動的方式，無需有線連接設備，透過設備的 Developer Settings，啟動追蹤紀錄即可，隨後可以將追蹤紀錄導入到 Instruments 中進行分析

![Screenshot 2025-09-23 at 16.45.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.45.55.png)

要進一步瞭解的話，可參考

[WWDC25 Profile and optimize power usage in your app](https://developer.apple.com/videos/play/wwdc2025/226/)

### Organizer

Xcode Organizer 可以透過指標和診斷日誌，監控已上架 app 的功耗和效能影響

![Screenshot 2025-09-23 at 16.50.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.50.36.png)

Xcode 16 中的 Organizer 為寫入診斷資料進 disk 推出一項功能稱為 Trending Insights，這功能有助於突出顯示哪些問題的功耗和性能影響了 app

在 Xcode 26 更進一步，為 hang 和啟動診斷日誌 (launch diagnostics) 也增加了 Trending Insights 功能了 

![Screenshot 2025-09-23 at 16.57.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.57.50.png)

在 Insights 區塊可以看到過去五個版本的影響增長幅度，會繪製成表格，清晰易閱讀

![Screenshot 2025-09-23 at 16.58.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_16.58.18.png)

現在知道了優化效能的方向了，可以使用 URL 共享功能，與同事共享 diagnostic report 了

![Screenshot 2025-09-23 at 17.00.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.00.17.png)

在 Organizer 中，還有很多有關 Metrics 的功能

![Screenshot 2025-09-23 at 17.03.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.03.20.png)

其中 Launch Time 還提供了 Metric Recommendations 的功能，他會與其他相似 app 做比較，像圖片的例子，啟動時間的部分顯示 Recommendation: 425 ms

注意，目前只有 Launch Time 有這功能，未來會慢慢加其他的

![Screenshot 2025-09-23 at 17.05.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.05.44.png)

## Building

### Explicitly Built Modules for Swift

在 Xcode 16 中推出了 Explicitly Built Modules，可用於 C 和 Objective-C 的 code，對於 build code 及 debugging 有速度提升

而在 Xcode 26 Explicitly Built Modules 默認可用於 Swift code

![Screenshot 2025-09-23 at 17.24.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.24.41.png)

有了 Explicit Modules，Xcode 會把每個編譯單元的處理分為三個獨立的階段 

Scanning → Building modules → building source code

這種分離為構建系統提供了更多的控制，有助於優化 build pipeline pip

Explicit Modules 可以

- 提高 build 的效率及可靠性
- 提升 module 共享的精準度和確定性
- 加速 Swift code 的 debug 速度，因為 debugger 可以重複利用已建構的 module

更多細節可參考

[WWDC24 Demystify explicitly built modules](https://developer.apple.com/videos/play/wwdc2024/10171/)

### Swift Build

![Screenshot 2025-09-23 at 17.38.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.38.03.png)

今年 Apple 以開源項目的形式發佈了 Swift Build，這是一個強大且可以擴充的 build engine，廣泛應用於 Xcode, Swift Playground 以及 Apple 自有操作系統的內部 build 流程

還一直努力將 Swift Build 整合到 Swift Package Manager 中，從而在 Swift 開源工具鏈和 Xcode 中提供統一的 build 體驗

還新增對所有 Swift 生態兼容平台的支持，包括 Linux, Windows, Android 以及其他平台

![Screenshot 2025-09-23 at 17.47.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.47.55.png)

要在自己的 package 中 preview 這種新的實現方式，可以透過 Swift command line tool 的 build-system 選項

![Screenshot 2025-09-23 at 17.48.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.48.50.png)

這是 Github 首次為驅動 Xcode 和 Swift 生態的 build engine 提供實現方面的支持，要進一步瞭解 Swift Build 或參與相關開發，請查看 [repository on Github](https://github.com/swiftlang/swift-build)

### Enhanced Security

![Screenshot 2025-09-23 at 17.53.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.53.08.png)

App 的安全性很重要，Xcode 新增了 Enhanced Security 功能，能為自己的 App 與 Apple 原生 App 同等嚴密的安全保護措施，比如 pointer authentication (指針驗證)

啟動方式如下

先到 Signing & Capabilities，然後新增 Capability，篩選出 Enhanced Security

![Screenshot 2025-09-23 at 17.56.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.56.14.png)

![Screenshot 2025-09-23 at 17.57.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_17.57.35.png)

建議假如被攻擊的可能性較高，就要啟用此功能，更多細節請參考 Apple 開發者文件 [Enabling enhanced security for your app](https://developer.apple.com/documentation/xcode/enabling-enhanced-security-for-your-app#Overview)

## Testing

Xcode 的 UI 測試，今年進行了重大升級，UI 自動化錄製功能得到增強，推出全新的 code generation system

這邊的例子要對這畫面做 UI 測試，這是一個修改精選輯的畫面

![Screenshot 2025-09-23 at 18.03.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.03.44.png)

現在多了一顆錄製的按鈕，按下即可開始錄製，接下來就可以操作模擬器，他就會自動寫 code

![Screenshot 2025-09-23 at 18.04.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.04.19.png)

結束錄製一樣在顯示行數的地方，按一樣的按鈕

![Screenshot 2025-09-23 at 18.07.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.07.10.png)

最後在這個 test case 前面行數的地方，有個菱形 icon，點擊後就會開始 run test

此外 code 裡許多元素還顯示了多個選項的 icon

![Screenshot 2025-09-23 at 18.10.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.10.03.png)

點擊後，我們可以使用這些選項，微調測試中某個元素的標識 (identified) 方式，這對進行新的 UI 測試變得容易

![Screenshot 2025-09-23 at 18.10.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.10.16.png)

剛剛上述生成 code 的功能，已經整合到 test report 的自動化管理器中 (Automation Explorer)

這邊打開一個有 UI 測試失敗的測試報告

![Screenshot 2025-09-23 at 18.15.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.15.50.png)

他的錯誤在於想要點擊 Text Field，但未找到指定的 identifier，

![Screenshot 2025-09-23 at 18.16.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.16.38.png)

右側是包含完整的測試影片，如果需要，都可以即時重播整個測試

![Screenshot 2025-09-23 at 18.19.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.19.58.png)

時間軸上也可以明顯看到發生錯誤的時間

![Screenshot 2025-09-23 at 18.21.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.21.00.png)

接下來，來檢查一下 `Description` element，查看 element 的 details，會發現 identifier 是正確的，但類型是 Text View，而不是 test case 期望的 Text Field，這表示之前可能 code 真的是顯示 Text Field，但過來需要支援多行而改成 Text View

![Screenshot 2025-09-23 at 18.23.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.23.14.png)

特別有用的一點是，這個 element 的 identifier，正確 code 已經生成好了，我們可以直接 copy，用來更新測試進行修正

![Screenshot 2025-09-23 at 18.29.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.29.39.png)

UI testing workflow

Enhanced UI automation recording, with improved code generation

Additional device interactions on all Apple devices

- Including hardware keyboards, hardware button presses

更多細節可參考 

[WWDC25 Record, replay, and review: UI automation in Xcode](https://developer.apple.com/videos/play/wwdc2025/344/)

借助 measure API，UI 測試也非常適合用來衡量 UI 的靈敏度，Xcode 26 才新增的 API `XCTHitchMetric` ，以便在 App 測試階段及時發現 hitches，下面的例子是測試 App 的滾動動畫效能

![Screenshot 2025-09-23 at 18.34.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.34.28.png)

`XCTHitchMetric` 能夠報告多個 App hitch performance 的 metrics

![Screenshot 2025-09-23 at 18.36.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.36.57.png)

還有 Hitch Time Ratio 表示了 App 出現 hitches 的總時長，在這個測試衡量區間內的占比

![Screenshot 2025-09-23 at 18.37.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.37.34.png)

要進一步瞭解 hitches 可參考

[Tech Talks - Explore UI animation hitches and the render loop](https://developer.apple.com/videos/play/tech-talks/10855/)

要測試 code 以防效能下降，另外一種有效方式是 Runtime API 檢查，這些檢查功能位於 Test Plan configuration editor，借助 Thread Performance Checker，測試現在能夠在 framework runtime 時顯示 issue，還突出顯示 thread 的問題

![Screenshot 2025-09-23 at 18.40.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.40.49.png)

Thread Performance Checker 可以檢測 thread 的問題，比如 priority inversions (倒置) 或 main thread 上非 UI 任務的問題，下圖為例，Thread Performance Checker 告訴我們不應該在 main thread 上 call 這 API，否則可能造成 App unresponsiveness

![Screenshot 2025-09-23 at 18.44.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.44.59.png)

Test Plan 也可以允許選擇，如果 app 在 runtime API check 發現問題時，是否要將測試標為失敗

![Screenshot 2025-09-23 at 18.46.47.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/What’s%20new%20in%20Xcode%2026/Screenshot_2025-09-23_at_18.46.47.png)

## Resources

https://developer.apple.com/videos/play/wwdc2025/247