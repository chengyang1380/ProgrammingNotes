# What’s new in Swift | Apple

![New Zealand — Deer Park Heights Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/IMG_8215.jpg?raw=true)

New Zealand — Deer Park Heights Queenstown

## Swift over the years

![Screenshot 2025-06-12 at 14.01.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.01.22.png?raw=true)

因為從 Swift 2 到 Swift 3，因為 Xcode 的關係，導致用戶一定要被迫升級 Swift 3，而且那次是大改版很難升級，但到 Swift 4 之後，Apple 團隊之後的解法是讓 Compiler 可以支持多種版本，不用被迫一次升級全部的 Swift 程式碼，所以一個專案內有 Swift 3 或 Swift 4 的編譯，這樣就可以逐步使用新語言，可以準備就緒後再 migrate。

Swift 5 之後，穩定了 ABI，這表示對 app 開發者來說，減少了下載大小，因為就不再需要在 App 中捆綁 Swift 標準資料庫的完整副本，所以 Swift 標準資料庫是操作系統本身的一部分，而且針對操作系統進行了優化，並在所有 Swift App 和框架中共享。 

Swift 6 提供數據競爭的安全保障，盡可能可以寫出正確的併發程式碼

## Swift project update

### community structure

swift.org/community

![Screenshot 2025-06-12 at 14.05.25.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.05.25.png?raw=true)

## Swift everywhere

### supported platforms

- Apple platforms
- Linux
    - Ubuntu
    - Amazon Linux
    - CentOS
    - Red Hat UBI
    - Fedora (🆕)
    - Debian (🆕)
- Windows

### Tools

- Xcode

**Language server**

SourceKit LSP: 適合用於 Swift 的語言服務器實現，可以讓 IDE 和編輯器整合 Swift 支持

- VSCode
- Neovim
- Emacs

### Cross compilation

![Screenshot 2025-06-12 at 14.12.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.12.56.png?raw=true)

可以在 macOS 上開發後，將程式部署到 Linux 服務器或 Container

### Static Linux SDK for Swift

Builds Linux binaries on macOS

No need to install additional packages

Built on Swift Package Manager support for SDK

### Foundation

![Screenshot 2025-06-12 at 14.19.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.19.00.png?raw=true)

swift-foundation 是開源的

### Swift Testing

它利用了 Macro 以及現代 Swift 功能，並整合併發功能，無縫整合。

他也是以開源的，兼顧了跨平台服務

![Screenshot 2025-06-12 at 14.21.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.21.16.png?raw=true)

- Declare a test function
- Customize a test’s name
- Organize test function with tags
- Parameterize test with arguments

其他細節可以參考

WWDC24 - Meet Swift Testing 

WWDC24 - Go further with Swift Testing

### Build

Implicitly built modules

![Screenshot 2025-06-12 at 14.24.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.24.57.png?raw=true)

每次 build code，都會先處理一些 module，例如 SwiftUI, SwiftDat，而且往更深層的其他 module，其他程式碼也是，這部分沒辦法併發，所以在 debug 時，他也需要建立自己的版本，第一次 po 會比較久。

### Explicitly built modules

- More parallelism in builds
- Better visibility into build steps
- Improved reliability of builds
- Faster debugger start-up

![Screenshot 2025-06-12 at 14.57.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_14.57.46.png?raw=true)

但現在改成這樣，可以加速 build code 和 debugger 速度

可以在 Xcode 中啟用 Explicitly Built Modules

![Screenshot 2025-06-12 at 15.00.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_15.00.13.png?raw=true)

更多細節可以餐靠

WWDC24 - Demystify explicitly built modules

以前 Swift 是在 apple 底下

github.com/apple/swift

github.com/apple/swift-format

github.com/apple/swift-evolution

…

但現在變成

github.com/swiftlang

並由 Swift Project 管理

## Language updates

 Swift 6 引用了一種可實現數據競爭安全性的新語言模式

### Noncopyable types

所有的 Swift 類型，不管是 reference type or value type，默認情況都是可以 copy 的，noncopyable type 會禁用這種默認情況。

這很適合在文件等，獨特的系統資源，這樣就可以避免多次寫入同一個文件，也可以防止在沒有自動清理的情況下，很容易引起的 resource leak 問題

![Screenshot 2025-06-12 at 15.12.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_15.12.03.png?raw=true)

我們可能再打開文件和初始化之間寫一些程式碼，這會終止程式，deinitializer 永遠無法執行從而導致 resource leak，我們可以使用 optional 的 init 解決這問題

![Screenshot 2025-06-12 at 15.15.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_15.15.00.png?raw=true)

在 Swift 5.10 對 noncopyable 的類型支援只有 value type，在 Swift 6 所有 generic 上下文和 Standard library 的 `optional` 等，都引入了 noncopyable 的型別支持

- Supported in all generic contexts
- Standard library support for `Optional` , `Result` , `Unsafe pointers`

更多細節請參考

WWDC24 - Consume noncopyable types in Swift

除了表單獨特所有權，noncopyable 還讓我們精細控制性能，在資源嚴重受限的底層系統中，copy 成本有可能變很高，因此這情況也適合使用 noncopyable

