# Write Swift macros

![New Zealand — Deer Park Heights Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/IMG_8058.jpg?raw=true)

New Zealand — Deer Park Heights Queenstown

觀看 WWDC23 Write Swift macros 筆記

## Overview

Apple 一開始提出一個範例，把計算的過程顯示成字串，用一個 tuple 組合起來，但這種方式很明顯，有個大缺點，就是容易有人為疏失，導致錯誤發生，還無法用 compiler 檢查字串是否相等於左邊的算數。

![截圖 2024-12-06 16.07.24.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_16.07.24.png?raw=true?raw=true)

但在 Swift 5.9 中，我們可以定義一個 `stringify` 的 macro 來簡化這過程，這個 macro 也在 Xcode 的模板中。

![截圖 2024-12-06 16.10.04.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_16.10.04.png?raw=true)

我們來單獨看一下這個 macro 的定義

![截圖 2024-12-06 16.17.35.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_16.17.35.png?raw=true)

它很像一個普通 function，可以接受一個整數作為輸入參數，並輸出一個 tuple 結果。

像我們這樣定義型別都很清楚，就可以使用的很安心。

![截圖 2024-12-06 16.19.03.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_16.19.03.png?raw=true)

Swift 與 C 語言的 macro 不同，C 語言的會在型別檢查之前的預處理器階段進行評估。

Swift 的都是用我們熟悉的的函數功能，例如可以讓 macro 變成 generic 的。

要注意的是，這個 macro 使用 `@fresstanding` 的表達式角色聲明的，這表示開發者可以在任何可以使用表達式的地方使用這個 macro，並且將以井字號標示，就像 `#stringify` 。

![Screenshot 2025-01-06 at 10.42.52.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_10.42.52.png?raw=true)

還有另外一種 macro，是可以增強聲明的 attached macro，稍後會再詳細介紹。

### Macro plug-in

再來讓我們瞭解其工作原理，先把重點關注在 freestanding 的 macro，每個 macro 的執行就是會把程式碼 expansion（展開），過程會先在 compiler plug-in 中定義如何實現，然後 compiler 會將整個 macro expression（表達式）的原始碼發送到這個 plug-in。

那這個 plug-in 要做的第一件事，就是將 macro 的源程式碼解析為 SwiftSyntax tree，而這個 tree 是 macro 的原程式碼，精確的結構表示，這就是執行 macro 的基礎。

![截圖 2024-12-06 17.27.26.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_17.27.26.png?raw=true)

像裡面有 `identifier` 就是該 macro expression  的名稱 `stringify` 。

而 Swift macro 強大的地方在於， macro 的實現本身就是一個用 Swift 編寫的程式，可以對 syntax tree (語法樹)進行任何轉換。

在這個範例，它生成了我們之前看到的 tuple，把生成的 syntax tree 序列化為原始碼，並將其發送給 compiler，然後會用 expansion （展開）的程式碼替換為 macro experssion（表達式）圖片的右上角 `(2 + 3, "2 + 3")`。

![截圖 2024-12-06 17.41.46.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/%25E6%2588%25AA%25E5%259C%2596_2024-12-06_17.41.46.png?raw=true)

## Create a marco

我這邊使用的是 Xcode 16.2

打開 Xcode → File → New → Package 

![Screenshot 2025-01-06 at 11.14.52.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_11.14.52.png?raw=true)

選擇 Swift Macro 的模板

![Screenshot 2025-01-06 at 11.15.36.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_11.15.36.png?raw=true)

![Screenshot 2025-01-06 at 11.16.19.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_11.16.19.png?raw=true)

最後在自行命名，這邊我使用 WWDC，和影片一樣，然後我們打開 WWDCClient 資料夾的 main.swift

![Screenshot 2025-01-06 at 13.49.31.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_13.49.31.png?raw=true)

可以對這個 macro 按右鍵 Expand Macro

![Screenshot 2025-01-06 at 13.52.58.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_13.52.58.png?raw=true)

![Screenshot 2025-01-06 at 13.53.32.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_13.53.32.png?raw=true)

這跟之前看到的結果一樣

再來我們可以看他是如何定義的，一樣右鍵 Jump to Definition

![Screenshot 2025-01-06 at 14.02.41.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.02.41.png?raw=true)

跟之前介紹的稍微不太一樣，這邊使用 generic 的方式，接收任何型別的 T。

他是被宣告成 `#externalMacro` ，這告訴 compiler 在執行展開時，它需要查看 WWDCMacros module 中的 `StringifyMacro` 類型。

