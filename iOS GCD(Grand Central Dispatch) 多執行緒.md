# 簡單聊聊 iOS 多執行緒 多線程 GCD (Grand Central Dispatch)

## 前言
在目前的工作上，其實對於 iOS GCD 的操作不多，有時候可能頂多會用到 
[asyncAfter(deadline:execute:)](https://developer.apple.com/documentation/dispatch/dispatchqueue/2300020-asyncafter)
``` swift

DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
    // do something...			
}
```
目前接觸到非同步問題，大多都依賴 [Bolts](https://github.com/BoltsFramework/Bolts-Swift) 這個套件（類似 [PromiseKit](https://github.com/mxcl/PromiseKit) )來完成這種非同步的問題。
最常見的情境就是：例如可能有 A, B, C 三個 Task ，C 需要等 A 跟 B 都完成，才能做 C。
在 `Bolts` 上大概會長這樣
```swift
let task: [Task<String>] = [A, B]
// 看需求情境決定 task 要在哪個 Executor (執行緒上做) e.g., immediate, mainThread and queue(DispatchQueue)...
Task.whenAllResult(task).continueOnSuccessWithTask(.queue(.global())) { [weak self] strings -> Task<Void> in
     return C
}.continueWithTask(.mainThread) { [weak self] (task) -> Task<Void> in
     // do something...
}
```

近期發現對於 iOS GCD (Grand Central Dispatch) 好像只懂基礎概念，應該還需要深入了解，所以藉此機會好好重新認識它。

## 什麼是 GCD (Grand Central Dispatch)?

簡單來說就是想完成以下幾件事情
* 我們有時候想要把任務們集中起來管理，像是串接起來， A 做完 B 才能接著做，尤其是一些非同步的任務
* 我們任務很多時可以利用 `CPU` 來把任務分給好幾個 `Thread` 來做，這樣就更有效率
* 我們有時候一些不重要的任務，可以開其他 `Thread` 來執行

那麼這時 Apple 就把這些事封裝成好用的工具 `GCD (Grand Central Dispatch)` 來供我們使用。
瞭解這些使用原因後，後續就會更好理解！

## 基本名詞解釋

* **Thread**
首先是執行緒 `Thread` ，可以說是最核心的東西，它由裝置上的 CPU 來決定數量，可以想像它像是工廠裡的流水線，有越多條流水線，處理事情的速度就可以越快，但是我們是無法直接操控它。

* **DispatchQueue**
這是在 GCD 中最常見的東西，可以想像它是一個可以接收任務的容器(就是佇列)，然後有 `First in, first out (FIFO)` (先進先出) 的特性，有三種類型

> Main queue

> Global queue

> Custom queue

並且可以 `Serial` (串行) 或 `Concurrent` (併發)，那不管是在主執行緒或背景執行緒上，一個 `Queue` (佇列)是一個可 `Sync` (同步) 或 `Async` (非同步) 執行的 `Closure` 代碼塊，當 `Queue` (佇列)生成後，它會被自動分配到 CPU 的任一核心上(某個 `Thread`)，多個 `Queue` (佇列) 同樣會被相對應管理，這部分我們開發者就不用處理了，因為 iOS 上有一個 `a pool of threads` (執行緒池)，它是除了主執行緒以外的執行緒集合，系統會挑選一個以上的執行緒來使用，依照你建立的佇列數量，以及建立方式。


* **Main Queue**
是 `Serial` (串行)，系統的主線程，更新 UI `必定` 要在這，所以很常看到類似的 code
```swift
DispatchQueue.main.async {
    // update UI...
}
```
我們在使用 `Main Queue` 時要格外小心，它應該要長期維持在閒置可用的狀態，這樣才可以應付使用者操作帶來的介面變動需求。


* **Global queue**
是 `Concurrent` (併發)，適合放非 UI 相關任務在這，這就是系統的併發的隊列
```swift
DispatchQueue.global(qos: .userInitiated)
```
這裡的 `qos` 有分優先等級
從最高至最低 
* `userInteractive`
* `userInitiated`
* `default` 
* `utility`
* `background` 
* `unspecified`

那當然也不是所有的任務都用最高優先度 (`.userInteractive`) 最好 ，用多了也會造成 app 資源不足的問題。

* **Custom queue**
預設為 `Serial` (串行)，但也可以指定為 `Concurrent` (併發)，這是我們自己創建的 queue
```swift
// label 通常都是 bundle name (apple 教學推薦)，最好唯一碼
DispatchQueue(label: "com.ccy.testGCD", attributes: .concurrent)
```


* **Serial (串行)**
表示多個任務是串接在一起，佇列裡的任務會按照順序完成，聽起來很沒有效率，因為 A 任務完成 B 才能才能開始執行，但這樣的特性也帶來安全性，可以避免競爭危害 (Race condition)，有些資料就可以安全的共享。


* **Concurrent (併發)**
跟 `Serial` (串行）是相反的，等同所有任務一起去執行，不用誰等誰，是比較有效率的，但是任務因為沒有串再一起，所以各任務拿到結果的時間就會比較難預測。


* **Synchronous (同步)**
表示等它完成任務 (function) 完成後才返回，所以使用不當的話，可能會造成執行緒堵住或 deadlock ，像是 B 一直在等 A 完成才能做，但是 A 一直沒有完成，這時候就永遠不會結束，可以想像跟打電話一樣，當你只有一台手機，然後撥打電話給朋友時，你不結束通話就無法再打給其他人。


* **Asynchronous (非同步)**
跟 `Synchronous` (同步)相反，表示它不用等前面任務好了才可以下一個任務，所以他不會造成執行緒堵住的問題，因為會開啟新的線程 (`Thread`)，用剛剛打電話的例子來說，這時你就變成有多台手機，然後撥打電話給朋友時，你想再打給其他人也可以了(開啟新執行緒）。

```
注意：雖然非同步 Asynchronous 有開新執行緒的能力，但不一定會開，這跟任務所指定的隊列類型(Serial, Concurrent)有關，下方會說明。

sync 與 async 和 serial 與 concurrent 是可以互相搭配使用，下面會說明，sync 可搭配 serial 也可以搭配 concurrent ，相同的 async 也都可以搭配使用，
最主要是影響是當前的 Thread 是否會阻塞。
```

所以整合再一起會變成：

* Serial Queue + Sync  => 不會開新的執行緒 `Thread`，照順序執行任務
* Serial Queue + Async => 開`一條`新的執行緒 `Thread`，照順序執行任務
* Concurrent Queue + Sync  => 不會開新的執行緒 `Thread`，照順序執行任務
* Concurrent Queue + Async => 開`多條`新的執行緒 `Thread`，同時執行任務 (**效率最好**)

介紹完這些名詞後看這張圖可能就有一點點概念

![](https://miro.medium.com/proxy/1*qrqnz__GlLozi-wn_A-wKQ.jpeg)

## 實際例子
看完了那麼多名詞介紹，沒有實際操作有時候可能還是一知半解，接下來我們就來看一點 Code。
我們首先建立一個新的專案，只需要到 `ViewController.swift` 的 `viewDidLoad` 上操作就可以了。

* Serial Queue + Sync

那我們都知道 `Serial` 可以用 `Main queue` 或者 `Custom queue` 來完成，那我先建立一個 `Custom queue` 然後使用 `Sync` ，我們故意在 `task1` 中 delay 3 秒。
```swift
func task1() {
    print("Task 1 started")
    // make task1 take longer than task2
    sleep(3)
    print("Task 1 finished")
}

func task2() {
    print("Task 2 started")
    print("Task 2 finished")
}
		
let serialQueue = DispatchQueue(label: "com.ccy.testGCD")
serialQueue.sync {
    task1()
 }
serialQueue.sync {
    task2()
}
```
Output:

```
Task 1 started
Task 1 finished
Task 2 started
Task 2 finished
```
我們在 `task1` 中刻意延遲，但是因為 `Serial` + `Sync` 的關係， `task2` 還是要等 `task1` 完成，並且因為 `Serial` 所以只有用到一個 `Thread`。

* Serial Queue + Async

那一樣上面的例子，但這次我們把 `Sync` 改為 `Async`。
Output:

```
Task 1 started
Task 1 finished
Task 2 started
Task 2 finished
```
雖然我們改用了 `Async` 但因為我們的 `Queue` 還是 `Serial` 的，所以還是需要等前面的人完成才能接著做，這裡一樣只有用到一個 `Thread`，但因為用了 `Async` 所以這裡是不會造成 `Thread` 執行緒被堵住的問題。

* Concurrent Queue + Sync

這次我們把 `Serial` 改為 `Concurrent`，在 `init` 中有參數 `attributes` 可以調整。
```swift
func task1() {
    print("Task 1 started")
    // make task1 take longer than task2
    sleep(3)
    print("Task 1 finished")
}

func task2() {
    print("Task 2 started")
    print("Task 2 finished")
}
		
let concurrentQueue = DispatchQueue(label: "com.ccy.testGCD", attributes: .concurrent)
concurrentQueue.sync {
    task1()
 }
concurrentQueue.sync {
    task2()
}
```
Output:

```
Task 1 started
Task 1 finished
Task 2 started
Task 2 finished
```
結果也是一樣照順序執行，雖然我們使用了 `Concurrent` ，但因為 `Sync` 任務還是要依序執行， `Thread` 也只用到一個。

* Concurrent Queue + Async

跟上面一樣的 Code 但把 `Sync` 改為 `Async`。
Output:

```
Task 1 started
Task 2 started
Task 2 finished
Task 1 finished
```
因為我們是 `Concurrent queue` 並且 `Async`，所以我們的任務會直接全部一次出去，而且會看到不同 `Thread` 在執行。
![image](https://cdn-images-1.medium.com/max/1600/1*o1d4aX_KMNb3ZigT4Qu6hw.png)

* DispatchQoS

我們知道 `qos` 是有優先度的， 
`userInteractive`> `userInitiated` > `default` > `utility` > `background` > `unspecified`
我們來測試看看
```swift
let queue1 = DispatchQueue.global(qos: .userInteractive)
let queue2 = DispatchQueue.global(qos: .userInitiated)
		
queue1.async {
  for i in 0...5 {
    print("queue1: \(i)")
  }
}
		
queue2.async {
  for i in 0...5 {
     print("queue2: \(i)")
  }
}
```
Output:
```
queue1: 0
queue2: 0
queue1: 1
queue2: 1
queue1: 2
queue1: 3
queue1: 4
queue1: 5
queue2: 2
queue2: 3
queue2: 4
queue2: 5
```
因為兩個的優先度其實都很高，第一與第二，而且因為 `global` (`Concurrent`) 的，所以開了不同 `Thread` 在執行，所以結果順序其實每次都不一定一樣，但能看得出來 `queue1` 會比較被優先處理完畢。

## 其他常用使用
我們接著看看其他常用的方法

* **DispatchGroup**
有了這個我們可以更好的追蹤不同 `Thread` 上的任務。想像一下我們有 A, B, C 三個任務，然後 A, B 在不同的 `Thread` 上執行， C 需要等待 A, B 都完成才能執行，這樣的情境就很適合使用 `DispatchGroup` 。
   * `DispatchGroup.enter` 
   讓 task 進入 group。

   * `DispatchGroup.leave`
   讓 task 在 group 已經執行結束。

   * `DispatchGroup.wait`
   這是會阻塞當前 `Thread` ，等待所有任務或者任務超時。
   
    * `DispatchGroup.wait(timeout:)`
   也設定 `timeout` 參數，就可以拿到 `DispatchTimeoutResult` 得知道結果，e.g., success, timedOut，但是這結果是同步的。

   * `DispatchGroup.notify`
   非同步執行，所以不會阻塞當前 `Thread`，等 group 中任務都執行完畢。

那我們來看一下實際的 Code：
```swift
let group = DispatchGroup()
		
let queue1 = DispatchQueue(label: "com.ccy.testGCD1", attributes: .concurrent)
queue1.async(group: group) {
  for i in 1...3 {
    print("queue1: \(i)")
  }
}
		
let queue2 = DispatchQueue(label: "com.ccy.testGCD2", attributes: .concurrent)
queue2.async(group: group) {
  sleep(3)
  for i in 1...3 {
    print("queue2: \(i)")
  }
}
  
group.wait()
print("wait a minute")
group.notify(queue: .main) {
  print("tasks finished")
}
```
我們首先建立了一個 `DispatchGroup()` ，然後建立兩個 `queue` 都是 `concurrent`，特別的是我們故意在 `queue2` 中加 `sleep(3)` ，刻意延遲，然後還加了 `group.wait()`。

Output:
```
queue1: 1
queue1: 2
queue1: 3
queue2: 1
queue2: 2
queue2: 3
wait a minute
tasks finished
```

然後假設我們把 `groupt.wait()` 給拿掉，在執行一次

Output:
```
wait a minute
queue1: 1
queue1: 2
queue1: 3
queue2: 1
queue2: 2
queue2: 3
tasks finished
```
這樣的結果我們可以知道
1.  `group.wait()` 是同步的，會卡住後面的 `print("wait a minute")` 。
2.  `group.notify` 是非同步執行 closure ，等到裡面 task 都結束。


* **延遲使用**
最常見就是
```swift
DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
    // do something...			
}
```
這樣就可以延遲一秒，然後 closure 才會開始執行。

## 再複習一次，看一些 Deadlock 情境
*Case 1*
```swift
print("task 1")
DispatchQueue.main.sync {
    print("task 2")
}
print("task 3")
```
執行了上面的 code 會有什麼結果勒？

output:

```
task 1
````

然後就會 Crash 了，為什麼會這樣勒？
* 首先裡面用到了 `sync` 同步，所以是會卡 `Thread` 的。
* 然後是會執行在 `Main queue` 上。
* `task 2` 是被包在 `sync` (同步)裡面

所以就等於 `task 3` 跟 `task 2` 在互相等對方，依照順序來說是 `task 1` 先被處理，再來是啟動了 `sync queue` ，這時 `task 2` 加入隊列裡，但還未執行，再來是處理 `task 3` ，所以是 `task 3` 在 `task 2` 前面 ，可是在 `task 3` 的前面有一個在 `Main thread` 同步的任務，所以又要等它完成才能繼續，這就造成了 `Deadlock` 。

假如我們把上面的 `sync` 改成 `async`

```swift
print("task 1")
DispatchQueue.main.async {
    print("task 2")
}
print("task 3")
```

output:

```
task 1
task 3
task 2
```
因為變成 `async` (非同步)的，所以就無需等它完成，就可以繼續執行。
又或者我們假如把 `main queue` 改成 `global queue`。

```swift
print("task 1")
DispatchQueue.global().sync {
    print("task 2")
}
print("task 3")
```

output:

```
task 1
task 2
task 3
```

執行了 `task 1` 後會馬上遇到一個 `sync thread` ，所以我們要等它完成才能繼續走，但是它是開了其他 `Thread` 執行，所以 `task 3` 跟 `task 2` 是會在不同的 `Thread` 執行，所以不會有像前面的例子，兩個任務互相等對方的問題。
再來我們看一個比較複雜的問題，結合了上述觀念，你應該會知道結果。

*Case 2*
```swift
let serialQueue = DispatchQueue(label: "com.ccy.testGCD") // serial
print("task 1")
serialQueue.async {
    print("task 2")
    serialQueue.sync {
        print("task 3")
    }
    print("task 4")
}
print("task 5")
```

output:

```
task 1
task 5
task 2
```

然後就 Crash 了，首先 `DispatchQueue(label:)` 是會生出 `Serial` (串行) 的隊列，再來就馬上執行了 `task 1` ，過來會遇到 `async` (非同步)的且是 `serial` 的隊列，因為是 `async` 不會卡 `thread` 所以會繼續執行，直接到 `task 5` ，然後回到 `task 2` ，執行後就遇到我們前面看到的情境一樣 `deadlock` 了， `task 3` 跟 `task 4` 又會互相等待對方。
那我們改良一下，我們把 `task 1` 之後，把本來的 `serialQueue` 改成 `async global queue` 來執行。

```swift
let serialQueue = DispatchQueue(label: "com.ccy.testGCD")
print("task 1")
DispatchQueue.global().async {
    print("task 2")
    serialQueue.sync {
        print("task 3")
    }
    print("task 4")
}
print("task 5")
```

output:

```
task 1
task 5
task 2
task 3
task 4
```

這樣就解決了剛剛 Crash 的問題，一開始執行了 `task 1` 接著遇到一個 `async global queue` ，因為它是 `async` 我們不必等它，所以會先執行到 `task 5` ，再來就到 `task 2` ，關鍵的 `serialQueue.sync` 它是同步的，所以 `task 3` 結束後才可以執行到 `task 4 `。
大家看完上述兩個 case ，相信大概對 `deadlock` 比較不陌生了，也知道最容易發生在於 `sync` (同步) 的情況，有時這些 `thread` 上互相依賴的關係下，會一層嵌一層，一不小心就太複雜，所以使用上一定要小心。



## 結語
我想看完這篇文章後應該對 iOS GCD 有基本的了解，當然可能還有更多細節的使用沒有探討到，或者你可能都是直接使用一些第三方套件來完成這些繁瑣的事情，但我希望看完後都有基本的概念。
最後感謝您看到這裡，內文有任何錯誤，歡迎討論指教，謝謝您


## 參考了
* https://franksios.medium.com/ios-gcd多執行緒的說明與應用-c69a68d01da1
* https://zhuanlan.zhihu.com/p/403841877
* https://medium.com/@crafttang/gcd%E5%92%8Coperation-operationqueue-%E7%9C%8B%E8%BF%99%E4%B8%80%E7%AF%87%E6%96%87%E7%AB%A0%E5%B0%B1%E5%A4%9F%E4%BA%86-f38d50521543
* https://medium.com/彼得潘的-swift-ios-app-開發教室/ios-30-dispatch-group-處理多個非同步操作-583fe2225a8f
* https://waynestalk.com/dispatch-queue-tutorial/
* https://bing-guo.github.io/2020/04/13/intv-ios-gcd/
* https://www.appcoda.com.tw/grand-central-dispatch/
* https://chy305chy.github.io/2018/11/29/GCD%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%EF%BC%88%E4%BA%8C%EF%BC%89%E2%80%94%E2%80%94Dispatch-Queue%E5%92%8CThread-Pool/
