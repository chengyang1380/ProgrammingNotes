# Meet the Foundation Models framework

![On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/DJI_0021.jpg)

On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.

## Introduction

![Screenshot 2025-07-24 at 10.38.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_10.38.14.png)

這是可以跑在設備端的大語言模型，這 framework 支援 macOS, iOS, iPadOS 和 visionOS。

可以藉助這個 framework 來增強 app 中現有的功能，例如提供個性化搜尋建議

![Screen Recording 2025-07-24 at 10.40.46-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screen_Recording_2025-07-24_at_10.40.46-1.gif)

也可以開發新功能，例如在旅遊 app 中生成行程規劃

![Screen Recording 2025-07-24 at 10.40.46-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screen_Recording_2025-07-24_at_10.40.46-2.gif)

甚至可以拿來生成遊戲角色的對話

![Screen Recording 2025-07-24 at 10.40.46-3.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screen_Recording_2025-07-24_at_10.40.46-3.gif)

這個 framework 針對內容生成、文章摘要、用戶輸入分析等場景有深度的優化

![Screenshot 2025-07-24 at 10.47.23.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_10.47.23.png)

而且這些都是在本地端執行，此模型的所有輸入輸出資料全程保持隱私安全，所以他可以不用連接網路，而且他是內建於系統層，App 的大小不會被影響

![Screenshot 2025-07-24 at 10.49.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_10.49.17.png)

## The model

要快速掌握模型使用及提示技巧，最佳方式就是使用 Xcode 26 的新功能 Playground，可以測試多種 prompt 以尋找最佳方案

使用上非常直覺

![Screenshot 2025-07-24 at 11.14.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.14.52.png)

也可以使用 for-loop 的技巧，連續發問

![Screenshot 2025-07-24 at 11.17.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.17.26.png)

上面 demo 用的 on-device model，是一個擁有30億參數的大型語言模型，且每個參數均採用 2bit 量化

### Capabilities

但也要記得我們只是 on-device，這終究只是一個設備級的模型，這模型針對摘要生成、訊息抽取、文章分類等場景進行專項優化，而這個模型並非通用知識問答或複雜邏輯推理而設計的，那類型任務就需要依賴服務器級的大語言模型才能完成了

![Screenshot 2025-07-24 at 11.25.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.25.11.png)

### Considerations

設備級模型需要將任務拆解為更小的子任務單元才能處理

![Screen-Recording-2025-07-24-at-11.32.08.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screen-Recording-2025-07-24-at-11.32.08.gif)

針對常見場景，例如文章摘要、內容標注等，有專門的 adapters，可以充分釋放模型在特定領域的潛力

![Screenshot 2025-07-24 at 11.35.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.35.28.png)

## Guided generation

默認情況下，語言模型生成的輸出，是非結構化的自然語言，這類輸出雖然便於人類閱讀，卻難以呈現到 App 的視圖層
非結構化的意思就像，client 跟 server 的 API 互相溝通時，也希望 response 的內容是結構化的，像是資料結構用 JSON 格式，細節也會定義好其中欄位是 Array 之類的，然後某個欄位裡面會有什麼資料結構。

![Screenshot 2025-07-24 at 11.43.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.43.30.png)

一種常規解決方案是透過設定指示，要求模型生成易解析的結構化數據，例如 JSON 或 CSV

![Screenshot 2025-07-24 at 11.46.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.46.41.png)

但這種做法很快就會陷入反覆溝通的惡性循環，會不得不持續追加越發精準的指令，明確定義能執行與禁止執行的操作邊界

![Screenshot 2025-07-24 at 11.49.22.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.49.22.png)

這樣的方法往往不盡理想，最終不得不編寫臨時適配程式碼，來提取和修補內容，這方案並不可靠，因為模型本質是概率驅動的，一定會有機會出現結構性錯誤

![Screenshot 2025-07-24 at 11.42.23.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_11.42.23.png)

所以這個章節 Guided generation 就是要解決這個問題

這邊 Foundation Model Framework 提供兩個全新的 Macro `@Generable` 和 `@Guide` 

`@Generable` 可以生成特定 instance 的類型

`@Guide` 為 properties 提供自然語言描述

![Screenshot 2025-07-24 at 14.02.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.02.59.png)

定義好我們的結構，使用上如下，我們在 prompt 的部分，不需要再指定輸出格式，而且最關鍵的是可以獲得功能完備的 Swift object，這要讓我們在 map 到畫面上，就很容易

![Screenshot 2025-07-24 at 14.04.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.04.16.png)

Generable 支援的 type