那這個類型是如何定義的？我們可以用快速開啟的方式，查找它

![Screenshot 2025-01-06 at 14.08.42.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.08.42.png?raw=true)

![Screenshot 2025-01-06 at 14.07.52.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.07.52.png?raw=true)

![Screenshot 2025-01-06 at 14.09.47.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.09.47.png?raw=true)

因為 stringify 是一個 freestanding 的 expression macro ，所以在這我們可以看到 StringifyMacro 是要遵守 `ExpressionMacro` protocol，這個 protocol 有一個要求就是實作 `expansion(of:in:)` 。

![Screenshot 2025-01-06 at 14.16.54.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.16.54.png?raw=true)

- `of node: **some** FreestandingMacroExpansionSyntax` 語法樹參數
- `in context: **some** MacroExpansionContext` 用於與 compiler 溝通上下文的參數
- `ExprSyntax` 重寫的表達式語法

那麼在實現中都做什麼？

![Screenshot 2025-01-06 at 14.22.55.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_14.22.55.png?raw=true)

首先，檢索 macro 表達式的單個參數，這個參數一定存在，因為 stringify 被設計接受單個參數傳入，並且在 macro 展開之前，所有參數都要進行型別檢查。

最後在 return 時，使用 string 插值來建立 tuple 的語法樹，`return "(\(argument), \(literal: argument.description))"` ，第一個元素 (argument )是參數本身，第二個元素 (argument.description) 是一個包含參數原始碼的文字。

但要注意的是，在這裡並不是返回我們平常使用的 String，這是返回一個 expression syntax 表達式語法，這個 macro 將自動調用 Swift 解析器，將這個 literal 文字轉換成語法樹。

再來我們需要講講測試，我們的專案裡已經有這個測試檔案了

![Screenshot 2025-01-06 at 16.14.30.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_16.14.30.png?raw=true)

這邊的測試會使用 `assertMacroExpansion` function，來驗證 `stringify` macro 是否正確展開。

![Screenshot 2025-01-06 at 16.12.10.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_16.12.10.png?raw=true)

### Swift macro template

- Macro declaration defines the macro’s signature

我們在其中看到了基本編譯分塊，會有不同的 module 不同的 type，還有 macro role。

- Implementation operates on SwiftSyntax trees

Compiler 插件執行展開操作，它本身是一個用 Swift 編寫的程式，並且在 SwiftSyntax 上執行。

- Easy to test

我們還看到他非常容易進行測試，因為它們是語法樹的確定性轉換，而且語法樹的原始碼很容易比較。

## Macro roles

![Screenshot 2025-01-06 at 16.41.21.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_16.41.21.png?raw=true)

簡單說明一下

`@freestanding` 使用 `#` 開頭

`@attached` 使用 `@` 開頭

