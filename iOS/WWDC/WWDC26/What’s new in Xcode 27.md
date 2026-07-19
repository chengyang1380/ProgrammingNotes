# What’s new in Xcode 27

![**Queenstown Hill Walking Track — New Zealand**](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/IMG_8564.jpg)

**Queenstown Hill Walking Track — New Zealand**

Xcode 27 能夠輕鬆用 Coding Agents 完成任務，且可以快速迭代新的專案構想，另外在 workspace 比以往更多 customizable

- Workspace
全新的 workspace appearance 以及如何 customized
- Projects and files
要啟動新專案如此簡單，尤其靈感湧現時
- Intelligence
深入探討精彩的全新更新，關於在 editor 使用 coding agent
- Running apps
介紹 Device Hub 如何在 device 和 simulator 上評估 app
- Refining apps
如何在 app 上線後持續精進體驗

# Workspace

這是 Xcode 27 全新 workspace 的標準外觀

![Screenshot 2026-07-11 at 21.35.09.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.35.09.png)

## Toolbar

部分原本位於 jump bar, history navigation 以及 editor controls 的相關 controls，都移進 Toolbar 裡

![Screenshot 2026-07-11 at 21.37.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.37.36.png)

Activity informations, build progress 出現在 window title 下方

![Screenshot 2026-07-11 at 21.40.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.40.34.png)

在中央有全新的進入點，用於 coding agent 互動，晚點會再詳細介紹，另外其中還有我們熟悉的 scheme 以及 destination picker

![Screenshot 2026-07-11 at 21.41.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.41.28.png)

右上角有用於新增分頁和編輯器窗格的 controls 以及自訂 editor’s setting 的選項，還有三項選擇用於切換 editor modes

![Screenshot 2026-07-11 at 21.44.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.44.43.png)

第一個選項會在 canvas 顯示 previews & playgrounds

![Screenshot 2026-07-11 at 21.47.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.47.33.png)

第二個選項會在 Assistant Editor 中顯示相關內容

![Screenshot 2026-07-11 at 21.49.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.49.41.png)

最後一個則進入 review source control changes

![Screenshot 2026-07-11 at 21.50.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.50.30.png)

說到 source control，branch picker 移動到 bottom bar ，這樣更輕鬆地容納命名很長的 branch name

![Screenshot 2026-07-11 at 21.52.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.52.52.png)

上述都是 default 的樣式，我們新的 Toolbar 最棒的地方就是可以完全自訂義，可以新增或刪除任何項目或按照自己喜好重新排序

![Screenshot 2026-07-11 at 21.54.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_21.54.08.png)

## Themes

自訂 workspace 不只是 toolbar 而已，有了 Xcode 27 的全新 theme，可以從精美的預設主中選擇，或者用 slider 調整符合自己的 style。

在 Xcode 的 setting window 中有一項 Appearance，包含了設定 theme 所需的一切選項 

![Screenshot 2026-07-11 at 22.01.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.01.49.png)

例如這裡透過滑動 slider，把 foreground & background 的顏色調整了

![Screenshot 2026-07-11 at 22.03.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.03.37.png)

也可以換顏色 

![Screenshot 2026-07-11 at 22.05.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.05.02.png)

![Screenshot 2026-07-11 at 22.05.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.05.29.png)

或者直接使用其他 theme

![Screenshot 2026-07-11 at 22.06.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.06.05.png)

關於 Font 也可以調整

![Screenshot 2026-07-11 at 22.07.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.07.25.png)

而且還可以為特定的 workspace 使用不同的 theme，font 設定也會分別儲存，有時候要在多個專案 (workspace) 做切換，此時就可以換個 theme 來區分，也可以用心情來區分

![Screenshot 2026-07-11 at 22.09.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.09.55.png)

另外這些 theme 都有支援 dark mode

![Screenshot 2026-07-11 at 22.11.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.11.44.png)

## Issues

Warnings & errors 也重新設計以配合新的 theme，預測性或 ”即時” 問題有新的低調外觀，以減少輸入時的干擾，並與在 building 時獲得的 warnings & errors 有所區別

