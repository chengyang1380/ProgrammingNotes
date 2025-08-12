# Analyze hangs with Instruments

![On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/DJI_0032.jpg)

On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.

### Prerequisites

建議先看過 WWDC 19 的 [Getting Started with Instruments](https://developer.apple.com/videos/play/wwdc2019/411/)

處理 hang 有三步驟

![Screenshot 2025-08-07 at 10.11.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.11.44.png)

這 session 重點在 Analyze 及 Fix，關於 Find 可以查看另外一個 session [WWDC22 Track down hangs with Xcode and on-device detection](https://developer.apple.com/videos/play/wwdc2022/10082/)，這 session 有關於查找 hang 的所有工具，包括 Instruments，或 device 端的 hang detection (can enable in iOS Developer settings)，和 Xcode Organizer

![Screenshot 2025-08-07 at 10.16.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.16.54.png)

## What is a hang?

這邊用一個電燈的例子，來敘述什麼是 hang，當電燈泡插線之後，應該要馬上亮起來，拔掉之後就要馬上暗下來

![Screen Recording 2025-08-07 at 10.21.20-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-07_at_10.21.20-1.gif)

![Screen Recording 2025-08-07 at 10.21.20-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-07_at_10.21.20-2.gif)

但假如有 delay 的話會像下面這樣

![Screen Recording 2025-08-07 at 10.21.20-3.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-07_at_10.21.20-3.gif)

這個延遲可能只有 500ms，但我們一定很好奇這燈泡盒子發生什麼事，燈不能即時開關，很怪

![Screenshot 2025-08-07 at 10.39.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.39.07.png)

但有些情況 500ms 的延遲是無傷大雅的，例如兩個人在對話時，而燈泡這例子是對真實物體操作，直覺是要立即有反應

![Screenshot 2025-08-07 at 10.40.31.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.40.31.png)

![Screenshot 2025-08-07 at 10.41.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.41.43.png)

delay 大多數在 100ms 是很難察覺的，基本上 250ms 以內是可以接受的 ，但超過 250ms 就不太 ok 了，因此在 Apple 提供的工具裡 default 是 250ms 超過就會收到 hang report，超過 500ms 就是 hang 了

![Screenshot 2025-08-07 at 10.49.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.49.35.png)

但記得有些東西需要感覺 instant，就要 < 100ms，而有些東西 Request - Response 不用就在 < 500ms 也可以

![Screenshot 2025-08-07 at 10.52.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.52.03.png)

有些情境是會兩種狀況都有的，像是在 mail 裡，點擊 send button 是 instant 的，而畫面 dismiss 是 request-response

![Screenshot 2025-08-07 at 10.53.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.53.44.png)

所以 UI 類的東西，像是 tap button，盡可能在 < 100ms

## Event handling and rendering loop

我們需要保持 main thread 的工作量，避免給他非 UI 工作的 task

![Screenshot 2025-08-07 at 10.59.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_10.59.38.png)

[Improving app responsiveness](https://developer.apple.com/documentation/xcode/improving-app-responsiveness)

我們更新 UI 都需要在 main thread  花費一些時間，像下圖這樣，假設給 main thread 任務量太大，或當 event 要進入時 main thread 還有其他 task 在執行，都會造成 hang

![Screen Recording 2025-08-07 at 11.05.39.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-07_at_11.05.39.gif)

所以理想情況下 main thread 的任何任務都不該超過 100ms

![Screenshot 2025-08-07 at 11.13.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_11.13.20.png)

其他細節可參考：

[Explore UI animation hitches and the render loop](https://developer.apple.com/videos/play/tech-talks/10855/)

[Improving app responsiveness](https://developer.apple.com/documentation/xcode/improving-app-responsiveness)

## Busy main thread hang

首先我們在 Xcode 中使用 Profile，快捷鍵 `command + i` ，開啟 Instruments 後，選擇 Time Profiler， 當還不確定要怎麼做，但想更好了解 app 正在做什麼，Time Profiler 是個好的起點

之後我們就建立了一個新的 Instruments document，仔細看左邊就會看到除了 Time Profiler，還有 Hangs tool，這對我們分析很有幫助

![Screenshot 2025-08-07 at 11.22.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_11.22.53.png)

再來就可以點擊 record button 開始錄製，這時 app 就會開啟了

這邊的情境是點了一顆按鈕後，整個畫面卡住，過了好幾秒有個 sheet present 才恢復正常 UI

![Screen Recording 2025-08-07 at 11.25.03.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-07_at_11.25.03.gif)

操作完後，回到 Instruments 按下暫停錄製，並且仔細看這些資料，會看到 Severe Hang (3.44 s)，這符合我們剛剛用 app 的情況，按下按鈕後等了超久 UI 才正常

![Screenshot 2025-08-07 at 11.31.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_11.31.17.png)

### Two types of hangs

主要分兩種類型會造成 main thread hang

1. 真的把 main thread 操爆，CPU 的部分就會很明顯有大量活動
2. main thread 被 block，可能，可能是被堵住，這時的 CPU 的部分不會有什麼活動

![Screenshot 2025-08-07 at 13.49.47.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_13.49.47.png)

回到 Instruments，選到 main thread 的部分，我們關注 hang 的部分，所以在他的區塊右鍵點擊 Set Inspection Range and Zoom

![Screenshot 2025-08-07 at 13.53.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_13.53.38.png)

仔細看 main thread 上的 CPU 狀況，很明顯使用率都在 60-90% 之間，這情況就是 busy main thread

![Screenshot 2025-08-07 at 13.55.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_13.55.21.png)

接下來好好查看 CPU 在忙什麼，可以先透過右下角的 Heaviest Stack Trace，點擊了其中一項，detail view 的 tree view 就會更新了，在 heaviest stack trace default 會隱藏非來自 source code 的後續 function 呼叫，這是因為要更方便查看 source code 所涉及的範圍

![Screenshot 2025-08-07 at 14.20.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_14.20.08.png)

可以將 Call Tree 打開，複選 Hide System Libraries，這樣 detail view 就會更單純了，這樣就過濾掉 system libraries 的所有 function，我們更專注於自己的 code

![Screen-Recording-2025-08-07-at-14.25.12.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen-Recording-2025-08-07-at-14.25.12.gif)

接著要再更深入找出原因，會有兩個可能

1. 這 function 就是如此花費 CPU 時間？
2. 但也有可能這 function 一直被呼叫

這會影響該如何減少 main thread 上的 work

![Screenshot 2025-08-07 at 14.29.32.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_14.29.32.png)

下面這樣是一個典型的 call stack 結構，由 main function 到 UI framework，然後到 `something()` 後執行我們的程式碼

假設這個 function (Turtle）需要很長的時間，那麼就要查看一下它在裡面做了什麼，如果他做很多事情，那我們也許就減少它 code 的工作量，就解決耗時問題了

![Screen-Recording-2025-08-07-at-14.39.35-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen-Recording-2025-08-07-at-14.39.35-1.gif)

那也有可能我們發現某個 function 一直被 call，像右邊 Unicorn function，他反覆做了很多次，這情況通常就是源頭，一直 call 的，與其去優化 Unicorn function，不如去處理源頭的 for loop，看如何減少呼叫次數

![Screen-Recording-2025-08-07-at-14.39.35-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen-Recording-2025-08-07-at-14.39.35-2.gif)

所以在耗時的 function ，我們應該往下看，看 function 的實作細節，優化 code 減少工作量

而在被多次 call 的 function 情境，應該往上看源頭，研究如何減少被呼叫次數

![Screenshot 2025-08-07 at 14.55.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_14.55.28.png)

但在 Time Profiler 無法得知是什麼情況，而且從 Time Profiler 收集的訊息就只有每個間隔，當前 CPU 上被呼叫的 function，所以下圖的這些場景都有可能發生

![Screenshot 2025-08-07 at 14.57.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_14.57.20.png)

### Time Profiler only samples

- Cannot differentiate between long-running and often-called functions:
- Use `os_signpost` or **other instruments** to measure precise runtime or how often specific work is performed

所以要測量特定 function 的執行時間，記得使用 `os_signposts` ，做法可參考 WWDC19 [Getting Started with Instruments](https://developer.apple.com/videos/play/wwdc2019/411/)

但也有其他工具能準確告訴我們發生什麼事，而 SwiftUI View Body 工具就是其中之一

要使用 SwiftUI View Body 這工具，回到 Instruments 畫面，點擊右上角 + 號按鈕，這是提供所有工具的列表，甚至我們自己也可以編寫自定義工具

![Screenshot 2025-08-07 at 15.11.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_15.11.45.png)

最後可以把它拖曳進來，那因為剛剛第一次紀錄的時候還沒加進來，所以會是空的，沒資料

![Screen-Recording-2025-08-07-at-15.12.51.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen-Recording-2025-08-07-at-15.12.51.gif)

紀錄完之後，可以使用 `Ctrl + Plus` 增加 track 的高度

![Screen-Recording-2025-08-07-at-15.21.35.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen-Recording-2025-08-07-at-15.21.35.gif)

![Screenshot 2025-08-07 at 15.23.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_15.23.50.png)

再來把 Severe Hang 給放大來看，看到 SwiftUI 的 track，每個間隔代表執行一次 view body 的執行，其中第二排 track 有很多橘色的間隔，都被標記為 `BackgroundThumbnailView` ，這代表執行了多少次 view body，以及每次執行需要的時間

![Screenshot 2025-08-07 at 17.54.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_17.54.34.png)

仔細看 detail view 裡把 Backyard Birds 給展開，可以看到 `BackgroundThumbnailView`  執行了 70 次，總共花費 3.23s，平均持續時間為 50ms，這很明顯一定會 Hang

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/image.png)

依照 app 的狀況來看，只需要顯示 6 張圖片，但卻跑了 view body 70次，似乎不是很正常，所以應該要減少 call view body 的次數

![Screenshot 2025-08-07 at 18.01.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_18.01.15.png)

為了找到相關 code，可以用 main thread track，選到該 function，然後右鍵 Reveal in Xcode，馬上就找到 code 了

![Screenshot 2025-08-07 at 18.14.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_18.14.56.png)

![Screenshot 2025-08-07 at 18.16.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_18.16.15.png)

可以對這個 View 右鍵 Find → Find Selected Symbol in Workspace

![Screenshot 2025-08-07 at 18.16.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_18.16.49.png)

這個 View 被包在兩層 ForEach 裡，確實會一直重複跑到

![Screenshot 2025-08-07 at 18.18.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-07_at_18.18.30.png)

解決方案，可使用 `LazyVGrid`，關於 Lazy 的相關細節可參考 WWDC20 [Stacks, Grids, and Outlines in SwiftUI](https://developer.apple.com/videos/play/wwdc2020/10031/)

![Screenshot 2025-08-08 at 10.04.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.04.20.png)

這樣調整後的結果，Hangs 的部分變成 Microhang，並不到 400ms

![Screenshot 2025-08-08 at 10.05.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.05.07.png)

右鍵 Set Inspection Range 看細節，會發現原本的 `BackgroundThumbnailView` 只執行了 8 次，從 severe hang 變成 micro hang，已經不是名顯的 delay 了，有些人可能覺得這樣就夠了，但接下來看 iPad 的例子，就會知道這樣的優化還不夠

![Screenshot 2025-08-08 at 10.07.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.07.10.png)

我們試試在 iPad 上的效能如何，另外一點是因為我們也要確保在各設備上能正常運行，結果會發現在 iPad 上 run，明明做和剛剛 iPhone 一樣的事情，卻又變慢了，其實仔細看這個 sheet，能明白因為螢幕變大，要呈現的 thumbnails 變多了

![Screenshot 2025-08-08 at 10.19.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.19.15.png)

我們的 Instruments 也記錄了這次的狀況，一樣把 Hang 的部分右鍵 Set Inspection Range and Zoom 展開，然後選起 View Body 的 tack，看 detail view 會發現 `BackgroundThumbnailView` 又執行了很多次，共 40 次，總共花費 1.49s

![Screenshot 2025-08-08 at 10.24.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.24.56.png)

所以這樣的範例能夠知道，一樣的程式碼在 iPhone 和 iPad 上執行會有不一樣的效能結果，所以往後就算看到 mirco hang，也記得處理

剛剛前面處理方向是因為多次 call `BackgroundThumbnailView` 造成效能的問題，現在看看如何提升單次執行的效率

首先在 View Body track 裡對單個 `BackgroundThumbnailView` hang 右鍵 Set Inspection Range，然後再點選 Main Thread track，在右下角 Heaviest Stack Trace 裡選起 `BackyardBackground.thumbnail.getter` 

![Screenshot 2025-08-08 at 10.47.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.47.06.png)

從 detail view 可以得知到需要消耗 150ms，然後在 Heaviest Stack Trace 裡可以看到 thumbnail getter call 了 `UIImage imageByPreparingThumbnailOfSize:` ，這看起來只是順手處理縮圖，但這種處理縮圖也算耗時，在這邊也需要 150ms 左右，這件事應該由 background thread 來執行，不應該交給 main thread

![Screenshot 2025-08-08 at 10.47.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.47.45.png)

接著我們在 Heaviest Stack Trace 裡選 `BackgroundThumbnailView.body.getter` ，

然後對按下右鍵選擇 Open in Source Viewer

![Screenshot 2025-08-08 at 10.51.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.51.20.png)

這時 detail view 會從 call tree 改顯示為 source viewer，會看到 body getter 的實作

![Screenshot 2025-08-08 at 10.38.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_10.38.54.png)

接下來我們透過 path 的最右邊 menu button，在 Xcode 把這個檔案打開，目前我們知道在處理縮圖這件事，不應該給 main thread 來執行，應該交給 background load thumbnail

![Screenshot 2025-08-08 at 11.12.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.12.54.png)

首先調整 `BackgroundThumbnailView` ，看他 image loaded 了嗎，假如沒有先顯示 `ProgressView` 

![Screenshot 2025-08-08 at 11.16.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.16.53.png)

最後就是 load thumbnail 的優化了，我們可以透過 `.task` 這 modifier 來 load thumbnail

![Screenshot 2025-08-08 at 11.19.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.19.20.png)

接下來我們來看看成果，很明顯當點擊 button 後 sheet 是立即顯示，thumbnail 會先顯示 progress indicators 顯示，load 完就顯示完整 thumbnail

![Screen Recording 2025-08-08 at 11.20.08.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-08_at_11.20.08.gif)

## Async hang

雖然在使用上看起來成功了，但回到 Instruments，還是有接近 2 秒的 hang

![Screenshot 2025-08-08 at 11.23.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.23.46.png)

現在的情況是 hang 的時間稍微晚一些，看來是 load thumbnail 的部分沒問題，但後續可能有，我們再來看看 hang 在 Backyard Birds App 中的哪裡發生

![Screen Recording 2025-08-08 at 11.25.49.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screen_Recording_2025-08-08_at_11.25.49.gif)

很明顯當 sheet 起來之後，瘋狂的點擊 done button 沒有立即反應，所以等於在 loading 的過程 UI interaction 是被卡住的，這就是 Instrument 告訴我們的 hang

### Types of hangs: Interaction

這種 hang 和之前介紹的有點不一樣，前面說的是 main thread busy 或 blocked，但還有一種給 hang 分類的方式，就是 hang 的發生原因及時間，這稱為 synchronous and asynchronous hang

![Screenshot 2025-08-08 at 11.41.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.41.20.png)

簡單來說 

- Synchronous/Direct: 假設 main thread 已在 work，當有一個事件進入之後，需要很長時間處理它，那麼這事件就是一個 hang
- Asynchronous/Delayed: 假設控制剛剛上述的問題，讓事件能快速處理，但我們可能還是會延遲一些工作，讓 main thread 稍後完成，或者 main thread 又有其他事件要處理，所以後面的事件需要等待之前的工作完成才能得到處理，這樣會導致 hang

![Screenshot 2025-08-08 at 11.43.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_11.43.07.png)

### Hang detection

而 Hang detection 的工作方式為，查看 main thread 上所有工作項目，並檢查工作項目是否過長，如果是就會被標記成 potential hang

![ezgif-32f0485a47c98b.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/ezgif-32f0485a47c98b.gif)

無論是否有用戶 input，它都這樣檢查，因為用戶 input 可能在任何時刻發生，然後就可以檢查到 potential hang，所以 asynchronous or delay 的情況一樣會被檢查到

![ezgif-32f1018e662613.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/ezgif-32f1018e662613.gif)

但這些只是算 potential delay，而不是真實的 delay

asynchronous hangs 通常由 `dispaych_async` 在 main queue 或 main actor 上 run asynchronously 在 Swift Concurrency task 引起的，簡單來說就是任何在 main thread 的工作原因都有可能導致 hang

![Screenshot 2025-08-08 at 14.12.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.12.33.png)

像我們剛剛的例子，一開始是第一個 hang 是 synchronous hang，點擊顯示 sheet 的 button 後，後續要做了一個很長時間的 work，導致顯示 delay。

點擊 done button 本身不會觸發什麼耗時的 work，但因為 main thread 上有正在處理的 work，導致 done button 的點擊沒有被 main thread 處理

現在來處理這問題，回到 Instruments，在 detail view 可以看到 `BackgroundThumbnailView` 有 75 次呼叫，因為大多數 thumbnail body getter 被 execute 兩次，SwiftUI 大概建立了 40 個 progress indicators 顯示在 grid 上，但實際上最終只顯示了 35 個，然後這些 thumbnail loaded 後，view 會更新再次 call body，總共就會 execute 75 次 body getter，而這樣總執行時間也小於 1ms，所以 body getter 的速度已經很快了

![Screenshot 2025-08-08 at 14.14.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.14.08.png)

但我們還是有一個 hang，再次點擊 main thread track，查看 Heaviest Stack Trace，依然是 thumbnail getter 讓 main thread 花費很多時間，但仔細看他這次是在一個 closure 裡

![Screenshot 2025-08-08 at 14.20.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.20.54.png)

double-click 它，detail view 會打開 source viewer，這段 code 應該在這時刻執行沒錯，但不應該在 main thread 上執行

![Screenshot 2025-08-08 at 14.27.52.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.27.52.png)

類似這樣的問題 Swift Concurrency tasks 沒有按照期望的方式 execute，我們有另外一個輔助解決工具，就叫做 Swift Concurrency Tasks，使用就像加上 Hangs track 一樣

![Screenshot 2025-08-08 at 14.34.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.34.21.png)

在 main thread track 中，因為加了 Swift Tasks，我們可以顯示多個圖表，

![ezgif-8aab52a103c855.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/ezgif-8aab52a103c855.gif)

再來放大 hang 的時間間隔，一樣右鍵 Set Inspection Range

![Screenshot 2025-08-08 at 14.39.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.39.58.png)

這時看 Main Thread track 就會看到 Swift Task 那條有很多 task 在執行了

![Screenshot 2025-08-08 at 14.40.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.40.30.png)

選擇一項 task，右鍵 Set Inspection Range

![Screenshot 2025-08-08 at 14.42.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.42.19.png)

在 Heaviest Stack Trace，確認這 task 是不是在處理 thumbnail 的 work，看來沒有錯，該 task 確實是在處理 thumbnail，只是不希望這 task 在 main thread 上執行

![Screenshot 2025-08-08 at 14.43.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.43.27.png)

會這樣是因為，我們使用了 View，他預設是在 `@MainActor` 執行，所有 `body` 也會被影響

再來 `.task {}` 也受到上下文影響，也跟著會在 `@MainActor` 做事

最後是 `thumbnail`，因為它是 synchronous 的，所以在哪個 thread 上呼叫，就會被同步影響，最後也在 main actor 上執行

![Screenshot 2025-08-08 at 14.45.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.45.54.png)

### Tasks and action isolation

![Screenshot 2025-08-08 at 14.57.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_14.57.58.png)

default 情況下 Swift Concurrency Tasks 會繼承上下文的 actor isolation，所以 SwiftUI 的 .task modifier 也一樣

有兩種方式擺脫 main actor

1. 使用 synchronous call 未綁定在 main actor 的 function，這樣就可以允許 task 脫離 main actor，但有些情況是不可行的
2. 使用 `Task.detached {}` ，將 task 從上下文的 actor 分離出來，但這不是最簡潔的方法，而且是建立一個獨立的 task，比簡單暫停一個現有的 task 成本還高，而使用第一種方法，當 View 消失時 SwiftUI 還會自動取消 task modifier 裡的 task，但是 Task.detached 就沒有這樣的特性

更多細節可以參考
[WWDC22 Visualize and optimize Swift concurrency](https://developer.apple.com/videos/play/wwdc2022/110350/)

[Improving app responsiveness](https://developer.apple.com/documentation/xcode/improving-app-responsiveness)

因為我們的情況是 asynchronous context，加上 thumbnail function 轉為 nonisolated 及 asynchronous 是很容易的，所以方法1更合適我們

做法很簡單，首先找到定義 `thumbnail` 的地方，將原本 synchronous 改為 asynchronous，加上 keyword `async` 即可

![Screenshot 2025-08-08 at 15.24.22.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.24.22.png)

最後在 call 的地方加上 `await` ，因為它現在是 asynchronous 的

![Screenshot 2025-08-08 at 15.25.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.25.35.png)

這樣就會讓他在 Swift Concurrency 中的 concurrent thread pool 中執行，而非 main thread 了

現在來看看這修改在 Instruments 上的變化，Main Thread 上的 task 時間間隔都變很短

![Screenshot 2025-08-08 at 15.27.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.27.30.png)

往下滑動來看，其他 thread 的 track 有很多我們的 task，現在他們在 thread 上不是 parallel 執行，也不是 sequentially 的，已經善用我們的 CPU 多核心

![Screenshot 2025-08-08 at 15.36.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.36.27.png)

## Blocked main thread hung

### Hangs

![Screenshot 2025-08-08 at 15.38.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.38.30.png)

我們已經調查且修復 main thread unresponsive 的狀況，知道是 main thread 卡住造成

也知道 hang 可能有 synchronous （直接單一個大 task 要給 main thread）及 asynchronous（安排大量的 task 要給 main thread 上 execute，導致有些 delay） 的，這在 Instruments 都可以檢測。

最後在 fix 的部分也有概念，把 task 放在 background 或者讓單個 task 的 work 量減少，只有更新 UI 才需要 main thread 上

![Screenshot 2025-08-08 at 15.47.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.47.18.png)

但還有一個情況是 Blocked Main thread，這種情況 main thread 也不會佔用 CPU 太多，這情況還是需要透過 Instruments 來分析

仔細看這個 Track，明明已經是 Severe Hang 但是 CPU 的用量卻極低，這就是典型的 Blocked Main thread，

![Screenshot 2025-08-08 at 15.52.04.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.52.04.png)

我們把 CPU Usage 的 track 放大來看，會看到很多 mark，都是透過 Time Profiler 採集的 sample，但到後面就沒有了

![Screenshot 2025-08-08 at 15.56.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.56.14.png)

假如選這個沒有 sample 及 CPU 沒有活動的區段，會發現 detail view 上沒有任何資料

![Screenshot 2025-08-08 at 15.54.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.54.11.png)

這時我們可以使用另一個 tool Thread States，跟之前加的 tool 一樣，從 Instruments library 新增，在畫面上的右上角有顆 plus button

![Screenshot 2025-08-08 at 15.58.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_15.58.27.png)

現在多一條 Track，和 Swift Concurrency instrument 一樣

![Screenshot 2025-08-08 at 16.00.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.00.00.png)

但我們要關注的點在 Main Thread track 上，仔細看有一個很長的 `blocked` 的時間間隔

![Screenshot 2025-08-08 at 16.01.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.01.10.png)

超過了 6s，這就是我們 hang 的持續時間

![Screenshot 2025-08-08 at 16.01.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.01.30.png)

當我們點擊那個 blocked 時間間隔，底下的 detail view 也會更新，這稱 Narrative view，裡面顯示了 blocked state，這裡面告訴我們 thread 的狀況，做什麼，何時做，為什麼要做

![Screenshot 2025-08-08 at 16.04.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.04.11.png)

仔細看時間項目，有一條就告訴我們 thread 被 blocked 6.64s，並且是因為 syscall `mach_msg2_trap` 

![Screenshot 2025-08-08 at 16.47.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.47.36.png)

而右下角是 Backtrace，但這個 backtrace 並不是最重要的 backtrace，他不是某種集合，而是 `mach_msg2_trap` 的精準 backtrace

![Screenshot 2025-08-08 at 16.51.40.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.51.40.png)

function 呼叫是最底下的 leaf node，然後 call stack 在它的上方，而透過 call stack ，可以得知 syscall 是由 MLModel 分配發起，而 MLModel 由於分配 `ColorizingService` 的 object 發生的，而 object 上有一個 property 叫 `shared` 是個 singleton，最後就會看到 body getter 的 closure 中呼叫的（一直往上源看）

![Screenshot 2025-08-08 at 16.52.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_16.52.46.png)

![Screenshot 2025-08-08 at 17.10.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.10.33.png)

double-click 它，會看到 source viewer，直接看 code 似乎沒什麼問題對吧？

![Screenshot 2025-08-08 at 17.11.47.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.11.47.png)

因為我們在其中使用了一個 singleton，任何的 access 就會啟動建立這個 ColorizingService instance，而這過程載入就會 blocked thread（假設 ColorizingService 的 init 過程很複雜，work 量很大，那一定會造成 main thread 的工作量大增），

![Screenshot 2025-08-08 at 17.21.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.21.45.png)

那直覺可能就會想說，那我們用 `await async` 避免 blocked main thread 就好了，但這並不能解決問題

首先 `await async` 只能用在 asynchronous function，而目前的 `shared` 並不是，它只是一個 `static let` property，是一個 synchronous 的，而假如要用在 `colorize(:_)` 這 function 是可以的，因為他是一個 asynchronous function

![Screenshot 2025-08-08 at 17.30.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.30.06.png)

### Fix

![Screenshot 2025-08-08 at 17.33.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.33.53.png)

1. 把 `shared` 加上 async，這樣就可以讓他脫離 main actor，基本上這做法是 ok 的，當情境是等待其他地方的 thread 要繼續執行時，通常沒問題
2. 但是有時候 blocked thread 可能是因為 locked or semaphores，要特別注意，可參考下面的 session，避免寫出有問題的 code

其他細節可參考 

WWDC21 [Swift concurrency: Behind the scenes](https://developer.apple.com/videos/play/wwdc2021/10254/)

最後再來看一個 blocked main thread 的例子

這是我們剛剛處理的 case

![Screenshot 2025-08-08 at 17.58.24.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.58.24.png)

但在往左邊看還有另外一個 blocked main thread，但明明也是 blocked，他卻沒有被 Instruments 標記成 potential hang，這邊 main thread 只是休眠了，因為沒有 user input，但從 operating system 的角度來看確實是 blocked，但它只是在無事可做時不運行，主要是為了節省資源，只要有 input，就會被喚醒，進行處理，因此要確定 blocked main thread 是否是 responsiveness 的問題，需要查看 Hangs 工具，而非 Thread State 工具

![Screenshot 2025-08-08 at 17.59.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_17.59.10.png)

### Clarifications

所以當 Blocked Main Thread 或 High CPU usage 時不一定是 Unresponsive Main Thread

但 Unresponsive Main Thread 一定是 Blocked Main Thread or Busy Main Thread

![Screenshot 2025-08-08 at 18.05.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC23/Analyze%20hangs%20with%20Instruments/Screenshot_2025-08-08_at_18.05.06.png)

hang detection 會考慮這些所有細節，並且只會標記 main thread unresponsive 時的 potential hang

## Wrap-up

- Keep work on the main thread below 100ms
- Determine whether the main thread is busy or locked
- Hangs can surface synchronously or asynchronously
- Do less work
- Move work to the background
- Measure fires, the optimize