- String
- Int
- Float
- Double
- Bool
- Array
- Generable type
- Recursive type
    
    ![Screenshot 2025-07-24 at 14.13.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.13.27.png)
    

這樣子在 UI 部分有強大的應用潛力

使用 Guided Generation 的優點

- Guarantees correct structure: 約束解碼的技術，從根本上保證了生成結果的結構正確性
- Simplifies prompts:  也可以讓 prompt 更簡潔，只需要專注描述預期行為，不用考慮輸出格式
- Bolsters accuracy: 提升模型的準確性
- Boosts speed: 加速推理過程

### Advanced guidance

- Constrained decoding
- Generation schemas
- Regex guides

還有一些進階用法，像是運行時創建動態模式，這部分細節可以參考另外一個 Session

[WWDC25 Deep dive into the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/301)

## Snapshot streaming

接下來要討論 streaming，而這一切都建立在熟悉的 `@generable` macro 基礎上

### Tokens

![Screenshot 2025-07-24 at 14.31.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.31.12.png)

假如使用過大語言模型，可能知道他們是以 `token` 作為單位生成文字，通常在 streaming 輸出時，token 會以所謂的 delta (增量形式逐塊)傳遞，但在 Foundation Models frameworks 上採用了不同的實現方式。

簡單說一下 `Token` ，它可以是一個詞、一個詞根、一個標點符號，甚至是一個字元。模型會將輸入的文字切分成這些小單元，進行理解和預測，然後再輸出成文字。
 `Delta Streaming` 的過程，就是我們平常使用 AI 的過程，想像正在看一個人打字，他不是打完一整篇文章後才給您看，而是每打完一個字或一個詞，就立即顯示在螢幕上。這個「每打完一個字或一個詞就顯示」的動作，就是「delta 傳遞」。 

**為什麼 Foundation Models frameworks 要用不同的方式實現？** 

![Screenshot 2025-07-24 at 14.48.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.48.26.png)

關鍵在他每次回覆都是一個 structure，而不是單一一個 delta，每次都回一個新的 structure，就要想辦法解析內容，更新到 UI 上，尤其遇到結構非常複雜的 structure，會變得很難實作，所以使用 Delta streaming 不適合用在 structured output。

那這也是為什麼需要 Snapshot streaming，當 Model 生成 delta 時，framework 也會轉換成 snapshot，所以會隨著 Model 逐步生成 response 內容

![Screenshot 2025-07-24 at 14.52.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.52.01.png)

假如把 `@Generable` macro 給展開來，會發現其中有實作了 `PartiallyGenerated` 的東西，本質上他是 structure 的鏡像，只不過所有 property 都變成 optional 的

![Screenshot 2025-07-24 at 14.57.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_14.57.56.png)

使用上就變成 `streamResponse` ，他是一個 async sequence，每次拿到的 element 就是更新的 snapshot

![Screenshot 2025-07-24 at 15.02.23.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_15.02.23.png)

這個做法與 SwiftUI 完美契合

![Screenshot 2025-07-24 at 15.06.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_15.06.33.png)

![Screenshot 2025-07-24 at 15.07.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_15.07.25.png)

![Screen-Recording-2025-07-24-at-15.04.07.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screen-Recording-2025-07-24-at-15.04.07.gif)

### Best practices

- Get creative with transitions: 透過 SwiftUI 動畫和過渡效果來巧妙遮掩延遲，將等待過程轉換成愉悅體驗
- Think carefully about view identity: 特別是在生成 Array 時，請仔細考慮 SwiftUI view identity 的問題
- Property order matters: 請記住屬性是按他們在 Swift struct 上聲明的順序生成的，這對動畫效果和模型輸出質量都有影響，例如 summary 應該要放在 struct 的結尾，這樣比較容易生成最佳結果
    
    ![Screenshot 2025-07-24 at 15.14.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_15.14.18.png)
    

還有其他細節，請參考 [WWDC25 Code-along: Bring on-device AI to your app using the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/259/)

## Tool calling