當我們在修改 code 時，Xcode 會自動預測問題，這些預測使用與 theme 融合的低調背景，讓我們注意力主要集中在輸入的內容上

![Screenshot 2026-07-11 at 22.16.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.16.59.png)

但我們開始 build 時，原本低調的預測將轉變成 build warnings & errors，帶有完整強度的顏色

![Screenshot 2026-07-11 at 22.18.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-11_at_22.18.36.png)

# Projects and files

Xcode 27 讓啟動新專案變得非常容易

## New Project

![Screenshot 2026-07-12 at 14.37.24.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_14.37.24.png)

![Screenshot 2026-07-12 at 14.37.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_14.37.39.png)

根據不同需求選擇

現在選擇後不用先命名，可以先開始寫 code，等後續要儲存再來想命名，跟以前的順序不一樣，以前要先設定一堆基本資訊，例如：專案名稱、是否需要本地儲存 CoreData, SwiftData 或測試的選擇 XCTests, Testing…等等。

![Screenshot 2026-07-12 at 14.39.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_14.39.17.png)

## New playground & Other files

現在收到某個單一檔案，就算他不屬於任何專案的，Xcode 27 依然可以在 Canvas 中顯示 playground results  和 UI previews！這讓彼此分享簡單的想法變得容易

![Screenshot 2026-07-12 at 14.43.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_14.43.11.png)

## Intelligence

假如之後想要把這個檔案加進專案，這時 Coding agents 可以幫上大忙

在 Xcode 27 中， coding agents 的功能有大幅增強，比以往更容易啟動，並掌控 parallel agent tasks 和對話。

對話紀錄現在移到 editor pane，讓我們可以與其他 editor 組合使用，搭配 tab 分頁、split 畫面或任何適合的工作流程方式，editor 也包含一個便捷的方式，來查看 agnet 做了哪些變更以及產生的任何成果

### Task

可以 toolbar 上的這顆按鈕來啟動一個新的對話或 coding agent task

![Screenshot 2026-07-12 at 15.04.47.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.04.47.png)

### Editor

對話以 editor 的形式呈現，可以搭配 tab、split 或任何想要的方式來組織 workspace

![Screenshot 2026-07-12 at 15.06.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.06.15.png)

當有什麼 idea 出現，可以先使用 slash plan - `/plan` 來啟動 plan tool，可以花些時間規劃好 agnet 所需考量的細節，然後 agent 會收集此計劃所需的必要情境，而不會先進行任何變更

![Screenshot 2026-07-12 at 15.08.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.08.39.png)

在他探索的同時，可以啟動 sub-agent 來 parallel 處理其他事情

![Screenshot 2026-07-12 at 15.15.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.15.39.png)

在這個情況下，需要提供一些輸入來說明如何解決問題，此時需要提供一些指引，讓他繼續朝著建立計劃的方向推進

![Screenshot 2026-07-12 at 15.15.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.15.50.png)

一但 plan 準備好，可以先閱讀過，給予回饋或者沒問題，就讓 agent 開始實作

![Screenshot 2026-07-12 at 15.18.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.18.00.png)

當 agent 開始作業時，他對所做的任何變更都會呈現出來

![Screenshot 2026-07-12 at 15.18.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.18.58.png)

### Artifacts

產生的任何 file, artifacts, or screenshot 都會顯示在這，這是一個很好的方式，讓我們看到 app 如何演進，當 agent 在 simulator 和 previews 中與 app 互動時

![Screenshot 2026-07-12 at 15.18.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.18.16.png)

當讓 agent 深入實作 plan 時，可以開啟 coding assistant sidebar 

![Screenshot 2026-07-12 at 15.24.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.24.01.png)

因為他有一份和 aganet 的對話以及可能同時進行中的 task list，這 sidebar 讓我們可以明瞭的查看各對話，並了解他們是否需要任何輸入或有未讀的訊息

![Screenshot 2026-07-12 at 15.25.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.25.46.png)

還有很多值得探討的內容，推薦看這個 session，全面了解 Xcode 27 中的 agent workflow

