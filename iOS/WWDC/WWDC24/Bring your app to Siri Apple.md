# Bring your app to Siri | Apple

![coverPhoto](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/New%20Zealand%20—%20Deer%20Park%20Heights%20Queenstown.jpeg?raw=true)

* https://developer.apple.com/videos/play/wwdc2024/10133/

* https://www.youtube.com/watch?v=Lb89T7ybCBE

## Introduction

### Integrate with Siri

透過 Siri 可以快速探索您的 App，而且是快速並且高效 

### Developing with Siri

![截圖 2024-06-21 10.30.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_10.30.16.png?raw=true)

- iOS 10 開始有 SiriKit，可以在您的 App 開發類似功能，例如播放音樂，發送訊息，使用 SiriKit domains 是最佳啟動此功能的方式。
- iOS 16 開始有 App Intents，一個新框架，可以將 App 與 Siri 、 Shortcuts（捷徑）、Spotlight 等整合，如果目前的 App 與現有的 SiriKit domains 不重疊。

其他細節可以去看 **WWDC24: Bring your app’s core features to users with App Intents | Apple**

* https://developer.apple.com/wwdc24/10210

* https://www.youtube.com/watch?v=REGJcedvheE

## What’s new

### What’s new with Siri

Conversational improvements: 

Siri 在3個關鍵方面有明顯突破