它能讓 Model 執行你在 App 中定義的 code，這些功能對於充分發揮模型潛力至關重要，因為提供模型許多擴充能力，我覺得可以把它想像成是 [Model Context Protocol (MCP)](https://modelcontextprotocol.io/introduction)，而且他具有 Swift type 的安全優勢，這也讓用戶可以透過自然語言控制 app 內功能，可能是「工具導向」時代的開始

- Identify information or actions needed: Model 可以自主識別需要額外訊息或操作的任務，在難以透過 code 決策時，它可以智能的選擇調用工具的時機和方式
- Access additional information: 提供 Model 的訊息可以是通用的知識，近期事件或個人數據，例如在旅遊 App 中，可以透過 MapKit 獲得位置訊息
- Ground responses: 這也使 Model 能夠引用事實數據，既能有效抑制幻覺生成，又支持對 Model 輸出進行事實核查
- Take actions: Model 可以執行 App 內操作和系統級操作

### How to calling works

現在 Model 要生成一個旅遊規劃

![Screenshot 2025-07-24 at 15.41.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_15.41.28.png)

一開始幫 Session 配置 tool，系統就會自動將這些 tool 及相關使用說明提供給 Model

接下來我們輸入目標地點的 prompt

此時模型就會評估是否需要 calling tool 來優化 response，Model 會發起一個或多個 tool calls

在這個範例 Model 主動發起兩個 tool calls，查詢餐廳和飯店

下個階段 FoundationModels framework 將自動執行你編寫的 tool code

再來將 tool output 結果插入 Session 紀錄

最後 Model 會整合 tool output 及歷史 Session 紀錄，生成 response

### 如何定義 tool?

![Screenshot 2025-07-24 at 16.01.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.01.56.png)

要將使用的 struct conform Tool protocol

這 protocol 要實作的有 `name: String` 及 `description: String` ，名稱和自然語言的描述，framework 就會自動提供這些訊息給 Model，幫住它理解何時應 call 我們的 tool

再來要定義 `func call(arguments:)` ，當 Model call 我們的 tool 時，將執行定義的 method。

而 argument 可以是任意 `Generable` type

### 為什麼 argument 必須是 generable ?

- Built on top of guided generation: 是因為 tool calling 的功能基於引導式生成 (guided generation)機制建構
- Never produces invalid tool calls: 這也確保了 Model 永遠不會產生無效的工具名稱或參數

![Screenshot 2025-07-24 at 16.06.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.06.56.png)

實作部分可以看到我們透過 `CLGeocoder` 及 `WeatherService` 來取得位置跟天氣

最後再透過 `ToolOuput` 類型呈現，它可以用 `GeneratedContent` 來創建結構化數據，也可以直接使用 String 生成，後者做法適合用於自然語言輸出的工具場景

![Screenshot 2025-07-24 at 16.10.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.10.19.png)

定義好了 Tool 接下來就是要確定 Model 能夠訪問它了

![Screenshot 2025-07-24 at 16.15.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.15.44.png)

所以要將 tool 傳入 session 的 initializer ，tool 必須在 session 初始化時就要綁定，並會在整個 session lifetimes 供 Model 使用，再來就很單純，像往常一樣向 Model 發送 prompt 即可，以上已示範如何在 compile time 定義安全的類型工具，對大多數情境都很適用

### Dynamic tools

但 tool 也可以實現 dynamic，例如可以透過動態生成模式，在 run time 時定義 tool 的參數及行為

- Runtime-defined behaviors
- Dynamic arguments schema
- Parameterized names and descriptions

其他細節可參考 [WWDC25 Deep dive into the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/301)

## Stateful session

Foundation Models framework 是圍繞在 stateful session 的概念構建的，默認情況下，當我們創建 session 時，系統會調用設備端通用 Model 進行處理，但其實我們可以根據需要提供自訂義指令

### Instructions

![Screenshot 2025-07-24 at 16.27.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.27.25.png)

- Role of the model: 這指令可以用於設定 Model 的角色身份
- Response style and length: 可以指定 response 風格和詳細程度等參數
- Optional: 需要注意的是自訂義 instruction 屬於 optional 的，若未指定，系統會採用合理的默認指令

### Instructions versus prompts

如果決定使用自訂 instructions，需明確 instructions 與 prompt 的關鍵區別

- Instructions should come from you: 這應該由開發者設定
- Prompts may come from you or your user: 而 prompt 還可以來自 user 輸入
- Instructions get priority: Model 優先遵守 instructions 而非 prompt，這特性雖然助於防範 prompt 的 injection attacks （注入攻擊）但也並非萬無一失
- Don’t allow untrusted content in instructions: 一般來說，instruction 是 static 靜態的，但切勿將不可信的用戶輸入直接插入 instructions

其他更多細節可參考 [WWDC25 Explore prompt design & safety for on-device foundation models](https://developer.apple.com/videos/play/wwdc2025/248/)

### Multi-turn interactions

Model 每次的交互，都會作為上下文使用，並保留在對話紀錄中，像下面的例子說 “do another one”，Model 是可以理解要做什麼，透過 `session.transcript` 可以查看過往紀錄，如果有需要的話，可以繪製成 UI View 展示這些內容給 User

![Screenshot 2025-07-24 at 16.50.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.50.37.png)

還有一點很重要，當 Model 正在生成輸出時，它的 `isResponding` property 會變成 true，可以觀察這個 property 並確保在 Model 完成 response 前，不要在提交新的 prompt

![Screenshot 2025-07-24 at 16.56.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_16.56.43.png)

可以設定 `SystemLanguageModel(useCase:)` 

![Screenshot 2025-07-24 at 17.04.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.04.53.png)

### Content tagging adapter

- Tag generation
- Entity extraction
- Topic detection

默認情況下，它被訓練用於輸出主題標籤，並直接集成了 guided generation，因此，只需要使用我們的 Generable macro 定義一個 struct，然後傳入用戶輸入即可從中提取主題

![Screenshot 2025-07-24 at 17.11.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.11.27.png)

而且還可以向他提供自訂義 instructions，和自訂 Generable output type，這樣可以用它來檢測動作、情感等內容

![Screenshot 2025-07-24 at 17.15.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.15.02.png)

### Availability

記得現在的 Model 只能 run 在有支援 Apple Intelligence 的裝置，要檢查是否可以用 `.availability` 即可

![Screenshot 2025-07-24 at 17.21.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.21.43.png)

### Error handling

- Guardrail violation: 違反安全護欄（例如有有害、非法、歧視性、仇恨言論、不道德的結果）
- Unsupported language: 語言尚未支援
- Content window exceeded: 超出上下文窗口限制

為了最佳用戶體驗，要記得妥善處理這些錯誤

該怎麼好好處理，細節可參考 [WWDC25 Deep dive into the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/301)

## Developer experience

使用全新的 `playground` macro 來呼叫 Model prompt 功能

![Screenshot 2025-07-24 at 17.29.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.29.37.png)

### Playground

- Iterate on a prompt quickly: 功能強大，無需 rebuild，即可快速 iterate prompt
- Access all types in your projects: 在 Playground 中，可以直接訪問專案中所有類型，包括已驅動 UI 的 generable type

### Instrumentation

- Important to understanding latency: 要了解底層延遲很重要，會影響 user 體驗
- LLMs are slower than smaller ML models: 因為與傳統的機器學習 Model 相比大語言模型的運行時間更長

明確了解延遲產生的環節，能夠幫助調整 prompt 的詳細程度或判斷何時調用一些實用 API 來幫忙，協助預載之類的

![Screenshot 2025-07-24 at 17.44.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.44.18.png)

而全新的 Instruments App 性能分析模板，正是為了實現這點所建構的，可以分析 Model request 的延遲，觀察優化空間，並量化改進效果

### Feedback Assistant

![Screenshot 2025-07-24 at 17.49.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.49.54.png)

- Report unexpected model responses
- Provide feedback for APIs

有遇到問題的話可以回饋給 Apple，讓他們改進 Model 和 API

對此 Apple 還提供了 encodable 的 feedback attachment data structure

![Screenshot 2025-07-24 at 17.50.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.50.45.png)

### Custom adapter

![Screenshot 2025-07-24 at 17.55.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.55.29.png)

假如有機器學習從業者，有高度專業化的使用場景和自訂 dataset，可以使用 Apple 的 adapter training toolkit，但請注意，這伴隨著重大責任，隨著 Apple 不斷優化 Model，需要定期重新訓練 adapter

更多詳情請參考 Developer Article [Get started with Foundation Models adapter training](https://developer.apple.com/apple-intelligence/foundation-models-adapter/)

現在已經對 Foundation Models Framework 有基本概念了

![Screenshot 2025-07-24 at 17.56.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Meet%20the%20Foundation%20Models%20framework/Screenshot_2025-07-24_at_17.56.07.png)

## Next steps

想要更深入了解，可參考這些 session 及 doc

- [WWDC25 Code-along: Bring on-device AI to your app using the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/259/)
- [WWDC25 Deep dive into the Foundation Models framework](https://developer.apple.com/videos/play/wwdc2025/301)
- [WWDC25 Explore prompt design & safety for on-device foundation models](https://developer.apple.com/videos/play/wwdc2025/248/)
- [Get started with Foundation Models adapter training](https://developer.apple.com/apple-intelligence/foundation-models-adapter/)

## Resources

- [Video](https://developer.apple.com/videos/play/wwdc2025/286)
- [Human Interface Guidelines: Generative AI](https://developer.apple.com/design/human-interface-guidelines/generative-ai)