其他細節可參考 [WWDC23 Expand on Swift macros](https://developer.apple.com/videos/play/wwdc2023/10167/)

但這邊特別介紹一下 `@attached(member)` ，下面是一個滑雪相關 app，會讓初學者找到適合的坡道

![Screenshot 2025-01-06 at 16.47.03.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_16.47.03.png?raw=true)

我們定義了一個通用的 `enum Slope` ，包含所有坡道，簡單到困難的，另外還有一個只有簡單坡道的 `enum EasySlope` ，雖然這樣很好提供了型別安全性，但很重複，像是如果想要再新增一個簡單的坡道，就要動到這兩個 enum，還有其中的 initialization 及 computed property。

我們來試試看使用 macro 來改進此情況，目標是自動生成 initialization 及 computed property。

### The Plan

- Declare attached member macro
- Create empty macro implementation
- Create test case
- Write macro implementation
- Integrate macro into app

接著我們用剛剛的專案來改，可以把 stringify 相關的東西給刪除。

我們先在 WWDC.swift 的檔案定義 attached member macro。

```swift
/// Defines a subset of the `Slope` enum
///
/// Generates two members:
///  - An initializer that converts a `Slope` to this type if the slope is
///    declared in this subset, otherwise returns `nil`
///  - A computed property `slope` to convert this type to a `Slope`
///
/// - Important: All enum cases declared in this macro must also exist in the
///              `Slope` enum.
@attached(member, names: named(init))
public macro SlopeSubset() = #externalMacro(module: "WWDCMacros", type: "SlopeSubsetMacro")
```

再來我們到 WWDCMacro.swift 的檔案定義 SlopeSubsetMacro，我們這邊刻意先讓他返回一個空陣列，因為我們要先來寫測試。

```swift
/// Implementation of the `SlopeSubset` macro.
public struct SlopeSubsetMacro: MemberMacro {
    public static func expansion(
        of attribute: AttributeSyntax,
        providingMembersOf declaration: some DeclGroupSyntax,
        in context: some MacroExpansionContext
    ) throws -> [DeclSyntax] {
        return []
    }
}
```

可是我們需要讓 SlopeSubset 在 compiler 中可見，所以還要新增

```swift
@main
struct WWDCPlugin: CompilerPlugin {
    let providingMacros: [Macro.Type] = [
        SlopeSubsetMacro.self
    ]
}
```

再來回到 WWDCTests.swift 的檔案，開始新增測試

```swift
let testMacros: [String: Macro.Type] = [
    "SlopeSubset" : SlopeSubsetMacro.self,
]

final class WWDCTests: XCTestCase {
    func testSlopeSubset() {
        assertMacroExpansion(
            """
            @SlopeSubset
            enum EasySlope {
                case beginnersParadise
                case practiceRun
            }
            """, 
            expandedSource: """

            enum EasySlope {
                case beginnersParadise
                case practiceRun
            }
            """, 
            macros: testMacros
        )
    }
}
```

我們的 macro 什麼都沒有做，先測試是否可以執行，以上面的程式碼測試結果，是會通過。

接下來我們把預期的結果給貼上去，最後會變成這樣

```swift
let testMacros: [String: Macro.Type] = [
    "SlopeSubset" : SlopeSubsetMacro.self,
]

final class WWDCTests: XCTestCase {
    func testSlopeSubset() {
        assertMacroExpansion(
            """
            @SlopeSubset
            enum EasySlope {
                case beginnersParadise
                case practiceRun
            }
            """, 
            expandedSource: """

            enum EasySlope {
                case beginnersParadise
                case practiceRun

                init?(_ slope: Slope) {
                    switch slope {
                    case .beginnersParadise:
                        self = .beginnersParadise
                    case .practiceRun:
                        self = .practiceRun
                    default:
                        return nil
                    }
                }
            }
            """, 
            macros: testMacros
        )
    }
}
```

在執行一次測試，是失敗的，很合理，因為我們還沒有實作 macro 的 init。

![Screenshot 2025-01-06 at 18.23.59.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_18.23.59.png?raw=true)

接下來我們回到 WWDCMacro.swift 開始實作

我們先確定我們使用 macro 的人，是不是 enum

```swift
import SwiftCompilerPlugin
import SwiftSyntax
import SwiftSyntaxBuilder
import SwiftSyntaxMacros

/// Implementation of the `SlopeSubset` macro.
public struct SlopeSubsetMacro: MemberMacro {
    public static func expansion(
        of attribute: AttributeSyntax,
        providingMembersOf declaration: some DeclGroupSyntax,
        in context: some MacroExpansionContext
    ) throws -> [DeclSyntax] {
        guard let enumDecl = declaration.as(EnumDeclSyntax.self) else {
            // TODO: Emit an error here
            return []
        }
        return []
    }
}

@main
struct WWDCPlugin: CompilerPlugin {
    let providingMacros: [Macro.Type] = [
        SlopeSubsetMacro.self
    ]
}
```

再來我們需要獲取 enum 聲明的所有元素，在此之前我們可以檢查 SwiftSyntax 樹中，enum 的語法結構，由於 macro 的實現只是一個普通的 Swift 程式，我們可以用 Xcode 中所有熟悉的工具，例如我們可以在展開函數內設置斷點，並執行測試時觸發該斷點

![Screenshot 2025-01-06 at 18.33.14.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_18.33.14.png?raw=true)

我們可以在 Debug console 那輸入 `po enumDecl` ，讓我們檢查一下輸出

![Screenshot 2025-01-06 at 18.34.01.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_18.34.01.png?raw=true)

接下來我們來看看這語法樹的內容，最裡面的節點是 enum 元素，就是 `beginnersParadise` 及 `practiceRun` 。

![Screenshot 2025-01-06 at 18.38.32.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-06_at_18.38.32.png?raw=true)

為了獲取它們，需要按照語法樹中給出的結構進行操作，所以讓我們逐步瀏覽該結構，並在過程中編寫訪問的 code。

在 enum 的聲明中有一個名為 `memberBlock` 的子級，該子級裡有一個 `leftBrace` ，它代表左右括弧，所以最底下還有一個 `rightBrace`，再來是一個 `members` ，因此要訪問 member 的話，要先從 `enumDecl.memberBlock.members` 開始，所以 code 就是

```swift
let members = enumDecl.memberBlock.members
let caseDecls = members.compactMap { $0.decl.as(EnumCaseDeclSyntax.self) }
let elements = caseDecls.flatMap { $0.elements }
```

這樣我們就可以拿到 enum 的所有 case ，再來就是自動生成 initialization 

![Screenshot 2025-01-07 at 10.23.04.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_10.23.04.png?raw=true)

我們關鍵要處理的就是一個 switch case，所以我們需要處理這些 case，然後創建語法節點，要查找創建語法節點有兩個好方法，除了剛剛用 po 的方式，過來就是閱讀 [SwiftSyntax 的文件](https://swiftpackageindex.com/swiftlang/swift-syntax/main/documentation/swiftsyntax)。

我們先建立一個 InitializerDeclSyntax，其實有點像我們平常寫 code 一樣，只是關鍵在我們要用一堆 Syntax 包裝

```swift
let initializer = try InitializerDeclSyntax("init?(_ slope: Slope)") {
		try SwitchExprSyntax("switch slope") {
				for element in elements {
						SwitchCaseSyntax(
		            """
                   case .\(element.name):
		                   self = .\(element.name)
                 """
		        )
        }
		    SwitchCaseSyntax("default: return nil")
		}
}
```

所以最後會變成

```swift
/// Implementation of the `SlopeSubset` macro.
public struct SlopeSubsetMacro: MemberMacro {
    public static func expansion(
        of attribute: AttributeSyntax,
        providingMembersOf declaration: some DeclGroupSyntax,
        in context: some MacroExpansionContext
    ) throws -> [DeclSyntax] {
        guard let enumDecl = declaration.as(EnumDeclSyntax.self) else {
            // TODO: Emit an error here
            return []
        }
        let members = enumDecl.memberBlock.members
        let caseDecls = members.compactMap {
            $0.decl.as(EnumCaseDeclSyntax.self)
        }
        let elements = caseDecls.flatMap { $0.elements }

        let initializer = try InitializerDeclSyntax("init?(_ slope: Slope)") {
            try SwitchExprSyntax("switch slope") {
                for element in elements {
                    SwitchCaseSyntax(
                        """
                        case .\(element.name):
                            self = .\(element.name)
                        """
                    )
                }
                SwitchCaseSyntax("default: return nil")
            }
        }
        return [DeclSyntax(initializer)]
    }
}
```

接下來可以執行測試看看，看看我們是否有寫正確，讓他能成功初始化，沒意外照著做，到這邊應該是會成功。

再來我們要再開一個新專案測試，導入我們寫好的這個 macro，**但注意這邊要先關閉掉原本 macro 的專案**，然後再要測試 macro 的專案導入，沒有關閉的話會導入後不能使用。

開新專案後，這邊專案命名我用 TestMacro，然後到 Package Dependencies，按下加號

![Screenshot 2025-01-07 at 16.13.42.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.13.42.png?raw=true)

點選 Add Local…，選擇剛剛做的 WWDC macro 資料夾，然後加入

![Screenshot 2025-01-07 at 16.14.47.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.14.47.png?raw=true)

![Screenshot 2025-01-07 at 16.15.51.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.15.51.png?raw=true)

再來找個新增檔案 Slope.swift ，然後貼上下面程式碼，這是主要要測試的內容

```swift
import WWDC

enum Slope {
    case beginnersParadise
    case practiceRun
    case livingRoom
    case olympicRun
    case blackBeauty
}

enum EasySlope {
    case beginnersParadise
    case practiceRun
    
    init?(_ slope: Slope) {
        switch slope {
        case .beginnersParadise: self = .beginnersParadise
        case .practiceRun: self = .practiceRun
        default: return nil
        }
    }

    var slope: Slope {
        switch self {
        case .beginnersParadise: return .beginnersParadise
        case .practiceRun: return .practiceRun
        }
    }
}
```

首先我們在 EasySlope 中加上我們剛剛寫的 `@SlopeSubset` 然後 build code

![Screenshot 2025-01-07 at 16.28.24.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.28.24.png?raw=true)

這時 EasySlope 就出現一個錯誤，因為我們在 `@SlopeSubset` 已經做了重複的事情，只要移除掉 24 行的 `init?(_:)` 就解決了。

之後假如我們想要看這個 macro 做了什麼，只要右鍵它，並點選 Expand Macro 就可以了。

![Screenshot 2025-01-07 at 16.30.34.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.30.34.png?raw=true)

![Screenshot 2025-01-07 at 16.31.00.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.31.00.png?raw=true)

假如還想要知道更多細節，用 option 按鈕，點擊它

![Screenshot 2025-01-07 at 16.31.42.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_16.31.42.png?raw=true)

### Write Macro

透過使用 macro，能夠在不編寫重複的程式碼下，獲得 EasySlopes 的型別安全性 

- Start with Swift macro package template
- Use debugger to explore syntax node structure
- Develop macro based on test cases
- Add package to an Xcode project

## Diagnostics

假如發生在不支援的情況下，使用 macro 會發生什麼事？這時就需要發出一些錯誤訊息，告訴使用者發生什麼狀況。

像是我們寫的 `SlopeSubset` 只能用在 enum，假如用在 class 或 struct 上面就要報錯，並讓使用者發生什麼錯誤，所以我回去把原本的 TODO 補起來吧。

首先我們先寫一個 test case，把下面的 code 加到 WWDCTests

```swift
    func testSlopeSubsetOnStruct() throws {
        assertMacroExpansion(
            """
            @SlopeSubset
            struct Skier {
            }
            """,
            expandedSource: """

            struct Skier {
            }
            """,
            diagnostics: [
                DiagnosticSpec(message: "@SlopeSubset can only be applied to an enum", line: 1, column: 1)
            ],
            macros: testMacros
        )
    }
```

仔細看，我們這邊刻意讓 macro 使用在 struct 上面，所以我們不期望它會生成一個初始化，而且應該要發出一個 diagnostics，也就是一個錯誤，所以上面的程式碼執行測試後，應該會得到一個錯誤，因為我們還沒有修改那個 TODO，讓它輸出錯誤，接下來就是來調整一下，我們回到 WWDCMacro.swift

我們可以定義一個 enum Error，如下

```swift
enum SlopeSubsetError: CustomStringConvertible, Error {
    case onlyApplicableToEnum
    
    var description: String {
        switch self {
        case .onlyApplicableToEnum: return "@SlopeSubset can only be applied to an enum"
        }
    }
}
```

然後在 guard else 的 TODO 地方改成 `throw SlopeSubsetError.onlyApplicableToEnum` 

![Screenshot 2025-01-07 at 17.10.02.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_17.10.02.png?raw=true)

然後我們再重新執行一次測試，這次應該要通過，而且假如我們真的把 `@SlopeSubset` 加在 struct 上，馬上會得到 Xcode 的錯誤

![Screenshot 2025-01-07 at 17.13.23.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_17.13.23.png?raw=true)

接下來我們來優化這個 macro，讓它更通用

我們先打開 WWDC.swift，定義 macro 的地方，原本是這樣

```swift
@attached(member, names: named(init))
public macro SlopeSubset() = #externalMacro(module: "WWDCMacros", type: "SlopeSubsetMacro")
```

我們可以加入 generic 的方式，所以定義一個 `<Supertset>` ，而且他不是專屬 Slope 了，可以取一個通用一點的名稱，叫 EnumSubset，可以用右鍵點擊 SlopeSubset 然後 Refactor 和 Rename

![Screenshot 2025-01-07 at 17.22.57.png?raw=true](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Write%20Swift%20macros/Screenshot_2025-01-07_at_17.22.57.png?raw=true)

再來就是要改內容了，要讓他能使用 generic 的東西，做法可以跟之前一樣，設定斷點，然後查看 syntax tree 的節點，取得 generic 參數，code 如下，放在 `guard let enumDecl` 之後， `let members` 之前。

```swift
guard let supersetType = attribute
		.attributeName.as(IdentifierTypeSyntax.self)?
		.genericArgumentClause?
    .arguments.first?
    .argument else {
    // TODO: Handle error
    return []
}
```

然後把 `let initializer` 那句做調整

```swift
let initializer = try InitializerDeclSyntax("init?(_ slope: \(supersetType))") {
```

雖然還很多細節沒調整完，例如文件註解，但我們可以先測試一下 test case 是否正常，所以回到測試，因為我們改成 generic 的，所以寫法要改成 `@EnumSubset<Slope>` ，沒出狀況的話，這邊是要測試能通過的。

## Summary

- Start with macro package template

要建立 macro 可以從 macro template 開始，裡面還有一個 `stringify` macro，是一個好範例。

- Write test cases

在開發 macro 的時候，強烈建議寫測試

- Print syntax tree in debugger

善用斷點，在 expansion function 中打印 syntax tree， 會更知道怎麼寫 code

- Write custom error messages

也要多寫 error message，假如遇到 macro 不適用的情況

## Resources

- https://developer.apple.com/videos/play/wwdc2023/10166