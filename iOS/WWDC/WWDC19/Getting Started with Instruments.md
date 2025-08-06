# Getting Started with Instruments

![On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/DJI_0036.jpg)
On the road from Queenstown to Glenorchy Wharf & Viewpoint, New Zealand.


## Orientation

Instruments 可以在開發初期就使用，建議要頻繁檢測自己的 app 效能，提早發現問題提早處理。

它包括 iOS、macOS、watchOS 和 tvOS。

![Screenshot 2025-08-05 at 10.02.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.02.29.png)

雖然他內建在 Xcode 裡，但他其實是一個獨立 app，所以有時候可以獨立使用

![Screenshot 2025-08-05 at 10.03.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.03.26.png)

Instrument 提供一系列工具，幫助分析 app 中的錯誤，它透過收集時間序列追蹤數據，這收集到的數據稱為 `Treat` 。

這邊介紹兩個可能常用的功能

Time Profiler: 使用操作系統提供的內部架構，以固定的時間間隔，來收集有關 thread 的所有 call stacks

Points of Interest: 從 app 中重要的區域收集數據，讓我們能夠用多種 API 對其加以強調，像是強調某個範圍的程式碼狀況，例如跑了多久，才從 A 點到 B 點結束，加以強調的 API 可用 Signpost API。

當一開始打開 Instrument，會看到一個 template list，這裡提供各種工具，來解決特定的效能問題

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image.png)

上圖是在 WWDC19 的截圖，新版 Xcode 16 已不太一樣

首次打開 Time Profiler 是這樣，空白的

![Screenshot 2025-08-05 at 10.23.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.23.27.png)

再來我們透過點擊右上角的 + 號按鈕，來額外增加 Instrument

![Screenshot 2025-08-05 at 10.24.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.24.03.png)

這時就可以看到可用的 Instrument 的完整清單

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image%201.png)

左上角是 Profiling Controls，讓我們能夠記錄、暫停或停止數據收集，以及設定 device 及 target

![Screenshot 2025-08-05 at 10.30.13.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.30.13.png)

當開始 record，Instrument 會佔用系統資源，為了最小化對我們 app 的影響，Instruments 還提供一個叫做 Last Few Seconds Model 的功能，也可以稱為 Windowed Mode

![Screenshot 2025-08-05 at 10.36.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_10.36.36.png)

Windowed Mode 會在記錄過程中阻止 Instruments 分析與顯示結果，直到按下暫停錄製時，才會收集最後幾秒的數據（可以自行設定秒數），假設使用的情境需要執行很久，而且這期間的數據不需要的話，直到發生問題時才需要記錄，那這 mode 就很實用，有些 template default 也是用這 mode

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image%202.png)

假設已收集完數據，會看到好多訊息，接下來就各自區塊來講解

![Screenshot 2025-08-05 at 11.41.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_11.41.42.png)

 

首先是上面的部分，Track Viewer，一個 Track 會展示與某個事件源相關的時間序列的追蹤數據，比如說 process, thread, or CPU core，單個 Instrument 可能有多個 Track，像下面這張圖就有3個 Track，分別為 Time Profiler Instruments，提供我們 App 系統佔用的總結，第二個為 Points of Interest Instrument，最後是把數據進行進一步的細分

![Screenshot 2025-08-05 at 11.43.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_11.43.39.png)

一個 Instrument 的追蹤可能會生成數十個 Track，所以這邊透過 Track Filter 來顯示需要的 Instrument，或按照 thread or CPU core 來細分

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image%203.png)

最底下的 View 是 detail view，提供 Track 的追蹤訊息，下面的例子是選了 Time Profiler 軌道，可以看到每個 thread 上呼叫的 function

![Screenshot 2025-08-05 at 14.02.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.02.43.png)

在 detail view 右邊的是 Extended Detail View (Inspector)，它可以提供正在使用的 Instrument 的更詳細資訊，取決當前情境和所選項，以目前情況來看會獲得 **heaviest** call stack 的總結

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image%204.png)

當前情況是 Inspection Head，它可以選擇一個時間範圍，這樣就可以選中所有在那個時刻正在發生的事件，然後被選中的事件會顯示在 Hovering Labels

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/image%205.png)

這些資訊是可以儲存並重新打開的，能夠探索之前的結果，也可以分享給同事，以供研究

## Profiling your app

這邊 demo 了一個 Solar System app，選擇使用 Time Profiler，因為想要分析在某個區段時間內出現的問題

![Screenshot 2025-08-05 at 14.28.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.28.46.png)

這問題是在重新載入時會 UI 會卡頓，而且是可以重現的，這樣就很好去找出問題，錄製後來看結果，從下圖可以發現很明顯 CPU 使用率超過 100%

![Screenshot 2025-08-05 at 14.31.03.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.31.03.png)

