# Swift Opaque and Existential Protocol Types (any & some)

![New Zealand - Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/IMG_7987.jpeg?raw=true)
New Zealand - Queenstown

# 前言

現在在 SwiftUI 上常常看到 `some` 關鍵字，每個 View 都有 `var body: some View { ... }`，大概知道它的功能就是要隱藏細節，尤其遇到型別很複雜的時候也很適合。

複雜型別例子可參考

- [官方文件 Opaque and Boxed Protocol Types](https://www.notion.so/Swift-Opaque-and-Existential-Protocol-Types-any-some-ae419520de18426cabfe0acbc26c730d?pvs=21)
- [Pofat 大大的文章](https://pofat.dev/2019/08/05/Opaque-Result-Type.html)

還有有時候定義一個 protocol 後，並且把它當作一個型別時，會需要用到 `any` ，但都沒有好好深入瞭解。

所以以下對於這兩個關鍵字 `some` `any` 研究的心得。

# Generic vs Existential

我們直接先看一段 code，我們有一個 protocol `Pet` ，分別由 `Dog` 和 `Cat` 實現

```swift
protocol Pet {
    var name: String { get }
}

struct Dog: Pet {
    var name: String { "Dog" }
}

struct Cat: Pet {
    var name: String { "Cat" }
}
```

再來有兩個 function，參數都是 `Pet` 

```swift
// generic
func callNameGeneric<T>(_ pet: T) where T: Pet {
    print("\(#function) \(pet.name)")
}

// existential
func callNameExistential(_ pet: Pet) {
    print("\(#function) \(pet.name)")
}

```

你會想優先選誰？

```swift
callNameGeneric(Dog())
callNameExistential(Cat())

// Console
// callName_generic(_:) Dog
// callName_existential(_:) Cat
```

以「功能的角度」來看，兩個都可以實現一樣的結果，而且都很通用、彈性很高。

但從「閱讀程式碼」角度來看，用 `existential` 看起來比較簡潔。

假如對於他們基本的了解的話，很容易就選擇它了，但它其實比較「耗能」的。

## Existential 效能？

細節可以參考[雪峰的 Blog](https://zxfcumtcs.github.io/) 大大的 [這篇](https://zxfcumtcs.github.io/2022/02/04/SwiftProtocol2/)，講解得非常詳細，或者參考

- [WWDC16 Understanding Swift Performance](https://developer.apple.com/videos/play/wwdc2016/416/)
- [Type Metadata](https://github.com/swiftlang/swift/blob/main/docs/ABI/TypeMetadata.rst#protocol-metadata)
- [SIL.rst - witness-tables](https://github.com/swiftlang/swift/blob/d24bc387973bcf60f5596ff387b996c1eb356456/docs/SIL.rst#witness-tables)
- [Whole-Module Optimization in Swift 3](https://www.swift.org/blog/whole-module-optimizations/)

快速講解效能的問題，先簡單介紹分配記憶體空間的方式

- Objective-C 中使用 MetaClass，Instance 透過 isa 指針引用 MetaClass
- Swift 會為各種類型 class, struct , enum, protocol 等等生成 Metadata Record
    - 而 Metadata Record 中包含 VWT (Value Witness Table) Pointer

看下方圖片比較好理解

![image](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/image.png?raw=true)
圖片出處 [https://zxfcumtcs.github.io/2022/02/04/SwiftProtocol2/](https://zxfcumtcs.github.io/2022/02/04/SwiftProtocol2/)

class, struct ,enum 對應的 instance 都有確定的「模型」來指導其內存佈局，模型就是他們本身的定義，包含他們的屬性，而 protocol 是沒有確定的「模型」，因為背後的真實類型很多種可能，那這樣 protocol 型別要怎麼內存佈局？

用了一個叫 **Existential Container** 的模型來指導 protocol 變量佈局內存。

Existential Container 分成兩種： `Opaque Existential Container` 和 `Class Existential Container`

### ***Opaque Existential Container***

沒有 class 約束的 protocol (no class constraint on protocol)，用這種類型的話，真實類型可能是 class, struct ,enum ，因此其儲存就非常複雜。

```swift
struct OpaqueExistentialContainer {
  void *fixedSizeBuffer[3];
  Metadata *type;
  WitnessTable *witnessTables[NUM_WITNESS_TABLES];
};
```

- `fixedSizeBuffer` 3個指針大小的 buffer 空間，當真實類型的 size（內存對其後的大小）小於3個字時，則其內容直接儲存在 `fixedSizeBuffer` 中，否則要在 heap 上另闢空間儲存，並將指針儲存在此。
- `type` 指向真實型別的 `Metadata` ，最重要就是引用其中的 VWT，用於各種內存操作。
- `witnessTables` 也可以稱為 Protocol Witness Table, PWT，儲存的是真實類型中對應的函數地址

### ***Class Existential Container***

用於有 class 約束的 protocol，背後真實類型只能是 class，因為是 class 所以 instance 都是在 Heap 上分配內存的。

而且只需要一個指針，指向 Heap。還有由於 class instance 含有指向其 Metadata 的指針，所以不必額外存 Metadata 的指針。

```swift
struct ClassExistentialContainer {
  HeapObject *value;
  WitnessTable *witnessTables[NUM_WITNESS_TABLES];
};
```

- `value` 指向 Heap 內存的指針
- `witness` PWD 指針

來看一個例子

![截圖 2024-08-28 15.00.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-28_15.00.31.png?raw=true)

由於這個 protocol Drawable 沒有 class constraint，所以是 `OpaqueExistentialContainer` 

- `Point` 的部分，在 instance 佔用了2個字的內存空間（小於3），所以不用額外開一個 heap 來存，反之 `Line` 就需要。
- `type` 分別指向了背後真實型別的 Metadata
- `PWT`  中的 function 指針指向真實型別中的 function

### **小結 Protocol 的部分**

- Protocol 不同於一般類型（class, struct ,enum），而是使用 Existential Container，其中有分成 `OpaqueExistentialContainer` 和 `ClassExistentialContainer` 作為內存的模型。
- 內存佔用 ≤ 3的 instance，直接儲存在 container buffer 中，否則需要在額外 heap 分配內存，並將指針存在 container buffer[0] 中。
- 內存管理 (allocating, coping, destroying) 相關方法在 VWT 中。
- 透過 PWT 實現方法動態派發 (Dynamic dispatch）。

### **Generic**

而 Generic 的話也是可以類型約束（Type Constraints），例如約束它是 class 或 protocol。

因此 Swift generic 可以分成三種

- No Constraints
    
    能做的事很少，不能執行任何方法，只能在 Stack 上為 generic 類型分配內存並執行內存拷貝
    
- Class Constraints
    
    可以在 Heap 上創建新 instance，方法調用透過虛擬函數表(vtable)實現
    
- Protocol Constraints
    
    可以在 Stack 或 Heap 上，按需求建立 generic instance，無論 value type 或 reference type。而且透過 PWT 實現方法調用。
    

所以 generic 中的方法調用都是動態派發 (dynamic dispatch），透過 vtable 或者 PWT。

這樣子，generic 和 protocol 不就一樣嗎？都是動態派發，效能照理來說就沒什麼差異了，但其實 generic 還有一個效能優化的東西 Specialization of Generics

### **Specialization of Generics**

generic 方法調用都是動態派發（透過 vtable 或 PWT）也一樣有性能的損耗。

為了優化此類型的效能損耗，Swift 編譯器會對 generic 進行特化 **Specialization of Generics**

簡單來說就是為具體型別生成相對應版本的函數，從而將 generic 轉成非 generic ，實現方法調用的靜態派發，來看一個例子

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
  let temp = a
  a = b
  b = temp
}

var a = 1
var b = 2
swapTwoValues(&a, &b)
```

例如，透過 `Int` 參數調用 `swapTwoValues` 時，編譯器就會產生該方法的 `Int` 版本，下方是透過 swiftc 命令可以生成 SIL。

SIL ([swift/SIL.rst at main · apple/swift · GitHub](https://github.com/apple/swift/blob/main/docs/SIL.rst)) 可以大致知道 Swift 背後實現原理

```swift
// specialized swapTwoValues<A>(_:_:)
sil shared [noinline] @$s4main13swapTwoValuesyyxz_xztlFSi_Tg5 : $@convention(thin) (@inout Int, @inout Int) -> () {
// %0 "a"                                         // users: %6, %4, %2
// %1 "b"                                         // users: %7, %5, %3
bb0(%0 : $*Int, %1 : $*Int):
  debug_value_addr %0 : $*Int, var, name "a", argno 1 // id: %2
  debug_value_addr %1 : $*Int, var, name "b", argno 2 // id: %3
  %4 = load %0 : $*Int                            // user: %7
  %5 = load %1 : $*Int                            // user: %6
  store %5 to %0 : $*Int                          // id: %6
  store %4 to %1 : $*Int                          // id: %7
  %8 = tuple ()                                   // user: %9
  return %8 : $()                                 // id: %9
} // end sil function '$s4main13swapTwoValuesyyxz_xztlFSi_Tg5'
```

所以簡單來說就是能用 generic 就不要用 protocol 當作型別，因為效能更佳。

### 效能小結

回歸到一開始的問題，我們知道這兩個 function 雖然可以做一樣的事情，但用「效能」的角度來看，我們知道要選誰了，但很容易無意識中寫出 `existential` 版本，所以 Swift 團隊就在 Swift 5.6 中引入了 `any` 關鍵字（https://github.com/apple/swift-evolution/blob/main/proposals/0335-existential-any.md）

- `any` 本身沒有任何功能，只是一個標記，假如 protocol 當作是型別時，並且加上 `any` 就是要要使用「Existential Type」的意圖
- 從 Swift 6.0 開始就要強制使用 `any` 了

```swift
// 原本
func callNameExistential(_ pet: Pet) {
    print("\(#function) \(pet.name)")
}

// 之後
func callNameExistential(_ pet: any Pet) {
    print("\(#function) \(pet.name)")
}
```

加上 `any` 之後效能上會比較差，有沒有 generic 的性能又有 Existential Types 的簡潔？

## `Some`

答案就是 `some` ，這個在 SwiftUI 常看到的關鍵字，以下 code 都可以達到同樣的功能，但效能不一樣。

```swift
// existential
func callNameExistential(_ pet: Pet) {
    print("\(#function) \(pet.name)")
}

// existential，但在 Swift 6.0 要加 any
func callNameExistentialWithAny(_ pet: any Pet) {
    print("\(#function) \(pet.name)")
}

// generic
func callNameGeneric<T>(_ pet: T) where T: Pet {
    print("\(#function) \(pet.name)")
}

// some
func callNameSome(_ pet: some Pet) {
    print("\(#function) \(pet.name)")
}
```

在這個情況下 `some` 和 `generic` 的效能一樣，但 `some` 更簡潔

但在當參數時，要注意不能當作多個參數，例如

```swift
// ✅
func isEqualGenerics<T>(t1: T, t2: T) -> Bool where T: Equatable {
  t1 == t2
}

// ❌
func isEqualSome(t1: some Equatable, t2: some Equatable) -> Bool {
  t1 == t2
}
```

用 `some` 還有一個優點，隱藏細節，這就像是 OOP 中的封裝，讓別人知道的越少越好，出錯的機率越低

像是我們常見的 SwiftUI

```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Text("Hello")
        }
    }
}
```

我們可以把 `some` View 改成 `VStack<Button<Text>>` 一樣可以 Compiler 可以通過

```swift
struct ContentView: View {
    var body: VStack<Text> {
        VStack {
            Text("Hello")
        }
    }
}
```

但很明顯第一種寫法，簡潔有力，假如 body 裡面的內容多複雜都沒關係

再來看一個例子，我們把 `Pet` 再加一個 property `isHungry: Bool` 

```swift
protocol Pet {
    var name: String { get }
    var isHungry: Bool { get }
}

struct Dog: Pet {
    var name: String { "Dog" }
    var isHungry: Bool { true }
}

struct Cat: Pet {
    var name: String { "Cat" }
    var isHungry: Bool { false }
}

// 這裡不能用 some，因為使用 some 表明這邊只能一種 Pet
var pets: [any Pet] = [
    Dog(),
    Cat(),
]
```

然後我們有一個找出肚子餓的寵物，但看下面這型別，實在很複雜，而且這個情況不使用 compiler 輔助，要直接手動寫出這樣的型別很困難

```swift
var hungryPets: LazyFilterSequence<LazySequence<[any Pet]>.Elements> {
    pets.lazy.filter(\.isHungry)
}
```

這個返回結果的型別很複雜，而且對外部呼叫的人來說根本不需要知道那麼多細節，這時候就可以用我們剛剛學的 `some` 

```swift
var hungryPets: some Collection {
    pets.lazy.filter(\.isHungry)
}
```

但當我們要開始餵食他們時發現，完全不知道他們是什麼型別了，使用 `some Collection` 太過抽象了

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/image%201.png?raw=true)

可以改成 `Collection<any Pet>` 

```swift
var hungryPets: some Collection<any Pet> {
    pets.lazy.filter(\.isHungry)
}
```

`Collection` 的定義，可以讓我們在使用時才決定 `Element` 

![image.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/image%202.png?raw=true)

## 要怎麼選擇 `some` 和 `any` ？

他們兩個都可以搭配 protocol 當作型別使用

- Opaque type → some
- Existential type → any

![截圖 2024-08-30 10.44.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-30_10.44.59.png?raw=true)

從 compiler 的角度來看

- `some` compiler 知道完整具體細節，但我們開發者不知道是什麼（對外部隱藏細節），我們只知道是**某種特定**的 Pet
- `any` compiler 只知道是一個箱子，跟我們開發者一樣不知道具體是什麼，我們只知道可能是**任何一種** Pet

這邊特別強調是**某種特定** 和 **任何一種**，是有差異的。

某種特定的型別更強調是具體其中一種而已，不會改變，而任何一種更強調是有可能是 Dog 也有可能是 Cat，很不固定的感覺，是有可能改變的。

下面會繼續討論這部分，從 code 來看會比較能感受其中的差異。

以變數的角度來看

- `some` 是匿名的，但真實存在，在方法派發上效能較好，但變數初始化完成，就不能再修改

```swift
var cat: some Pet = Cat()    // ✅
cat = Cat()   // ❌  Cannot assign value of type 'Cat' to type 'some Pet'
cat = Dog()   // ❌  Cannot assign value of type 'Dog' to type 'some Pet'

var copyCat: some Pet = cat    // ✅
copyCat = cat   // ❌  Cannot assign value of type 'some Pet' (type of 'cat') to type 'some Pet' (type of 'copyCat')

// ❌ Conflicting arguments to generic parameter 'τ_0_0' ('Dog' vs. 'Cat')
var pets: [some Pet] = [
    Dog(),
    Cat(),
]

var cat1: some Pet = Cat()
pets.append(cat1)   // ❌  No exact matches in call to instance method 'append'
```

- `any` 是二次封裝後的間接型別，在變數賦值時有一個裝箱 (Boxing) 的過程，方法派發是動態的，所以效能較差，但有失必有得，靈活性很高

```swift
var cat: any Pet = Cat()
cat = Cat()   // ✅
cat = Dog()   // ✅

var copyCat: any Pet = cat    // ✅
copyCat = cat   // ✅

// ✅
var pets: [any Pet] = [
    Dog(),
    Cat(),
]

var cat1: any Pet = Cat()
pets.append(cat1)   // ✅
```

假設把它們當作 function 的 return value 用時

```swift
enum PetType {
    case dog
    case cat
}

// ❌ Function declares an opaque return type 'some Pet', but the return statements in its body do not have matching underlying types
func createSomePet1(_ type: PetType) -> some Pet {
    switch type {
    case .dog: return Dog()
    case .cat: return Cat()
    }
}

// ✅
func createSomePet2(_ type: PetType) -> some Pet {
    switch type {
    case .dog: return Dog()
    case .cat: return Dog()
    }
}

// ✅
func createPet(_ type: PetType) -> any Pet {
    switch type {
    case .dog: return Dog()
    case .cat: return Cat()
    }
}
```

Opaque Type `some` 限定回傳的 type 是同一種，但為何在 SwiftUI 中可以有 if-else 的寫法，回傳不同 type?

以下的 Code 是可以運作的

```swift
import SwiftUI

struct ContentView: View {
    @State var isLoggedIn = false

    @ViewBuilder
    func createView() -> some View {
        if isLoggedIn {
            Text("Logged In")
        }
        else {
            Button("Click to Login") {
                isLoggedIn.toggle()
            }
        }
    }

    var body: some View {
        let view = createView()

        // print AnyView _ConditionalContent<Text, Button<Text>>
        print(type(of: view))

        return view
    }
}

#Preview {
    ContentView()
}
```

從這個 print 出來的結果 `AnyView` 就知道，雖然他看起來是回 `Text` 或 `Button` 但他最後都會變成 `AnyView` 這是一種 type erasure 的做法，讓所有型別最後轉換成同個通用的東西

我們可以從 View 的定義開始看，其中 `body` 的部分有一個 `@ViewBuilder` 的修飾

```swift
public protocol View {
    /// The type of view representing the body of this view.
    associatedtype Body : View

    /// The content and behavior of the view.
    @ViewBuilder @MainActor var body: Self.Body { get }
}
```

在往 `@ViewBuilder` 來看

```swift
/// A custom parameter attribute that constructs views from closures.
///
/// You typically use ``ViewBuilder`` as a parameter attribute for child
/// view-producing closure parameters, allowing those closures to provide
/// multiple child views. For example, the following `contextMenu` function
/// accepts a closure that produces one or more views via the view builder.
///
///     func contextMenu<MenuItems: View>(
///         @ViewBuilder menuItems: () -> MenuItems
///     ) -> some View
///
/// Clients of this function can use multiple-statement closures to provide
/// several child views, as shown in the following example:
///
///     myView.contextMenu {
///         Text("Cut")
///         Text("Copy")
///         Text("Paste")
///         if isSymbol {
///             Text("Jump to Definition")
///         }
///     }
///
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@resultBuilder public struct ViewBuilder {

    /// Builds an expression within the builder.
    public static func buildExpression<Content>(_ content: Content) -> Content where Content : View

    /// Builds an empty view from a block containing no statements.
    public static func buildBlock() -> EmptyView

    /// Passes a single view written as a child view through unmodified.
    ///
    /// An example of a single view written as a child view is
    /// `{ Text("Hello") }`.
    public static func buildBlock<Content>(_ content: Content) -> Content where Content : View

    public static func buildBlock<each Content>(_ content: repeat each Content) -> TupleView<(repeat each Content)> where repeat each Content : View
}
```

關鍵就在 `@ViewBuilder` 幫我們處理的，它使用了 type erasure 的方式，可以參考 [WWDC 22 Embrace Swift generics](https://developer.apple.com/wwdc22/110352) 16:59 開始看，這部分是簡單介紹

而怎麼處理的，就是靠 `@resultBiilder` ，這部分的細節可參考 Swift 5.4 中 [SE-0289](https://github.com/apple/swift-evolution/blob/main/proposals/0289-result-builders.md) Result Builders。

這種做法 (type erasure)其實很常見，例如在目前公司的專案裡，這個 183 行的 `errorPublisher` 的型別如此複雜，就很需要透過 type erasure 的做法，使用 186 行 `.eraseToAnyPublisher` 。

![截圖 2024-09-03 15.16.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-03_15.16.36.png?raw=true)

還有例如 `AnyCollection`, `AnySequence`, `AnyCancellable` 

## Protocol ‘XXX’ can only be used as a generic constraint because it has Self or associated type requirements.

我們可能很常看到這種錯誤

*Protocol ‘Equatable’ can only be used as a generic constraint because it has Self or associated type requirements.*

為什麼？

來看一段 code

```swift
public protocol Equatable {
  static func == (lhs: Self, rhs: Self) -> Bool
}

let lhs: Equatable = 1           // Int
let rsh: Equatable = "1"         // String
lhs == rsh                       // ?!, 不同型別的值可以判等？
```

很明顯 `lhs` 和 `rhs` 是不可以比較是否相等的，雖然他們都有比較是否相等的能力，但型別完全不一樣。

還有一個 `associatedtype` 的例子也一樣

```swift
protocol IdentifierType {
    associatedtype ID: Equatable
    var id: ID { get set }
}

struct Person: IdentifierType {
    var id: String
}

struct Website: IdentifierType {
    var id: URL
}

// 假如一個是 Person.id: String 另外一個是 Website.id: URL，很明顯是不能比較是否相等
func isEqual(_ lhs: IdentifierType, _ rhs: IdentifierType) -> Bool {
    lhs.id == rhs.id
}

// 這種情況就要用 generic 的方式來限制兩個東西的型別
func isEqual<T: Equatable>(_ lhs: T, rhs: T) -> Bool {
    lhs.id == rhs.id
}
```

這種情況我們可以透過以下幾種方式解決

- Type erasure
- Opaque Types `some`
- Generic

## **Primary Associated Types**

雖然我們有 `any` 和 `some` 來修飾 protocol 型別，但是 ****Protocol 是動態的特性，而且其中有 `Self requirements` / `Associated Type` 卻需要在編譯時保證型別，有可能解決嗎？

我們先看一個例子，我們有 `Animal` ，這次他們可以吃東西，還有喝水，但每個動物吃個東西都不一樣，例如牛吃乾草，雞可能吃家禽飼料之類的，所以額外用 `associatedtype FeedType` 。

```swift
protocol Animal {
    associatedtype FeedType

    func eat(_ food: FeedType)
    func drink()
}
```

再來我們有一個農場 `Farm` ，我們想讓動物喝水，沒什麼問題，但要餵食時出現了問題，我不知道該餵 `any Animal` 吃什麼，每種動物要吃的都不一樣。

```swift
struct Farm {
    func drinkAnimal(_ animal: any Animal) {
        animal.drink() // ✅
    }

    func feed(_ animal: any Animal) {
        // ❌ Member 'feed' cannot be used on value of type 'any Animal'; consider using a generic constraint instead
        animal.eat(???)
    }
}
```

在看另外一個問題，假如一次要餵很多動物喝水，該怎麼做？

直覺就是直接用 `[any Animal]` 讓它們喝水，但效能會比較差，或者為了效能用 generic，但這樣又回到 generic 這條老路上了

```swift
    func drinkAnimal(_ animals: [any Animal]) {
        for animal in animals {
            animal.drink()
        }
    }

    func drinkAnimals<T: Sequence>(_ animals: T) where T.Element: Animal {
        animals.forEach { $0.drink() }
    }
```

追根究柢就是因為 `associatedtype` 的關係，我們沒辦法直接知道是什麼，有什麼方法解決？

答案就是 `Primary Associated Types`

在 Swift ≤5.6， `Sequence` 的定義

```swift
public protocol Sequence {
      /// A type representing the sequence's elements.
      associatedtype Element where Self.Element == Self.Iterator.Element
      ...
}
```

而在 Swift 5.7 

```swift
public protocol Sequence<Element> {
      /// A type representing the sequence's elements.
      associatedtype Element where Self.Element == Self.Iterator.Element
      ...
}
```

所以剛剛的問題可以改成這樣，定義 protocol `Animal` 的地方再加一個 `<FeedType>` ，在 `func feed(_ animal: any Animal<AnimalFeed>)` ，這樣就可以明確傳遞 `AnimalFeed` 這個 instance 進來了

```swift
protocol Animal<FeedType> {
    associatedtype FeedType

    func eat(_ food: FeedType)
    func drink()
}

struct AnimalFeed { }

struct Farm {
    // ❌ Member 'feed' cannot be used on value of type 'any Animal'; consider using a generic constraint instead
    func feed(_ animal: any Animal) {
        animal.eat(???)
    }

    // ✅
    func feed(_ animal: any Animal<AnimalFeed>) {
        animal.eat(AnimalFeed())
    }
}

// 🐮
struct Cow: Animal {
    func eat(_ food: AnimalFeed) {
        print("Cow eat: \(food)")
    }
}

// ✅ output: Cow eat: AnimalFeed()
Farm().feed(Cow())
```

而剛剛的喝水問題，也可以調整成

```swift
    func drinkAnimals(_ animals: any Sequence<Animal>) {
        animals.forEach { $0.drink() } 
    }
```

`Primary Associated Types` 彈性很高可以放進一個 protocol 或一個具體型別

```swift
var animals: any Sequence<Animal> // Animal 是一個 protocol
var animal: any Animal<AnimalFeed>  // AnimalFeed 是一個 struct
```

這跟我們平常約的 `Generic Constraint` 很像，但是跟 `Primary Associated Types` 還是有差的，因為在使用時可以不指定，例如

```swift
var animals: any Sequence    // ✅
```

而且在 `extension` 中，`Primary Associated Types` 更簡潔

```swift
extension Sequence<String> { }

// Equivalent to:
extension Sequence where Element == String { }
```

但仔細看一下剛剛的 `Animal` 和 `Farm` 例子，其實還是有些美中不足的地方，例如 `AnimalFeed` 現在被我宣告成 `struct` 的，所以彈性很低，所有的動物都只能吃 `AnimalFeed` 這個具體的型別，但我們前面提到牛可能吃乾草，雞可能吃家禽飼料，該怎麼辦？

我們把 code 調整一下， 把 `Animal` `protocol` 中的 `drink()` 還有 `<FeedType>`  給刪除，現在只保留 `eat(:_)` 並且把 `AnimalFeed` 從 `struct` 改為 `protocol` ，還有新增給牛吃的食物 `Hay: AnimalFeed` 。

但這時我們的農場 `Farm` 出現兩個問題， compiler 報錯：

一個是 `Member 'eat' cannot be used on value of type 'any Animal'; consider using a generic constraint instead` 

另外一個則是又出現要餵食什麼的問題 `Cannot access associated type 'FeedType' from 'Animal'; use a concrete type or generic parameter base instead`。

```swift
protocol Animal {
    associatedtype FeedType

    func eat(_ food: FeedType)
}

protocol AnimalFeed { }

struct Hay: AnimalFeed { }

struct Cow: Animal {
    func eat(_ food: AnimalFeed) {
                print("Cow eat: \(food)")
    }
}

struct Farm {
    func feed(_ animal: any Animal) {
          // ❌
        // Member 'eat' cannot be used on value of type 'any Animal'; consider using a generic constraint instead
        // Cannot access associated type 'FeedType' from 'Animal'; use a concrete type or generic parameter base instead
        animal.eat(???) // 我們要餵食什麼？
    }
}

Farm().feed(Cow())
```

這題我們可以用 generic 的方式解決，因為這個情境是需要一個具體的 `Animal` ，所以可以用 generic 的方式。

```swift
struct Farm {
    func feed<A: Animal>(_ animal: A, with food: A.FeedType) {
        animal.eat(food)
    }
}
// ✅ output: Cow eat: Hay()
Farm().feed(Cow(), with: Hay())
```

但我們在前面學的，知道這個情境（只需要知道一個，並且具體的型別）也很適合使用 `some` ，跟 generic 一樣的效能，而且寫起來更好閱讀，並且這個 Xcode 警告，其實也告訴我可以用 `some` 來解決，按下 `Fix` 。

![截圖 2024-08-30 11.38.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-30_11.38.29.png?raw=true)

```swift
struct Farm {
    func feed(_ animal: some Animal) {
        // ❌ Cannot access associated type 'Feed' from 'Animal'; use a concrete type or generic parameter base instead
        animal.eat(???)
    }
}
```

## 定義多個型別之間的 `associatedtype` 關係

上述的問題，到現在剩不知道讓動物該吃什麼東西，解決這個問題之前，我們再把問題再更延伸一些，假設餵動物之前，還需要種植合適類型的作物，並且收穫作物以生產飼料。

那一開始我們寫 code，假如直接就從抽象層開始寫，有時候比較難，所以通常都是先把具體寫出來之後再慢慢優化，所以我們把目前 code 都在重新具體化，比較好以理解後續怎麼抽象

```swift
// 🐮
struct Cow {
    func eat(_ food: Hay) {
        print("Cow eat: \(food)")
    }
}

// 乾草
struct Hay {
    static func grow() -> Alfalfa { Alfalfa() }
}

// 苜蓿
struct Alfalfa {
    func harvest() -> Hay { Hay() }
}

let cow = Cow()

let alfalfa = Hay.grow()
let hay = alfalfa.harvest()

cow.eat(hay)
```

以牛（Cow）舉例，牛需要吃乾草，乾草（Hay）要透過苜蓿（Alfalfa）種植並收割後才有，但種植的東西（Alfalfa）要透過乾草（Hay）生長，所以等於他們之間關係是環環相扣，互相知道對方。

假設換成是雞（Chicken），雞要吃家禽飼料（Scratch），但家禽飼料需要有小米穀物（Millet）收割才有

```swift
// 🐔
struct Chicken {
    func eat(_ food: Scratch) {
        print("Chicken eat: \(food)")
    }
}

// 家禽飼料
struct Scratch {
    static func grow() -> Millet { Millet() }
}

// 小米穀物
struct Millet {
    func harvest() -> Scratch { Scratch() }
}

let chicken = Chicken()

let millet = Scratch.grow()
let cratch = millet.harvest()

chicken.eat(millet)
```

所以從上述具體實作來看，想必大家已經有抽象的想法了，原本的 `protocol` `Animal` 和 `AnimalFeed` 一定沒問題，就只差農作物了，我們這邊在定義一個 `protocol` `Crop` 代表農作物

```swift
protocol Animal {
    associatedtype Feed: AnimalFeed
    func eat(_ food: Feed)
}

protocol AnimalFeed {
    associatedtype CropType: Crop
    static func grow() -> CropType
}

protocol Crop {
    associatedtype FeedType: AnimalFeed
    func harvest() -> FeedType
} 
```

這邊可能有點抽象，特別說明一下 `AnimalFeed` 和 `Crop` 。

從下面這些圖一張一張看會比較好理解，大意是 `Self` 代表的是實體，從 `AnimalFeed` 先看，它其中的 `.CropType` 是另外一個實體 `Crop` 的 `FeedType` （ `Self.CropType.FeedType: AnimalFeed` )，可能有點複雜，因為他們的關係就相互環繞的，不好理解，可以想一下，我們的真實世界具體的東西，例如牛的食物是乾草，而乾草需要透過苜蓿收割才有，所以乾草的前身是苜蓿，有了苜蓿收割就有乾草，所以等同知道了有乾草這個具體的東西，就可以決定需要他的前置事物（苜蓿）是什麼了。

![截圖 2024-08-30 18.17.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-30_18.17.13.png?raw=true)

![截圖 2024-08-30 18.17.32.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-30_18.17.32.png?raw=true)

![截圖 2024-08-30 18.17.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-08-30_18.17.41.png?raw=true)

所以這也造成了 `associatedtype` 無限嵌套在符合 `AnimalFeed` 和 `Crop` 之間交替

讓我們在回到 `Farm` ，餵動物之前，我們要種植作物，然後加工成正確的類型的動物飼料，所以理想的情況是以下這樣

```swift
struct Farm {
    func feed(_ animal: some Animal) {
        let crop = type(of: animal).FeedType.grow() // 型別是 (some Animal).FeedType.CropType
        let feed = crop.harvest() // 型別是 (some Animal).FeedType.CropType.FeedType
        animal.eat(feed) // 我們這邊想要的型別是 (some Animal).FeedType
        // ❌ Cannot convert value of type '(some Animal).FeedType.CropType.FeedType'
        // (associated type of protocol 'Crop') to expected argument type '(some Animal).FeedType' (associated type of protocol 'Animal')
    }
}
```

但我們的 compiler 卻報錯了，可見目前我們設計的 protocol，定義的太過籠統，沒有準確的模擬我們的具體型別之間期望的關係

而且假設我們把 `Alfalfa` 的 `harvest()` 返回值，原本應該是 `Hay` ，但不小心寫錯，寫成返回雞的飼料 `Scratch` ，整個程式碼還是可以編譯的，具體型別都還是有滿足 `AnimalFeed` 和 `Crop` protocol 的要求，這表示我們目前定義的彈性還是有點高，任意滿足 `AnimalFeed` 的東西都能在 `Crop.harvest()` 中返回。

```swift
// 🐮
struct Cow: Animal {
    func eat(_ food: Hay) {
        print("Cow eat: \(food)")
    }
}

struct Hay: AnimalFeed {
    static func grow() -> Alfalfa { Alfalfa() }
}

struct Alfalfa: Crop {
    func harvest() -> Scratch { Scratch() } // ⚠️ 這邊應該是要 return Hay
}
```

我們在重新看一下我們的 protocol

```swift
protocol AnimalFeed {
    associatedtype CropType: Crop
    static func grow() -> CropType
}

protocol Crop {
    associatedtype FeedType: AnimalFeed
    func harvest() -> FeedType
}
```

真正的問題是，我們有太多不同的 `associatedtype`，它們又代表各自，所以我們需把它們在關聯起來，讓它們變成同一個具體型別，這樣就可以避免上面這種寫錯具體型別的問題發生，還可以讓`feedAnimal()` 提供它需要的保證，只要使用 `where` 關鍵字，就可以表達這些 `associatedtype` 之間的關係。

```swift
protocol AnimalFeed {
    associatedtype CropType: Crop 
        where CropType.FeedType == Self
    static func grow() -> CropType
}
```

我們在 `AnimalFeed` 中加上 `where CropType.FeedType == Self`，這樣的寫法就有靜態的保證，兩種不同的 `associatedtype` 實際上需要是相同的具體型別。

這樣符合 `AnimalFeed` protocol 的型別就被加以限制，就像下圖這樣

![截圖 2024-09-04 14.45.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_14.45.51.png?raw=true)

所以我們就可以得知這個 `AnimalFeed` 這樣循環下來，最後會得到原始的 `AnimalFeed` 而且一定是相同的具體型別，而不是像前面說的那樣，造成 `associatedtype` 的無限迴圈。

![截圖 2024-09-04 15.08.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_15.08.31.png?raw=true)

那麼 `Crop` 呢？

它也一樣，也是有過多的 `associatedtype` 

![截圖 2024-09-04 15.10.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_15.10.44.png?raw=true)

所以我們可以跟 `AnimalFeed` 一樣去限制他的型別，一樣使用 `where` 。

```swift
protocol Crop {
    associatedtype FeedType: AnimalFeed 
        where FeedType.CropType == Self
    func harvest() -> FeedType
}
```

![截圖 2024-09-04 15.12.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_15.12.27.png?raw=true)

經過我們的調整後再來看看 `Farm`

```swift
struct Farm {
    func feed(_ animal: some Animal) {
        let crop = type(of: animal).FeedType.grow()
        let feed = crop.harvest()
        animal.eat(feed)
    }
}
```

從參數 `animal: some Animal` 開始，我們先用 `type(of: animal).FeedType`  取得 `(some Animal).FeedType`，然後在 `.grow()` 取得 `(some Animal).FeedType.CropType` ，然後再從 `crop.harvest()` 得到 `(some Animal).FeedType` ，最後就得到該動物正確要吃的東西了，如下圖

![截圖 2024-09-04 15.21.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_15.21.20.png?raw=true)

所以我們能正常執行了 🎉

```swift

struct Farm {
    func feed(_ animal: some Animal) {
        let crop = type(of: animal).FeedType.grow()
        let feed = crop.harvest()
        animal.eat(feed)
    }

    func feedAll(_ animals: [any Animal]) {
        for animal in animals {
            feed(animal)
        }
    }
}

let farm = Farm()
let animals: [any Animal] = [
    Cow(),
    Chicken()
]

farm.feedAll(animals)
// output:
// Cow eat: Hay()
// Chicken eat: Scratch()
```

最後的關係圖

![截圖 2024-09-04 15.29.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Swift/Swift%20Opaque%20and%20Existential%20Protocol%20Types%20(any%20&%20some)/%25E6%2588%25AA%25E5%259C%2596_2024-09-04_15.29.20.png?raw=true)

## 總結

最後，講了那麼多，以下是重點

- **Write `some` by default.**
    
    假如是遇到 `any` 跟 `some` 的選擇，能用 `some` 就用 `some`，除非需求無法滿足，才選擇用 `any` 。
    
    generic 也是有被優化，所以跟 `some` 一樣效能都比較好，假如都能滿足需求的情況下，也優先選 `some` ，因為通常寫起來會比 generic 好閱讀，而且還有隱藏細節的特性 (opaque type)。
    
    這是 Apple 的建議，透過 ****`Opaque Parameter`，可以兼得泛型的性能和 `Existential type` 的簡潔。
    
- Type erasure，假如某些情況回傳的型別要很高的彈性，不限制它具體的型別時，可以用 `type erasure` 的方式，就例如像官方的 `AnyCollection`, `AnySequence`, `AnyCancellable`, `AnyPublisher` 。
- `Primary Associated Types` 可以解決很多 `Self requirements` / `Associated Type` 的問題。
    
    還有在設計 protocol 遇到 `associatedtype` 彈性不夠的話，可以用 `Primary Associated Types` ，例如官方的 `Sequence` 。
    
- 多個型別之間有 `associatedtype` 的關聯時，可以善用 `where` 來約束之間的關係

## 參考了

- https://docs.swift.org/swift-book/documentation/the-swift-programming-language/opaquetypes/
- https://developer.apple.com/videos/play/wwdc2022/110352/
- https://developer.apple.com/videos/play/wwdc2022/110353/
- https://juejin.cn/post/7113802054455263268
- https://blog.csdn.net/m0_65012566/article/details/136913045
- https://zxfcumtcs.github.io/2022/02/01/SwiftProtocol1/
- https://zxfcumtcs.github.io/2022/02/04/SwiftProtocol2/
- http://zxfcumtcs.github.io/2022/06/30/SwiftProtocol3/
- https://swiftsenpai.com/swift/dynamic-dispatch-with-generic-protocols/
- https://blog.zhangwen.site/swift-some-any/
- https://blog.csdn.net/qfanmingyiq/article/details/136846071
- https://www.youtube.com/watch?v=21bRqauym3k
- https://ejameslin.github.io/Opaque-return-types/
- https://github.com/swiftlang/swift/blob/main/docs/ABI/TypeMetadata.rst#protocol-metadata
- https://pofat.dev/2019/05/21/重新檢視-swift-的-protocol-二.html
- https://www.hackingwithswift.com/plus/advanced-swift/how-to-use-phantom-types-in-swift