[Xcode ,agents, and you](https://developer.apple.com/videos/play/wwdc2026/259/) - WWDC26

# Running apps

## Device Hub

現在 plan 已實作完成，在 simulator 啟動 app 時，它會在 Device Hub 中以新視窗開啟，Device Hub 有很好的方式可以探索和評估 app，跨 simulator and physical devices

![Screenshot 2026-07-12 at 15.39.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.39.59.png)

## Setting

使用不同的輔助功能設定，來測試 app 是非常重要的

![Screenshot 2026-07-12 at 15.38.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.38.38.png)

例如我們可以加強對比度加上選擇調整文字大小，最後再加上 dark mode

![Screenshot 2026-07-12 at 15.39.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.39.26.png)

## Resize mode

現在就算是 iPhone app 也要考慮 iPhone Mirroring，在 macOS 27 中 iPhone Mirroring 可以隨意調整畫面大小，因此在新的調整大小模式下，測試 app 是個好主意

![ezgif-4bb754d595100a2c.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/ezgif-4bb754d595100a2c.gif)

這在使用 simulator 上幫助很大了，但最酷的是他也支援 physical devices，打開 sidebar，會看到 simulator 和 device 的 list

![Screenshot 2026-07-12 at 15.51.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.51.52.png)

此時可以去選擇其他 device，例如實體的 iPad，並且一樣可以在 Device Hub 中查看及控制它

![Screenshot 2026-07-12 at 15.52.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_15.52.49.png)

![Screen Recording 2026-07-12 at 15.46.29.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screen_Recording_2026-07-12_at_15.46.29.gif)

![Screenshot 2026-07-12 at 16.00.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.00.20.png)

Device Hub 非常強大，有許多絕佳的 workflow，例如處理 file, data container, evaluating app configurations 等等，可以查看更多細節

[Get the most out of Device Hub](https://developer.apple.com/videos/play/wwdc2026/260/) - WWDC26

# Refining apps

## Localization

Xcode 27 可以更快速的 localization，因為 agent 是一個大型語言模型，非常適合為 String 做翻譯

這邊我們將 agent 把專案設定 localization，這邊先從西班牙文開始 

![Screenshot 2026-07-12 at 16.09.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.09.14.png)

Agent 讀取我們的 code 後，確保 String 已經準備好進行 localization 的引用，並建包含每個 UI String 的 String Catalog，只需要幾輪對話，app 就做好翻譯了

![Screenshot 2026-07-12 at 16.10.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.10.43.png)

開啟 String Catalog，就可以發現 agent 如此高效的批次方式處理任務

![Screenshot 2026-07-12 at 16.10.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.10.52.png)

我們回到 chat，來查看 agent 的進度，可以發現已經完成了，agent 分析我們的 app，執行了 localization 所需的任何 code changes，建立了 String Catalog，將每個 UI String 翻譯成西班牙文

![Screenshot 2026-07-12 at 16.11.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.11.05.png)

- Xcode handle the setup
- Agent generates translations with Xcode’s guidance
當 agent 產生翻譯時，它使用你的 project 完整情境以及 Xcode 提供的語言特定風格指引

可以善用 String Catalog 進行專注的單個語言作業，審查了西班牙翻譯，效果看起來很好，所以現在又新增一個簡體中文

在 Xcode 27 的 String Catalog 中，選擇某種語言後，可以點擊「Generate Translations」按鈕，Agent 就會在 background 開始作業

![Screenshot 2026-07-12 at 16.18.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.18.00.png)

後續可以隨時查看進度，無論是在 agent 對話本身，還是透過 String Catalog 填入的狀況

![Screenshot 2026-07-12 at 16.19.31.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.19.31.png)

![Screenshot 2026-07-12 at 16.20.51.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.20.51.png)

讓母語人士審查我們的 app 對其他語言的支援，是透過 TestFlight 進行的絕佳方式！TestFlight 的 user 可以提供翻譯回饋

### Localization tips

- Ask agent to ensure strings are localizable
請 agent 確認 code 中現有的 string 已準備好 localization
- Start with one or two languages
最好一開始先從一兩種語言開始，這樣出狀況很容易發現
- Test the app and get feedback
並且一定要測試 app，即使我們不懂每種語言，以便及早發現版面配置不自然或文字截斷的問題

### Achieve fluency with localization

更多 localization 需要知道的請查看

[Translate your app using agents in Xcode](https://developer.apple.com/videos/play/wwdc2026/213/) - WWDC26

[Code-along: Explore localization with Xcode](https://developer.apple.com/videos/play/wwdc2025/225/) - WWDC25

## Organizer

在 Xcode 27 中，它的功能不只是收集報告，還能協助採取行動

在之前 Organizer 一直是了解 user 遇到什麼問題的地方，但看到問題是一回事，弄清楚且如何處理他又是另外一回事，他現在還提供一些建議方法來處理常見問題，例如 hangs

Xcode 27 為 Organizer 帶來四項新功能

- Insights overview
- New metrics for storage and hitches
- Improved Metric Goals
- Guided performance analysis

### Insights overview

重新設計的 Overview，這影響最大的問題優先顯示，可以立即專注重大問題點，overview 頁面將 diagnostics and metrics 放在一起

![Screenshot 2026-07-12 at 16.43.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.43.54.png)

頂部的 metrics chart 的峰值告訴有那些東西需要關注，下方的 diagnostics 顯示 app 的哪個位置開始查看，一個畫面，不用在兩個之間切換

### New metrics for storage and hitches

儲存空間和動畫卡頓的新 metrics，能標示舊 metrics 無法發現的問題

在 Xcode 27 中，有一個新的儲存空間 metrics，顯示我們的 app 資料佔用了多少空間，手機上的儲存空間由所有 app 共享，所以當一個 app 過度使用時，每個 app 都可能受到影響

![Screenshot 2026-07-12 at 16.49.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.49.02.png)

該 metrics 分解了 documents, data, and binary size，因為 binary size 會影響行動網路下載和 launch time 

另外 Organizer 可追蹤動畫效能，之前都是在 scrolling hitches 的情境下，但現在新的 hitches metrics 會在更多地方都會追蹤，不只 scrolling hitches 了，例如如何使用 Liquid Glass 以及 SwiftUI view，更新的 metrics 給了更完整的圖像，也包括舊版遺漏的 animations

![Screenshot 2026-07-12 at 16.49.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_16.49.28.png)

### Improved Metric Goals

在 Xcode 27 中，原先的功能 - app recommendations 已升級為 Metric Goals

- Builds upon app-based recommendations
像是在去年 Organizer 開始顯示 launch time 的 recommendations，而 Xcode 27 提供了更全面的 goal，供我們達成。
這些 goal 是可達成且實際的，基於技術和功能上相似性在你的 app 和其他 app 之間
- Expanded to new metrics
這些 goals 涵蓋更多 metrics，例如 hang rate, disk writes, battery 以及剛剛提到的 storage 和 hitches metrics
- More cross-app comparisons
這些 goal 是針對我們 app 校準的，與相似 app 進行比較，基於 app 實際的功能以及它所採用的技術，除此之外，比較還包括，我們 app 的自身歷史基準，這樣就可以看到是否在進步

Organizer 非常擅長提供影響 user 問題的相關資訊，但我們想知道如何修復這些問題，現在 Organizer 新功能可以獲得 guided performance analysis (引導式的效能分析)，並使用 coding agents 產生 recommendations 

### Guided performance analysis

在某次 regression 問題上花費的大量時間，都在弄清楚他怎麼發生的以及如何重現，這正是 Generate Recommendation 功能所解決的部分

![Screenshot 2026-07-12 at 17.22.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.22.45.png)

按下這顆按鈕 Generate Recommendations…，agent 會一起處理 diagnostic data，向任何 agent toll 一樣，都可以迭代，嘗試不同角度、嘗試不同修復方式，直到找到適合的方案

## Instruments

每個 app 可能都存在一些問題，有些立即可以發現，有些需要深入挖掘，就像 Organizer 之前顯示給我們的 animation hitch，此時我們可以透過 Instruments 來研究這 hitch 的根本問題，在 Xcode 27 中，找到答案所需的時間大幅縮短

當感到 app 很慢，第一個問題始終是：時間到底花在哪？

在 Xcode 27 的新功能  **Top Functions**

### Top Functions

可讓問題更快浮現上來，例如想嘗試重現 Organizer 標示的那個 hitch，從下圖的這個範例來看，能明顯看到從 list page 進 detail page 時，detail page 上方的 animation 不是很順暢

![ezgif-48106853439afd39.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/ezgif-48106853439afd39.gif)

此情況很適合使用 Instruments 來偵測狀況， 從分析圖來看， CPU 確實有大量活動，把那個時間範圍選起來

![Screenshot 2026-07-12 at 17.46.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.46.00.png)

然後可以按下 Top Functions button（上圖已經按了），就可以看到我們在 app 的哪些地方花費大量時間

![Screenshot 2026-07-12 at 17.47.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.47.52.png)

![Screenshot 2026-07-12 at 17.48.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.48.12.png)

Top Functions 非常適合找出因多次執行操作而產生的效能問題，在這個情況下，我們可以看到 animation piepline 中有大量的工作

![Screenshot 2026-07-12 at 17.50.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.50.33.png)

從上圖來看，排名消耗最大的 function 是 `paperPhysics(_:)`，這時可以回到 Xcode 查看這個 function

回到 code 中能發現這個 loop 有多次迭代，修改後 loop 次數就正常了

![Screenshot 2026-07-12 at 17.52.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.52.06.png)

Top Function 直接將 code 最消耗資源的部分顯示出來

我們再重新跑一次 instruments，可以看到，現在 Top Functions 中沒有顯示任何我們 app 的 function

![Screenshot 2026-07-12 at 17.58.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_17.58.19.png)

其中 Instruments 還有很多酷功能，例如如何比較效能執行紀錄，這樣才能知道 code 變更了，但實際上使改善還是惡化

![Screenshot 2026-07-12 at 18.02.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_18.02.14.png)

其他細節可參考

[Debug and profile agentic app experiences with Instruments](https://developer.apple.com/videos/play/wwdc2026/243/) - WWDC26

[Profile, fix, and verify: Improve app responsiveness with Instruments](https://developer.apple.com/videos/play/wwdc2026/268/) - WWDC26

## Xcode Cloud

每次的 fix 和每次的 new feature，都可能帶來破壞（例如寫出 bug or hang），這時 Xcode Cloud  可以發揮一些作用

Xcode Cloud 是一個 Continuous Integration and Delivery 的服務，它在 cloud 上 builds and tests，且 parallel 跨多個 device、Xcode 和 OS version

在 Xcode 27 中，使用 Xcode Cloud 比以爲更容易、更快速，這邊將 app 的 Unit 和 UI Tests，讓它們在 Xcode Cloud 中自動執行，當我們對 main or feature branch 進行變更時，每次執行都將是潛在 regressions 問題是否存在的良好指標

這邊我們點擊 Get Started…按鈕，開始設定

![Screenshot 2026-07-12 at 18.17.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_18.17.21.png)

App 和 Developer Team 看起都正確，所以點擊 Next button

![Screenshot 2026-07-12 at 18.18.13.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_18.18.13.png)

我們將 Xcode Cloud 連接到我們 remote source code repository，然後就完成了

![Screenshot 2026-07-12 at 18.18.59.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC26/What%E2%80%99s%20new%20in%20Xcode%2027/Screenshot_2026-07-12_at_18.18.59.png)

之後每次 commit 就會執行 build 和 test 的 workflow

Xcode Cloud 也能幫助將 app 發佈給 user，與 TestFlight 及 AppStore 無縫整合，Xcode Cloud 的功能遠不止上述的這樣，其他細節可參考

[Build, deliver, and automate with Xcode Cloud](https://developer.apple.com/videos/play/wwdc2026/261/) - WWDC26

[Extend your Xcode Cloud workflows](https://developer.apple.com/videos/play/wwdc2024/10200/) - WWDC24

# Next steps

- Download Xcode 27
- Release notes
- Session resources
- Other sessions

下載 Xcode 27 根據 workflow 自訂他，並探索新功能

# Resources

https://developer.apple.com/videos/play/wwdc2026/258/