現在想要把它和其他的 track 作比較，所以要使用一個功能 Track Pinning，把游標放到最左邊前面，會看到一個 + 號的按鈕

![Screenshot 2025-08-05 at 14.38.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.38.11.png)

按下去之後就有多一條 track 固定在下方，所以 scroll 到其他 track 時，他還是會固定在那邊，方便瀏覽，這功能可以固定多條 track

![Screen Recording 2025-08-05 at 14.43.18.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-05_at_14.43.18.gif)

在 track list 中還可以看到那 app 的 CUP 使用情況以及 lifecycle

![Screenshot 2025-08-05 at 14.47.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.47.16.png)

仔細去看可疑的時間點，會發現一個 label，上面寫 Spinning

![Screenshot 2025-08-05 at 14.48.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.48.19.png)

Spinning 就是代表 main thread 卡住了，在 main thread 上的使用情境應該是用戶的輸入或者更新 ui，顯然這邊應該有問題，才會出現 Spinning

在 app 的 track 還可以再展開 thread 的情況，可以用來查看 main thread

![Screen Recording 2025-08-05 at 14.50.29-4.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-05_at_14.50.29-4.gif)

這邊很明顯可以看到 main thread 有很高的使用量，我們可以把這段時間選起，這樣下方的 detail view，會過濾事件，只顯示這區段的時間內的事件

![Screenshot 2025-08-05 at 14.54.40.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.54.40.png)

接下來仔細看 detail view ，這邊是提供 call graph，這些都是我們分析時所選時間區段內呼叫的 function

其實 Time Profiler 每秒會捕獲許多次 snapshot，並且記錄 process 中所有正在 run 的 function

![Screenshot 2025-08-05 at 14.59.12.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_14.59.12.png)

仔細看上面有一個列 100%，這表示每次採樣中，Solar System Mac 都出現了，這才是正常的，因為那是我們要分析的 app

往下看會看到 main thread 出現率是 96.7% ，假如把它展開來看，會發現他是嵌了很多層，要一一點開會很麻煩，有一個小技巧是鍵盤的 `option` 按住 + 點擊展開按鈕，會全部展開

![Screen-Recording-2025-08-05-at-17.39.58.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen-Recording-2025-08-05-at-17.39.58.gif)

但展開之後，要尋找到問題點變得十分困難，所以 Instruments 在 Time Profiler 的 Extended Detail View 提供 heaviest stack trace，這是在分析過程中，最常呼叫的 function 集合

![Screenshot 2025-08-05 at 17.45.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_17.45.17.png)

其中灰色的字是代表系統 Framework or Libraries，那仔細看還有白色 highlight 的部分，而其中第一項稱為 `thunk` 

![Screenshot 2025-08-05 at 17.49.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_17.49.56.png)

什麼是 `thunk` ? 它是由 compiler 生成的協助程式碼，他與 app 中的任何程式碼沒有直接關係，所以可以先無視他。

另外補充：它用來處理閉包（closure）的記憶體管理和呼叫約定（calling convention）。它是一個實作細節，通常可以忽略。

再來我們可以看 `thunk` 的下面一項，透過點擊兩次，可以直接跳進 code 裡，這裡就可以知道有一個 `scheduleParsingTask` 的 function

![Screen-Recording-2025-08-05-at-18.00.49.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen-Recording-2025-08-05-at-18.00.49.gif)

看 code 大概可以得知他在 main thread 上有些任務，其中有 parser 數據，很明顯這不太適合放在 main thread 上執行，想要修復他，可以透過 toolbar 上有顆 Xcode icon 的按鈕快速打開

![Screenshot 2025-08-05 at 18.06.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_18.06.39.png)

調整的內容很單純，就把 parser 的事情放給其他 queue 執行，完成後再回 main thread，再來我們從 Xcode 也可以開啟 Profile (快捷鍵 `command` + `i` ) 來檢查這 bug 處理的結果

![Screenshot 2025-08-05 at 18.09.46.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_18.09.46.png)

![Screenshot 2025-08-05 at 18.11.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_18.11.01.png)

原本的狀況是因為 main thread 被卡，所以這次可以透過 filter 快速找出 main thread 的 trace，查看更改後的結果

![Screenshot 2025-08-05 at 18.16.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-05_at_18.16.55.png)

從上圖可以很明顯看到，CPU 使用高峰的地方，在 main thread 的 trace 已經沒有那麼高的負載了，問題已解決

### Profiling Tips