- A more natural voice (交談時更自然)
- Personal context and on-screen awareness (上下文更相關，更加個性化，而且可以理解目前手機螢幕上的內容)
- Richer language understanding (更豐富的語言理解，可以更自然的與 Siri 交談，就算你結結巴巴，斷斷續續，他也可以理解）

假如 App 已經使用了 `SiriKit`，那將會自動獲得這些改進。

Action capabilities

- Take new actions in and across apps (可以在 App 內和跨其他 App 執行更多操作)
- Built on top of App Intents framework (廣闊的 App 世界與 Apple Intelligence 連結起來)

所以現在有新的 API: `App Intent Domain` 

![截圖 2024-06-21 11.09.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_11.09.40.png?raw=true)

但目前可以嘗試的是 Mail 和 Photos，接下來的幾個月裡，才會推出更多內容

![截圖 2024-06-21 11.10.45.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_11.10.45.png?raw=true)

今年 Siri 在這 12 個 domain 中，有 100 多種不同操作的支援

![截圖 2024-06-21 11.11.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_11.11.51.png?raw=true)

如果 App 具有這些領域中任何一個所涵蓋的功能，那麼這些 API 就可以提供幫助，例如有一個照片 App，可以使用 `photos.setFilter` 的意圖，讓用戶能夠說，對我昨天為瑪麗拍攝的照片做某件事。

![截圖 2024-06-21 11.12.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_11.12.47.png?raw=true)

## Actions

### Building

schema(模式)是一個重載的術語

![截圖 2024-06-21 11.16.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_11.16.38.png?raw=true)

假如從字典來看，是一個類別的所有成員所共有的概念、一般或基本類型的形式。

但從 Sirr 的背景下是什麼？

### Assistant Schemas

**Introduction**

- Based on App Intents (前面提到的 Apple Intents 以及 Siri Domains 提供的新功能)
- Shapes optimized for models (這些模型經過訓練，可以預期具有特定形狀的意圖，這種型形狀就是 Apple 所說的模式 (Schemas))，Assistant Schemas 就是我們所說的 API
- You write `perfrom` (我們只需要編寫一個執行的方法，然後讓平台處理其餘的事情)

**Shapes**

今年，建立了100多種意圖見了模式(Schema)，例如建立照片或發送電子郵件， 各自定義了一組對於該意圖的所有採用者來說通用的輸入和輸出，這就是所謂的形狀(Shapes)。

![截圖 2024-06-21 14.07.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.07.02.png?raw=true)

在所有這些幾何圖形的中間，執行的方法，具有完全的創作自由，可以定義適合您的 App 的體驗。

**Apple Intelligence**

Life-cycle of a Siri request with Apple Intelligence

![截圖 2024-06-21 14.13.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.13.38.png?raw=true)

從用戶發起一個請求開始，會經過模型，這模型經過專門的訓練，能夠對模式(Schema)進行推理，使 Apple Intelligence 能夠根據使用者請求進行預測。

一旦選擇了適當的模式(Schema)，請求就會被路由到工具箱(Toolbox)。

此工具箱包含了裝置所有 App 的 AppIntents 集合，並依其架構分組，然後找到符合的模式(Schema)，我們能使模型能夠對其進行推理。

最後，透過呼叫 Appntent 來執行該操作，顯示結果，並傳回輸出。

**`@AssistantIntent`**

來看一個範例，這是一個建立相簿的 AppIntent，其中仔細看 `@AssistantIntent(schema: .photos.createAlbum)` ，其中 `.photos` 就是 domain (領域)，而 `.createAlbum` 就是 schema (模式)。

![截圖 2024-06-21 14.23.43.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.23.43.png?raw=true)



由於模式(Schema)的形狀(Shapes)在編譯時已知，因此不在需要為我的 AppIntent 提供額外的元資料，所以可以簡化成以下這樣 

![截圖 2024-06-21 14.26.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.26.40.png?raw=true)




符合模式(Schema)的意圖(Intent)更容易在程式碼中定義。

但有時候形狀(Shapes)並不是總是靈活的，以下是解決的方法

`@AssistantIntent`(助理意圖)可以使用可選參數進行擴充，這樣就可以賦予形狀(Shapes)一點靈活性。

![截圖 2024-06-21 14.30.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.30.31.png?raw=true)

AppIntents framework 不僅包含建制操作的意圖(Intent)，也包括用於 App 中建模概念的實體(Entity)，就下從此意圖(Intent)返回一個 `AlbumEntity` 一樣。

**`@AppEntities`**

此外還新增了一個新的 Marcos，用於向 Siri 公開 `AppEntities` ，就像意圖(Intent)一樣採用這種新類型，在 `AppEntity` 聲明中新增一行程式碼一樣簡單。

![截圖 2024-06-21 14.35.48.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.35.48.png?raw=true)

而 `@AssistantEntity` 也可以從預先定義的形狀(Shapes)中受益，從而產生更簡潔的 App 實體(Entity)實作。

![截圖 2024-06-21 14.37.15.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.37.15.png?raw=true)

就像 `@AssistantIntent` 一樣，如果需要 `@AssistantEntity` 也可以透過聲明新的可選屬性來遠遠超出其形狀(Shapes)，就像我的相簿實體(Entity)上的相簿顏色屬性一樣。

![截圖 2024-06-21 14.44.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.44.08.png?raw=true)

**`@AssistantEnum`**

最後還有 enum，向 Siri 公開 `AppEnum` 就像剛剛說的 `Entity` 和 `Intent` 一樣簡單。

![截圖 2024-06-21 14.45.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.45.49.png?raw=true)

將 `@AssistantEnum` 加入到聲明中，就完成了，但是 AssistantEnum 不會對 enum case 強加任何形狀(Shapes)，要自行在實作過程中充分表達。

![截圖 2024-06-21 14.49.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_14.49.55.png?raw=true)

### Xcode demo

可以在這下載 demo，記得要下載最新版的 Xcode 使用（Xcode 16 Beta）

https://developer.apple.com/documentation/AppIntents/making-your-app-s-functionality-available-to-siri

這是一個相簿 App for Mac

![截圖 2024-06-21 15.08.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.08.12.png?raw=true)

還可以使用，右上角的選單，使用收藏、分享等功能

![截圖 2024-06-21 15.08.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.08.46.png?raw=true)

App 內有兩種基本模型種類， Asset 和 Album 

Asset: 代表照片或影片，裡面有標題和建立日期，還有一個 `AssestEntity` 的計算屬性

![截圖 2024-06-21 15.12.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.12.12.png?raw=true)

我們來看他的定義

![截圖 2024-06-21 15.13.48.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.13.48.png?raw=true)

Entity 是 App 內容進行建模的好方法，當與 AppIntent 結合使用時，它們允許系統對您的 Entity 執行操作。

再來我們看看意圖(Intent) `OpentAssetIntent` 

![截圖 2024-06-21 15.15.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.15.51.png?raw=true)

像裡面就有 `navigation` 來負責導航 App，還有 `perform` ，所以當得知是要打開相簿時，就會透過 36 行的程式碼執行跳轉。

再來我們來改良一下這個 `OpenAssetIntent` ，首先新增 `@AssistantIntent(schema: .photos.openAsset)` 。

![截圖 2024-06-21 15.20.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.20.14.png?raw=true)

然後這時 build code 後，會出現一個 error。

![截圖 2024-06-21 15.25.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.25.31.png?raw=true)

當向 Siri 公開這個 Intent 時，還需要確保任何關連的 Entity 或 Enum 也被公開。

所以我們先回到 `AssetEntity` ，向它新增 `@AssistantEntity(schema: .photos.asset)` ，然後再build 一次，但這次回得到另外一個錯誤

![截圖 2024-06-21 15.29.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.29.18.png?raw=true)

Apple 的模型(Model)經過訓練，可以預測具有特定形狀(Shapes)的實體(Entity)，透過實體(Entity)符合模式(Schema)，Compiler 能夠執行額外的檢查，來驗證實體(Entity)的形狀(Shapes)。

目前的情況是缺少了 `hasSuggestedEdits` 的屬性，新增後 error 就沒了

![截圖 2024-06-21 15.31.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.31.59.png?raw=true)

現在的實體(Entity)與架構的形狀(Shapes)相匹配，善用 Compiler ，幫助現有的 AppIntent 符合 Schema。

再來在 `OpenAssetIntent` 的檔案內新增 `UpdateAssetIntent` 

![截圖 2024-06-21 15.34.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.34.59.png?raw=true)

**Assistant Schemas 小總結**

Compiler validation

- Xcode validates intents and entities ，使用 Assistant Mode 可以對現有 AppIntent 進行額外的建置時驗證，也可以確保架構實現與模型訓練所以依據的形狀(Shapes)相符。
- Use Xcode snippets to get started，展示了從頭開始建立這些片段

### Testing

**Assistant Schemas**

Shortcuts

- Automatically appear as actions in Shortcuts，與任何 AppIntent 一樣，只要符合架構的 AppIntent 就會自動顯示在 Shortcuts（捷徑）裡，所以透過 Shortcuts 這是一個好的測試方法。
- Available today

![截圖 2024-06-21 15.52.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.52.24.png?raw=true)

### Shortcuts demo

我們從剛剛 demo 中的 `OpenAssetIntent` 和 `UpdateAssetIntent` ，來透過 Shortcuts 進行測試 demo。

從右上角 + 的按鈕，開始新增，然後點擊 `AssistantSchema` 來過濾，

![截圖 2024-06-21 15.54.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.54.16.png?raw=true)

![截圖 2024-06-21 15.55.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.55.05.png?raw=true)

然後選 Open Photo ，新增完成後，回到首頁就可以進行測試了

![截圖 2024-06-21 15.55.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_15.55.27.png?raw=true)

## Personal context

### In-app search

Siri 可以安全且私密的方式搜尋和推理裝置上的所有可用資訊。

- Built on `ShowInAppSearchResultsIntent` ，讓系統可以直接利用 App 的搜尋功能，
- Navigates to your search results，Siri 會將使用者直接導航至您的搜尋結果。使用 AppIntent 以及 Super human(這是一個電子郵件App) 等電子郵件 App，將能夠讓用戶能夠說：在 Super human 上查找自行車並在其 App 中查看結果。

來看一個範例的 Code，他符合現有的 `ShowInAppSearchResultsIntent` 類型。

![截圖 2024-06-21 16.02.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_16.02.13.png?raw=true)

現在有一個新的 API，可用於 App 的內建搜尋功能與 Siri 整合，只要新增 `@AssistantIntent(schema: .system.search)` ，這種搜尋意圖(Intent)也可以受益於預先定義的形狀(Shapes)。而且可以實現更簡潔的 AppIntent 實作。

![截圖 2024-06-21 16.04.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_16.04.41.png?raw=true)

### Demo

如果要用 Siri 在自己的 App 中搜尋照片要怎麼做？現在有一個好用的 API 可用。

在這個 demo App 裡有一個搜尋功能，可以篩選照片

![截圖 2024-06-21 16.31.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_16.31.58.png?raw=true)

回到 Xcode 裡，已經有一個搜尋 Asset 的 AppIntent，透過它可以使用 `navigation.openSearch(with: criteria.term)`  

![截圖 2024-06-21 16.33.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_16.33.51.png?raw=true)

那假設我們使用系統操作的新 AppIntent Domain，就可以像 Siri 和 Apple Intelligence 公開相同的意圖(Intent)。

只要新增 `@AssistantIntent(schema: .system.search)` ，Siri 就能夠將使用者直接路由到搜尋結果頁面。

![截圖 2024-06-21 16.38.45.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Bring%20your%20app%20to%20Siri%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-21_16.38.45.png?raw=true)

### Semantic search

- Find and **understand** photos, files, and even your app’s content，Siri 獲得了進行語義搜尋的能力，例如想搜尋「寵物」，它不僅是尋找「寵物」這個詞，還會找到貓、狗。
- Take action on search results，一旦找到內容，它就可以直接對其採取行動。
- Available to your apps，可以使用 AppIntents 框架來定義實體(Entity)以提供此附加上下文。
- Adopt `IndexedEntity` ，新的 API，使 Siri 能夠搜尋 App 的內容，使資訊在語意索引中可用。

更多 `IndexEntity` 可參考，**WWDC24: What’s new in App Intents | Apple**

* https://developer.apple.com/videos/play/wwdc2024/10134/

* https://www.youtube.com/watch?v=wirvkabKh8k

## Wrap-up

透過 Apple Intelligence 讓 Siri 迎來新時代，能夠在自己的 App 內做一連串的事情

- Make your app capable, flexible, and intelligent with Siri
- Adopt SiriKit or App Intents，是將 App 與 Siri 整合的框架，如果現在您的 App 與現有的 SiriKit domains 不重疊，那麼 App Intents 是適合您的框架。
- Use Assistant Schemas to integrate your app with Apple Intelligence，Siri 可以在您的 App 內採取行動（動作，例如在 App 內做某一件事），善用新的 Assistant Schemas API，能讓 App 從中受益

其他精彩影片：

**WWDC24: Bring your app’s core features to users with App Intents | Apple**

* https://developer.apple.com/wwdc24/10210

* https://www.youtube.com/watch?v=REGJcedvheE