- Express unique ownership
- Fine-grained performance control

### Embedded Swift

底層系統在內存、儲存和運行時功能方面受到限制，由於他們的佔用空間小， C 和 C++ 一直是在這類型系統上進行 coding 的首選，現在可以使用 Swift 了。

Embedded Swift 是 Swift 的新語言子集和編譯模型，他們可以生成非常小的獨立二進制文件，適用於高度受限的系統。

他會關閉某些需要運行時支持的語言功能，例如 reflection 和 “any” types，並使用編程技術，比如 full generic specialization，和靜態連結，來生成合適的二進制文件，儘管關閉了一些語言功能，但 Embedded Swift 子及，感覺還是非常接近「完成版」Swift，讓你可以輕鬆繼續寫慣用易讀的 Swift code

- New language subset
- New compilation model
- Small and standalone binaries
    - Disables features that need a runtime: reflection, “any” types
    - Special compiler techniques: full generic specialization, static linking
    - Embedded Swift is close to full Swift

借助 Embedded Swift，還可以寫小遊戲，這遊戲的二進制文件大小，超級小

![Screenshot 2025-06-12 at 15.45.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_15.45.47.png?raw=true)

**System with limited memory**

當然 Embedded Swift，不僅用於娛樂和遊戲，也可以用在構建工業應用 app

ARM and RISC-V microcontrollers

- Raspberry Pi Pico
- Espressif ESP32
- STMicroelectronics STM32
- Nordic Semiconductor nRF52

Apple 的 Secure Enclave （安全隔區處理器）就使用 Embedded Swift

他是獨立於主處理器的獨立子系統

- Brings memory safety to embedded systems
- Incremental adoption with Swift’s interoperability

更多細節可參考

WWDC24 - Go small with Embedded Swift

### C++ interoperability

去年推出了與 C++ 的雙向互操作性，Swift 可以直接與 C++ 進行互相操作，目前還在不斷擴充

- Virtual methods
- Default arguments
- C++ move-only types

`std::map` , `std::set` , `std::optional` , `std::chrono::duration` 

- Incrementally adopt Swift in C++ projects
- Improved security and productivity

其他細節可以參考去年的 

WWDC23 - Mix Swift and C++

### Typed throws

以前在 do catch 中要處理特定錯誤要這樣寫

![Screenshot 2025-06-12 at 15.57.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_15.57.58.png?raw=true)

但在 Swift 6 可以這樣做，直接指定 error type

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image.png?raw=true)

When to use typed throws

- Same module error handling
- Propagating error type in generic contexts
- Constrained environments

上述情況下，使用非 typed throws 成本過高

Swift 6 的升級點

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%201.png?raw=true)

最關鍵的是 Data-race safety 

### Data-race safety

因為我們寫併發程式 concurrent programs，很容易寫出 data race，當多個 threads 共享資料時，而其中一個 thread 試圖更改資料，很有可能就會出事。 

Data race 的問題，一直是 Swift concurrent 的主要目標

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%202.png?raw=true)

用於保護可變狀態的 `Actor` 以及實現安全資料共享的 `Sendable`，到 Swift 5.10 借助完整的 Concurrency 檢查標誌，實現了 data race 的安全性，所以到 Swift 6 已經默認提供 data race 的安全保障，在編譯，在編譯時就會檢查

後續我們也可以逐步的將 Module 升級至 Swift 6，不用一次全部

![Screenshot 2025-06-12 at 16.12.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/Screenshot_2025-06-12_at_16.12.46.png?raw=true)

禁止跨越 Actor 隔離邊界傳遞所有非 Sendable 的值，Swift 6 可以識別出，在非 Sendable 的值無法在從原始隔離邊界進行引用的情況下傳遞他們是安全的。

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%203.png?raw=true)

如果使用 Swift 5.10 的完整併發檢查，將非 Sendable `client` 引用從 MainActor 傳遞到 `ClientStore` Actor 將會導致 compiler 警告，但現在因為這個變數 `client` 不會在兩邊互相傳遞，就不會被檢查出錯誤。

可是把 `client` 引用傳遞給 `ClientStore` Actor 之後又將它引入 `logger` ，這時候就會報錯，表明是一個 data race。

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%204.png?raw=true)

Swift 6 除了 Actor 提供的高級同步功能，還有其他新的底層東西

**`import Synchronization`** 

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%205.png?raw=true)

 

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/What’s%20new%20in%20Swift/image%206.png?raw=true)

**Mutex**，和 Atomic 一樣，也應該存在 ‘**let**’ 屬性中，假如要訪問 Mutex 屬性，都要透過 `withLock` 方法的閉包，從而確保實現互斥訪問。

### Road to data-race safety

- Incremental migration
- Improved data-race checking
- New low-level synchronization primitives

其他細節可參考

WWDC24 - Migrate your app to Swift 6

## Wrap-up

- Language improvements
- Data-race safety
- Swift everywhere

## Resources

https://developer.apple.com/wwdc24/10136

https://youtu.be/17fZZ1v_N2U?si=8J5byKhPdmYDEiFR