- Time Profiler shows how your app is spending time: Time Profiler 是一個理解 app 如何使用時間的工具，可能在 responsiveness issue 用得上，像是動畫加載或者想要加速 app 啟動，讓他更快顯示內容，這些情況都很合適使用它
- Check main thread when responsiveness issues occur: 如果遇到 responsiveness 的問題，先看 main thread 狀況
- Profile release builds: **應該在 release 環境進行 profile**，因為 Compiler 支援各種不同 level 的優化，假如普通開發用的 Build-Run cycle (debug mode)，是會用 low-level 的優化，這讓過程更快一點，而且還會跑一些額外的除錯資訊和安全檢查，這些都會讓整體變慢，而使用 release 才會高效能的跑，才準確
- Profile with difficult workloads and older devices: 可以多用不同的設備或舊裝置進行 profile，能夠更確保產品品質

### Instruments on All Platforms

目前 Instruments 包含全平台 iOS、iPad OS、watchOS、tvOS、macOS、visionOS

### Simulator Caveat

Instruments 可以 run 在 simulator，但要記得他用的是 Mac 的資源，所以也擁有 Mac 的 CPU 和 memory，還有文件系統行為和硬碟行為，還有散熱限制，所以與原本的裝置（例如 iPhone）還是有差異，**強烈建議使用實體裝置**

### What About Efficiency?

Main thread responsiveness isn’t the whole story

除了 main thread 上的優化，還可以做其他事情來增加 responsiveness，例如優化 CPU

High CPU use can

- Drain the battery
- Heat up the device
- Spin up fans

接下來就是透過 Signposts 來做 demo

## Using Signposts

它是用來增加 Instruments 從中收集的 trace，並更好理解我們的 code 如何使用系統資源

### Statistical Profile Versus Measurement

我們來對比一下 Time Profile 做的事情，這樣更好理解 Signposts

一開始 Time Profile 會做 code 的統計分析，透過一個固定時間間隔內，觀測 app 中所有的 thread，並且建構出時間和 call stacks 的相關分析

![Screenshot 2025-08-06 at 15.03.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.03.41.png)

但是，相關分析並不能取代精準評估，無法得知 code 如何執行以及為什麼會執行

過程還會有一些 block code，在幾個時間點執行 (execution pattern 1)，或者更長的時間段內持續執行(execution pattern 2)，還有某些在特定的 Argument 上會 call function (execution pattern 3)，也會造成 CPU 忙碌

![Screenshot 2025-08-06 at 15.09.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.09.05.png)

為了區分這幾種 execution patterns，我們需要記錄 code 的精確評估，但這樣引出了一個問題，我們能操作的紀錄性能量測最好的方式是什麼？How to log operations?

不用擔心，我們不用 print 來處理，只需要使用 Signposts

### Features of Signposts

- Simpler and more efficient than printing: 專門 log 有 structured performance data ，所以比 print 還簡單高效
- Built-in support for measuring time: 提供了對評估時間的內建支援，所以無需擔心讀取 clock source 或使用什麼時間做為基準而進行評估
- Traced by Instruments: Instruments 知道如何追蹤它，來看下面的一個例子比較好理解

下面 highlight 的部分是 Points of Interest track，它展示了與某個在 code 中加入的 Signpost 相關的重點區域

![Screenshot 2025-08-06 at 15.22.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.22.56.png)

### Demo

**Using Signposts with Points of Interest**

回到一開始的例子，這邊把 CPU 使用量高的地方給選起

![Screenshot 2025-08-06 at 15.29.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.29.39.png)

接著在看 heavy stack trace （右下角），這邊要去找出最常被 call 或耗能的 function，目前已知 `setupScene()` 好像在處理很多 Array，可能是重新加載中操作的一部分，所以這時要去觀察他花了多少時間

![Screenshot 2025-08-06 at 15.35.25.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.35.25.png)

從 Xcode 中找到這 function 後，可以看到 line 86 有使用 `print`，因為那是 function 開始點

![Screenshot 2025-08-06 at 15.45.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.45.42.png)

然後往下滑會看到 line 205 也有一個 `print` 是 function 結束點

![Screenshot 2025-08-06 at 15.47.04.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.47.04.png)

但就像前面說的 Instruments 並不知道如何讀取 Print Statements，所以我們要先建立一個與 Instruments 進行通訊的 code，做法很簡單，先宣告一個 `OSLog` ，然後在 function 裡設定 begin 及 end （line 85 - 91）

![Screenshot 2025-08-06 at 15.50.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.50.35.png)

然後再次執行 Profile，進行 app 操作，這時仔細放大來看那些 CPU 用量很大的地方，在 Points of Interest 這 trace 裡，有一個 `setupScene` ，這就與我們的 Signpost 間隔相關

![Screenshot 2025-08-06 at 15.52.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.52.14.png)

並且這裡還有幾個相鄰的重點區域，在 track 中被記錄下來，但這不是預期的，也太多次被 call，不合理，滑鼠游標停在上面時，可以數一數有多少個重點區域，或者可以利用 detail view 

