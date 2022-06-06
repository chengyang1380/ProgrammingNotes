# 簡單聊聊 iOS Run Loop

## 前言
iOS 的 Run Loop 廣義來說就是 `Event Loop` ，也被稱為「事件循環」或者「事件分發器」，以 Apple 官方的說法是
```
Run loops are part of the fundamental infrastructure associated with threads. 
A run loop is an event processing loop that you use to schedule work and coordinate the receipt of incoming events. 
The purpose of a run loop is to keep your thread busy when there is work to do and put your thread to sleep when there is none.
```

中文的大意就是
```
Run Loop 是與 Thread 息息相關的基本基礎結構的一部分。 Run Loop 是一個調度任務和處理任務的事件循環。 
Run Loop 的目的是為了在有工作的時讓 Thread 忙起來，而在沒工作時讓 Thread 進入睡眠狀態。
```

用最直白的話來說就是「一種高級循環，讓程式繼續跑下去，而且會處理程式中各種事件( Touch Event、 NSTimer、Selector 事件)，讓 `Thread` 該做事就忙起來做事，不需要就好好休息」。

我們現在知道跟 `Thread` 有關係，所以理解 `run loop` 過後一定對理解 `Thread` 更有幫助。假如對 `Thread` 還不夠了解，可以參考之前寫的 [一篇](https://medium.com/@chengyang1380/簡單聊聊-ios-多執行緒-多線程-gcd-grand-central-dispatch-1cc947734177)。

`Event Loop (事件循環)` 的核心思想很單純，就像是以下這樣

```objective-c
int main(void) {
    initialize();
    while (message != exit) {
        processMessage(message);
        message = getNextMessage();
    }
    return 0;
}
```

所以在 `Event Loop` 中就是一直等待 -> 接收事件 -> 處理事件，直到滿足條件退出。

那麼回到 MacOS/iOS 裡，對於 `Event loop` 主要就是
`Foundation` 框架中的 `NSRunLoop`
`Core Foundation` 框架中的 `CFRunLoop`

其中 `NSRunLoop` 是對 `CFRunLoop` 的簡單封裝。

## Run Loop 與 Thread

那前面我們有說到 `Run Loop` 與 `Thread` 是關係很緊密，正常的情況下 `Thread` 會執行一個或許多任務，那執行完成後就會退出不在執行任務，但有了 `Run Loop` 後可以讓 `Thread` 不斷執行任務不退出。

官方的說法是

```
Run loop management is not entirely automatic. 
You must still design your thread's code to start the run loop at appropriate times and respond to incoming events. 
Both Cocoa and Core Foundation provide run loop objects to help you configure and manage your thread's run loop. 
Your application does not need to create these objects explicitly; each thread, including the application's main thread,
has an associated run loop object. Only secondary threads need to run their run loop explicitly, however. 
The app frameworks automatically set up and run the run loop on the main thread as part of the application startup process.
```

所以我們可以得知

`Run loop` 是與 `Thread` 綁在一起，每一條 `Thread` 都有唯一一個對應的 `Run loop object` 。
可以用 `Cocoa` 以及 `Core Foundation` 提供 `Run loop object` ，但是不能自行創建。
在 `Main thread` (主執行緒)上的 `Run loop object` 由系統自動建立的，並在 App 啟動時就自動完成，而 `Secondary threads` (子執行緒)是需要我們手動取得並處理啟動。


## Input Source 以及 Timer Sources

`Run loop` 從兩種 `source` 來接收事件，分別是 `Input sources` 以及 `Timer sources` 。

* `Input sources` 傳遞 `asynchronous` (非同步)事件，通常來自其他 `Thread` 或其他 App 的訊息。

* `Timer Sources` 傳遞 `synchronous` (同步)事件，這些事件在設定的時間點或重複的時間間隔發生。


![Apple 官方 3–1 的圖檔](https://cdn-images-1.medium.com/max/1600/1*DXGTGW8qsPOrVhQdAEJkNQ.jpeg)

從上圖我們可以得知 `Input sources` 將 `asynchronous events` (非同步事件)傳遞給相對應的處理程式，並使用 `runUntilDate:` 方法 (會讓 `Thread` 上有關聯的 `NSRunLoop object` 呼叫)到退出， `Timer sources` 將事件傳遞給相對應的處理程式，但不會導致 `Run loop` 退出。

那除了 `Input sources` 之外， `Run loop` 還會生成有關 `run loop` 行為有關的通知，假如註冊 `run-loop observers` 就可以收到相關的通知，而且還可以使用它們在 `Thread` 上進行額外處理。

`Input sources` 可以再細分成三種：
* Port-Based Sources 
系统底層的 `Port` 事件，是透過內核自動發出訊號，在 `Cocoa` 中，我們不需要直接建立 `input source` ，只需建立一個 `Port object`（NSPort object），然後使用 `NSPort` 的方法將該 `port` 新增到 `run loop` 中即可。`port object` 會處理所需 `input source` 的建立和配置，例如使用 `CFMachPortRef`, `CFMessagePortRef` 以及 `CFSocketRef` 相關的方法來建立。

如何設定與配置可參考 [Configuring a Port-Based Input Source](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-131281) 。

* Custom Input Sources
我們自己建立的 `sources`，只能從其他 `Thread` 手動發出訊號，必須使用 `Core Foundation` 中的 `CFRunLoopSourceRef` 的方法，我們還可以用多個 `callback functions` 來配置，還需要定義 `behavior, event arrives, event delivery mechanism` (行為、到達時機、傳遞機制)，它是執行在一個單獨的 `thread` 上。

如何創建可參考 [Defining a Custom Input Source](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW3) 以及 [CFRunLoopSource Reference](https://developer.apple.com/documentation/corefoundation/cfrunloopsource-rhr)。

* Cocoa Perform Selector Sources
除了 `Port-based sources` 之外， `Cocoa` 還定義 `Custom input sources` ，允許在任何 `Thread` 上執行 `Selector` ，跟 `Port-based sources` 一樣，`Perform selector` 在請求時是 `Serialized` （序列化），這樣就減緩一個 `Thread` 中運行多個方法所產生的同步問題。與 `Port-based sources` 不同的是，`Perform selector source` 在運行完 `Selector` 後自動從 `Run loop` 中移除。
假如使用的 `Perform selector` 不在 `Mani thread` 上，那就必須先啟動此 `Thread` 的 `Run loop` ，像是我們 App 系統裡的 `AppDelegate.applicationDidFinishLaunching:` 啟動後，我們就可以向 `Main thread` 發送 `selector` 的請求了。

`Selector` 有些在 `Thread` 上執行的方法:
```swift
// https://developer.apple.com/documentation/objectivec/nsobject/1414900-performselector
performSelectorOnMainThread:withObject:waitUntilDone: 
// https://developer.apple.com/documentation/objectivec/nsobject/1411637-performselectoronmainthread
performSelectorOnMainThread:withObject:waitUntilDone:modes: 
```
在 `application` 的 `main thread` 的下一個 `run loop` 週期內調用指定的 `selector`。這些方法可以提供堵塞當前 `thread` 並執行直至 `selector` 執行完成。

```swift
// https://developer.apple.com/documentation/objectivec/nsobject/1414476-performselector
performSelector:onThread:withObject:waitUntilDone:
// https://developer.apple.com/documentation/objectivec/nsobject/1417922-perform
performSelector:onThread:withObject:waitUntilDone:modes:
```
在已有的 `thread` 中調用指定的 `selector` 。這些方法跟剛上述的一樣，可以提供堵塞當前 `thread` 並執行直至 `selector` 執行完成。與剛剛最大的差異就是是否是 `main thread` 。

```swift
// https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSObject/Description.html#//apple_ref/occ/instm/NSObject/performSelector:withObject:afterDelay:
performSelector:withObject:afterDelay:
// https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSObject/Description.html#//apple_ref/occ/instm/NSObject/performSelector:withObject:afterDelay:inModes:
performSelector:withObject:afterDelay:inModes:
```
可以在當前的 `thread` 上等到下一次 `run loop` 的週期內，並延遲一個可選的時間，最後調用指定的 `selector`。

```swift
// https://developer.apple.com/documentation/objectivec/nsobject/1417611-cancelpreviousperformrequests
cancelPreviousPerformRequestsWithTarget:
// https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSObject/Description.html#//apple_ref/occ/clm/NSObject/cancelPreviousPerformRequestsWithTarget:selector:object:
cancelPreviousPerformRequestsWithTarget:selector:object:
```
用於取消前一個敘述的兩個方法( `performSelector:withObject:afterDelay:` 以及 `performSelector:withObject:afterDelay:inModes:` )。

更詳細的內容可參考 [NSObject Class Reference](https://developer.apple.com/documentation/objectivec/nsobject)

* Time sources 顧名思義就是時間定時器，它可能就是比較常見的東西，像是要做一個倒數的功能可能就會用到 [Timer](https://developer.apple.com/documentation/foundation/timer) `scheduledTimer(timeInterval:target:selector:userInfo:repeats:)` 來完成，那它的特性有：
    * 可以設定某個時間並 `sync` (同步)的傳遞事件到 `thread` 上。
    * Timer 是一種可以讓 thread 通知自己要做事的方式之一。像是我們有時候做一個搜尋功能，使用者在 `search bar` 上輸入文字時，我們就可以透過 `timer` 讓使用者輸入的過程中經過某個時間後啟動自動搜尋，讓搜尋更便利。
    * 雖然它是有關時間的通知，但它不能保證在準確的時間上執行，因它不是實時機制(`real-time mechanism`)。
    * 它跟 `Input sources` 一樣，與 `run loop` 指定的 `mode` 有關，後面會再說明 `mode` 。
    * 如果 `Timer` 的 `mode` 不是現在當前 `run loop` 正在跑的 `mode`，它是不會被啟動的，而且如果 `timer` 在 `run loop` 執行到一半時才被觸發，則 `timer` 還需要等到下一次 `run loop` 執行才會觸發執行，假設最壞的情況這個 `run loop` 不在執行，那麼 `timer` 也不會被觸發了。

這邊附上一個經典的例子，你的 `timer` 假如沒有使用正確，可能在 `UIScrollView` 被觸發時，你的時間就會暫停，解法在這 [StackOverflow](https://stackoverflow.com/questions/605027/uiscrollview-pauses-nstimer-until-scrolling-finishes) ，主要就是 ：

```swift
timer = Timer.scheduledTimer(timeInterval: 0.1,
                             target: self,
                             selector: aSelector,
                             userInfo: nil,
                             repeats: true)

RunLoop.main.add(timer, forMode: .common)
```

有關配置 `timer sources` 的更多資訊，可參考 [Configuring Timer Sources](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW6)。
有關參考資訊，可參見 [NSTimer Coass Reference](https://developer.apple.com/documentation/foundation/timer) 或 [CFRunLoopTimer Reference](https://developer.apple.com/documentation/corefoundation/cfrunlooptimer-rhk)。


---

## Run Loop Modes
* 主要是要監視 `input sources` 以及 `timer sources` ，以及通知 `run loop observers` 的集合。
* 每個 `run loop` 都要指定一種 `mode` 。
* 在 `run loop` 整個過程中，只有監視這個 `mode` 有關聯的 `sources` ，才可以傳遞事件。

那麼 Apple 定義了 5 種 `run loop mode`
* Default : 大多數情境都很適用。
    * [NSDefaultRunLoopMode(Cocoa)](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/TypesAndConstants/FoundationTypesConstants/Description.html#//apple_ref/c/data/NSDefaultRunLoopMode)
    * [kCFRunLoopDefaultMode (Core Foundation)](https://developer.apple.com/documentation/corefoundation/kcfrunloopdefaultmode)
* Connection : `Cocoa` 將此 `mode` 與 `NSConnection` 物件結合使用來監視響應。很少需要自己使用這種模式。
    * [NSConnectionReplyMode(Cocoa)](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/TypesAndConstants/FoundationTypesConstants/Description.html#//apple_ref/c/data/NSConnectionReplyMode)
* Modal : `Cocoa` 使用此 `mode` 來識別 `modal panel`（模式面板）的事件。
    * [NSModalPanelRunLoopMode(Cocoa)](https://developer.apple.com/documentation/appkit/nsmodalpanelrunloopmode)
* Event tracking : `Cocoa` 使用此 `mode` 來追蹤使用者的互動事件，像是游標拖動。
    * [NSEventTrackingRunLoopMode(Cocoa)](https://developer.apple.com/documentation/appkit/nseventtrackingrunloopmode)
* Common modes : 這是一種組合模式，仔細看可以發現它是 `Modes` 而不是 `Mode`，是一個模式的集合（例如包括： `default, modal, event tracking` ）。
    * [NSRunLoopCommonModes(Cocoa)](https://developer.apple.com/documentation/foundation/runloop/mode/1408609-common)
    * [kCFRunLoopCommonModes (Core Foundation)](https://developer.apple.com/documentation/corefoundation/kcfrunloopcommonmodes)


## Run Loop Observers

主要就是 `Run loop` 的行為觀察者。通常 `sources` 都是在同步或非同步的事件發生時被觸發，而 `run loop observer` 是在 `run loop` 執行時觸發的。我們可以透過 `run loop observers` 來處理一些 `thread` 尚待處理的事件，或者在 `thread sleep` 之前，在這個 `thread` 上做一些事。`run loop observers` 可以觀察到下面的六個事件：
* The entrance to the run loop.
    * 剛進入 `Run loop` 時
* When the run loop is about to process a timer. 
    * 當 `Run loop` 即將處理 `timer` 時
* When the run loop is about to process an input source. 
    * 當 `Run loop` 即將處理 `input source` 時
* When the run loop is about to go to sleep.
    * 當 `Run loop` 即將進入睡眠狀態時
* When the run loop has woken up, but before it has processed the event that woke it up.
    * 當 `Run loop` 喚醒時，但在它處理喚醒它的事件之前
* The exit from the run loop.
    * 當 `Run loop` 退出時

我們可以使用 `Core Foundation` 向 App 新增 `run loop observers` ，新增的過程需要先使用 [CFRunLoopObserverRef](https://developer.apple.com/documentation/corefoundation/cfrunloopobserver) ，因為它可以幫我們追蹤我們自定義的 `callback` 。

和我們前講的 `Timer` 很類似，可以只執行一次或者不停重複。

更多如何建立 `run-loop observer` 可參考 [Configuring the Run Loop](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW18) 以及 [CFRunLoopObserver Reference](https://developer.apple.com/documentation/corefoundation/cfrunloopobserver) 。


## The Run Loop Sequence of Events

每次執行 `run loop` 時，`thread` 上的 `run loop` 待處理事件就會被開始處理，並生成 `observers` 的通知，執行順序如下：

1. 通知 `observers` 已進入 `run loop`。
2. 通知 `observers` 準備就緒的 `timers` 即將觸發。
3. 通知 `observers` 準備就緒的 `not port based input sources` 都即將觸發。
4. 觸發所有 `non-port-based input sources`。
5. 如果 `port-based input source` 已準備好並等待啟動，就立即處理事件。 **轉到步驟 9。**
6. 通知 `observers` ， `thread` 即將進入休眠狀態。
7. `thread` 進入休眠狀態，直到發生以下事件之一：
    * `port-based input source` 的事件到達。
    * `timers` 觸發。
    * 為 `run loop` 設定的超時時間已到。
    * `run loop` 被手動喚醒。
8. 通知 `observer`，`thread` 剛剛被喚醒。
9. 處理待辦事件。
    * 如果觸發了使用者定義的 `timer`，處理 `timer` 事件並重新啟動迴圈。**轉到步驟2。**
    * 如果觸發了 `input source`，傳遞事件。
    * 如果 `run loop` 被明確喚醒但尚未超時，重新啟動迴圈。**轉到步驟2。**
10. 通知 `observer`，`run loop` 已退出。

因为 `timer` 和 `input sources` 的通知是在事件被觸發之前，所以這之間有一段間隔時間。如果想在這段時間做一些事情，可以使用 `sleep` 和 `awake-from-sleep` 通知來協助獲得這段時間。

## 常見的使用情境
* `AutoreleasePool` : 監聽 `kCFRunLoopEntry` 事件，保證在 callback 之前建立 `autoreleasePool` ；還有監聽 `BeforeWaiting` 事件，是放掉舊的 `threads` ，建立新的 `threads`；監聽 `kCFRunLoopExit` 事件，保證讓 `thread` 會被釋放。
* 事件響應 : 像是透過 `port-based input source` 來監聽用戶事件，像是 `UIControl` 。
* 手勢識別 : 監聽 `kCFRunLoopBeforeWaiting` 事件，在事件 callback 中標記待處理的手勢，並處理手勢。
* UI 更新 : `setNeedsLayout` 和 `setNeedsDisplay` 都會將 view 標記待處理，等下次 `run loop` 的時候就可以 layout UI。
* 定時器 : 就是我們常用的 `Timer` (`CFRunLoopTimerRef`)。
* `Perform Selector` : 將方法放到某個 `thread` 中，在下次 `run loop` 執行。
* `GCD (Grand Central Dispatch)` : `DispatchQueue.main.async{ ... }` ，就是向 `main thread` 發送消息，喚醒 `run loop` 並執行。

## 總結
* 什麼是 `Run loop` ？
就是一種管理要處理的事件和消息的循環。

* `Run loop` 的生命週期
  1. 通知 `observers` 已進入 `run loop`。
  2. 通知 `observers` 準備就緒的 `timers` 即將觸發
  3. 通知 `observers` 準備就緒的 `not port based input sources` 都即將觸發。
  4. 觸發所有 `non-port-based input sources`。
  5. 如果 `port-based input source` 已準備好並等待啟動，就立即處理事件。**轉到步驟 9。**
  6. 通知 `observers` ， `thread` 即將進入休眠狀態。
  7. `thread` 進入休眠狀態。
  8. 通知 `observer`，`thread` 剛剛被喚醒。
  9. 處理待辦事件。
  10. 通知 `observer`，`run loop` 已退出。

* `Run loop` 基本的組成是由 `Mode` ，`Mode` 分成五種:
1. `Default` 
2. `Connection`
3. `Modal`
4. `Event tracking`
5. `Common modes`
 
`Mode` 是 `Input source` , `Timer` 以及 `Observer` 的集合，每種 `mode` 對應一個或多個接受消息的方式 。
`Run loop` 處理事件是根據 `mode` 進行，而不是 `event type` 。

* `Sources` 類型
1. `Input source` : `Port-Based Sources` 、 `Custom Input Sources` 以及 `Cocoa Perform Selector Sources`
2. `Timer source` : `Timer`
3. `Observers` : 可觀察到:
    * 剛進入 `Run loop` 時
    * 當 `Run loop` 即將處理 `timer` 時
    * 當 `Run loop` 即將處理 `input Source` 時
    * 當 `Run loop` 即將進入睡眠狀態時
    * 當 `Run loop` 喚醒時，但在它處理喚醒它的事件之前
    * 當 `Run loop` 退出時

---

首先恭喜你看到這裡了，在第一次研究 `run loop` 的時候可能會像我一樣，被一堆專業名詞給嚇到，然後閱讀起來不太順，但重複看了兩三次後就會大概有概念了，內文若有不對的地方再請不吝嗇的提供指教，謝謝！

---

## 參考了
* https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html
* https://suelan.github.io/2021/02/13/20210213-dive-into-runloop-ios/
* https://hit-alibaba.github.io/interview/iOS/ObjC-Basic/Runloop.html
* https://juejin.cn/post/6844904110991360008
* https://juejin.cn/post/6844903450585595911
* https://prafullkumar77.medium.com/ios-run-loop-what-why-when-7febead400b7
* https://www.jianshu.com/p/fd072624bea1