![Screenshot 2025-08-06 at 15.56.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_15.56.01.png)

把想知道的時間區段選起來，並在點擊 Points of Interest track，就會有總結在 detail view 上了

![Screen Recording 2025-08-06 at 16.00.09.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-06_at_16.00.09.gif)

這樣仔細看就會發現怎麼 run 了 8 次，每次大概耗時 200ms

![Screenshot 2025-08-06 at 16.02.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.02.58.png)

然後可以從中間的選單 (path 的上方），來切換 detail view 的顯示內容，從 Summary: Regions of Interest → List: Regions of Interest，這樣方便看每一條項目。仔細看會發現很明顯不太對，我們好像一直在呼叫同個 function 做同件事

![Screen Recording 2025-08-06 at 16.04.02.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-06_at_16.04.02.gif)

接下來我們來找出 caller，來判斷一下是什麼原因需要一直 call 這 function，現在是選起 Points of Interest track ，我們要改回選起 Time Profiler，接下來透過右下角的 heavy stack view，來查看 caller，這很明顯是 `prepareScene()`

![Screenshot 2025-08-06 at 16.19.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.19.17.png)

點擊兩下後就可以跳過去 source，從這邊查看 code，可以得知 `prepareScene()` 並不是造成重複 call 的根本原因

![Screenshot 2025-08-06 at 16.20.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.20.18.png)

重返回上一頁，可以從 path 那邊在點擊 root

![Screenshot 2025-08-06 at 16.21.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.21.50.png)

然後繼續在 heavy stack 查看更上一層 caller，會找到一個 `updateWithPlanets` 的 function，點擊兩下到他的 source，可以看到這邊確實用了 for loop，那我們接著點擊 path 右邊的 Xcode icon 來開啟 Xcode

![Screenshot 2025-08-06 at 16.23.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.23.35.png)

仔細看這段 code，大概得知在這 for loop 裡，每次得到 planet 時就要 call `prepareScene`，以功能面來說沒錯，要一直更新畫面，但這也導致很耗能，最後解法就是把 `prepareScene` 放在 for loop 外面，避免一直重新繪製畫面

![Screenshot 2025-08-06 at 16.31.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.31.50.png)

最後透過測試來看 profile，建議在開發時，就要多寫測試，來查看效能，在 test case 裡，可以在 run 的 icon 上右鍵，選擇使用 Profile 來執行 test case

![Screenshot 2025-08-06 at 16.33.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.33.19.png)

在這 test 已經把重新加載的操作放在 Measure API 裡，Measure API 會多次執行，來收集一些重複的 measurements

![Screen Recording 2025-08-06 at 16.40.34.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-06_at_16.40.34.gif)

最後跑完，放大來看結果，**XCTestPerf…** 這區段是由 Measure API 提供，這內容是每次 run 的工作負載 iteration

![Screenshot 2025-08-06 at 16.46.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.46.29.png)

除了用拖曳的方式來選取某個時間區段，也可以使用 triple click

![Screen Recording 2025-08-06 at 16.51.01.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screen_Recording_2025-08-06_at_16.51.01.gif)

仔細放大來看，現在 `setupScene` 只會被 call 一次了，這表示我們從使用 Signpost 獲取的訊息，把我們所用的 CPU 時間減少了幾個數量級

![Screenshot 2025-08-06 at 16.53.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-06_at_16.53.16.png)

## Concepts

- Statistical profiles show which code is most commonly executed: 從 Time Profiler 或者其他地方收集到的統計分析十分有用，它能展示那些 code ，被頻繁的執行，但記得這並不能替代用 Signpost API 紀錄的評估
- Exact measurements show how and why code is executed: 因為 Signpost API 能告訴我們 code，是怎麼被執行的，以及為什麼會被執行
- XCTests reliably reproduce workloads for profiling: run test 是很有幫助的，能穩定且反覆重現  workload，記得能在開發的初期，就經常 profiling 它

### Instruments Templates for Resource Usage

- File Activity
- Network
- System Trace

除了上述這些，還可以自己客製化 

![Screenshot 2025-08-04 at 09.09.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC19/Getting%20Started%20with%20Instruments/Screenshot_2025-08-04_at_09.09.05.png)

## Summary

- Profile early and often
- Try out Instruments today

More Information

[WWDC 18 - Creating Custom Instruments](https://developer.apple.com/videos/play/wwdc2018/410/)

[WWDC 18 - Practical Approaches to Great App Performance](https://wwdcnotes.com/documentation/wwdcnotes/wwdc18-407-practical-approaches-to-great-app-performance/) （網路上找不到這部影片了）

[Logging](https://developer.apple.com/documentation/os/logging)

[Profiles and Logs](https://developer.apple.com/bug-reporting/profiles-and-logs/)