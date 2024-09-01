# SwiftUI @State @Binding @ObservedObject @StateObject

![DJI_0008.JPG](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI/SwiftUI%20@State%20@Binding%20@ObservedObject%20@StateObject/DJI_0008.jpg?raw=true)

**Keelung - Dawulun Beach**

在 SwiftUI 裡，有各種修飾 model 的方式，來狀態管理，最常見的就是 `@State`, `@Binding` , `@ObservedObject` 和 `@EnvironmentObject` …。

首先介紹最常見的 `State` 和 `Binding` 。

## @State


- 特別適合儲存 value type，像是 Bool, String, Int, Enum, Struct，所以最常搭配的 UI 元件有 Toggle, TextField…，但也可以搭配 reference type。
- 通常我們都會把它設為 private的，確保它只屬於建立他的那個 View，不要讓其他的地方可以存取這個值，這樣才可以確保 SwiftUI 正確管理它，讓它的生命週期有正確的跟著畫面。
- Thread-Safe 的所以可以在非 main thread 上做修改。
- 變數在 View 的 init 只能賦值一次，後續的調整要在 View 的 `body` 內進行，詳情請參考大神[**东坡肘子**](https://twitter.com/fatbobman)的[文章](https://fatbobman.com/zh/posts/avoid_repeated_calculations_of_swiftui_views/)。
- 在 Observation 框架中用於確保 `@Observable`  實例的生命週期不短於 View 本身。詳情請參考大神[**东坡肘子**](https://twitter.com/fatbobman)的[文章](https://fatbobman.com/zh/posts/mastering-observation/)。

## @Binding

它與 `@State` 時常一起出現，情境就是 parent view 用 `State` ，而 child view 會用 `Binding` ，然後在 child view 可以去更新 parent view 的 `State` 值。

像是這樣

![Screenshot 2024-07-15 at 22.53.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI/SwiftUI%20@State%20@Binding%20@ObservedObject%20@StateObject/Screenshot_2024-07-15_at_22.53.03.png?raw=true)

```swift
struct ParentView: View {
    @State private var isPlaying: Bool = false
    
    var body: some View {
        VStack {
            Text("Music")
            ChildView(isPlaying: $isPlaying)
        }
    }
}

struct ChildView: View {
    @Binding var isPlaying: Bool
    
    var body: some View {
        Button(isPlaying ? "Pause" : "Play") {
            isPlaying.toggle()
        }
    }
}
```

- 可以想像它是一個通道或橋樑，連結著兩個 View 的某個 model 狀態。
- 不是直接持有數據，所以通常初始化不會有值，都透過 parent view 傳遞值。
- 而且跟更新畫面沒關係，通常都由 `State` 觸發的，從 child view 的 `Binding` 改變某個值，隨後就會觸發到 parent view 的 `State` ，而造成 UI 更新。
- 假如 View 之間的層級過多，使用 `Binding` 傳遞，可能導致 model 狀態難以管理追蹤，此時可以考慮其他方案，例如 `@EnvironmentObject`。
- 在 Observation 框架中，可以使用 `@Binding` 為 `@Observable` 實例創建對應的 `Binding` 接口。

## **小結 @State 和 @Binding 的區別**

**狀態管理**：

- **`@State`** 用於管理和存儲 private model 狀態。
- **`@Binding`** 用於從 parent view 或外部來源接收和引用狀態。

**初始化要求**：

- **`@State`** 必須在聲明時初始化。
- **`@Binding`** 在聲明時不需要初始化，從外部 (通常是 parent view) 傳入一個綁定的值。

**用途範圍**：

- **`@State`** 適用於單個 View 圖內部的狀態管理。
- **`@Binding`** 適用於需要在兩個 View 之間共享和同步的狀態。

**數據流向**：

- **`@State`** 的數據流向是單向的，即 View 內部。
- **`@Binding`** 支持雙向數據綁定，允許數據在不同 View 之間流動和同步，例如 child view 去更新 parent view 的數據狀態。

## @ObservedObject

- 它與 `StateObject` 及 `EnvironmentObject` 一樣，要使用的 Model 都需要遵守 `ObservableObject` 。
- 要注意它的生命週期，因為它的生命週期不一定與 View 同步，例如有一個 `ObservedObject` 的 model object 在 child view，這個 child view 在某個 parent view 裡，parent view 在更新 parent view 上的某個 UI 元件，可能會造成 `ObservedObject` 的 object 重新 init。
- 它不管存儲，會隨著 View 的創建被多次創建，所以通常不該在 View 裡自行創建。
- 常與 `StateObject` 或 `State` 搭配，parent view 使用 `StateObject` 建立 model object，child view 使用 `ObservedObject` ，然後由 parent view 傳遞 model 進來，不由 child view 自行 `init` 。
- 更多細節可參考  [SwiftUI @StateObject @ObservedObject](https://github.com/chengyang1380/ProgrammingNotes/blob/main/iOS/SwiftUI/SwiftUI%20%40StateObject%20%40ObservedObject.md)
- 以下是簡單的範例：

```swift
class Model: ObservableObject {
    @Published var value: Int

    init(value: Int) {
        self.value = value
    }
}

struct ParentView: View {
    @StateObject var model = Model(value: 0)

    var body: some View {
        VStack {
            ChildView(model: model)
        }
    }
}

struct ChildView: View {
    @ObservedObject var model: Model

    var body: some View {
        Text("Value: \(model.value)")
    }
}
```

## @EnvironmentObject

- 它與 `StateObject` 及 `ObservedObject` 一樣，要使用的 Model 都需要遵守 `ObservableObject` 。
- 假如有多個相關的 View，需要用到同一個 Model，或者是（A 畫面傳遞給 B, C, D，但實際上只有 A 及 D 會用到此 Model），一個一個傳遞，不是一個好方法，這時就就很適合用 `EnvironmentObject` ，它不用一層一層傳遞，需要用到此 Model 的 View 自行宣告就可以了，常見的例子是一些環境變數，像是：字體大小、顏色模式，但記得 parent view 要提供，並且使用的 child view 要用正確的型別讀取，否則會 crash，在 preview 也是。
- 這些環境變數被改動時，關聯的畫面都會一起更新，所以很適合用在用戶主題風格、App 狀態之類的。
- 只能 get，不能 set。
- 啟動方式是放入一個 `EnvironmentValues` 的 `KeyPath` 。
- 記得只有在必要時才使用 `EnvironmentObject`，因為有時候會引發不必要的畫面更新。
- 以下是簡單的範例：

我們在 `DiceChildView` 的 model 不是直接由 `DiceParentView` 傳遞的，而是透過 `@EnvironmentObject var dice: Dice` 取得，而在 `DiceParentView` 自行管理 `@StateObject var dice = Dice()` ，但關鍵是透過 `.environmentObject(dice)` ，來傳給其他 child view。

```swift
class Dice: ObservableObject {
    @Published var value = 0
}

struct DiceChildView: View {
    @EnvironmentObject var dice: Dice

    var body: some View {
        Text("Dice: \(dice.value)")
    }
}

struct DiceParentView: View {
    @StateObject var dice = Dice()

    var body: some View {
        NavigationStack {
            VStack {
                Button("Roll Dice") {
                    dice.value = Int.random(in: 1...6)
                }

                NavigationLink {
                    DiceChildView()
                } label: {
                    Text("Show Detail View")
                }
            }
            .frame(height: 200)
        }
        .environmentObject(dice)
    }
}

#Preview {
    DiceParentView()
}

```

再來是 `@StateObject` ，這是從 SwiftUI 2.0 才有的東西

## @StateObject

- 它與 `ObservedObject` 及 `EnvironmentObject` 一樣，要使用的 Model 都需要遵守 `ObservableObject` 。
- 該物件在 View 中的生命週期會保持唯一，意思就是說假設 View 被更新了，這個物件也不會被重新創建，與 `ObservedObject` 相反。
- 上面敘述的這個特性，代表適合用在 Model 需要整個生命週期持續存在的情況，像是放在 View 的層級最上層，然後下層的 View 就比較適合使用 `ObservedObject`。
- 相較 `@State` 而言， `@StateObject` 就是加強版，適合管理複雜的 Model。

## **小結 @ObservedObject, @EnvironmentObject 和 @StateObject 的區別**

- 它們都是需要遵守 `ObservableObject`
- 雖然很多情況 `StateObject` 可以取代 `ObservedObject`，但他們還是有各自合適的使用情境， `StateObject` 更常維護與建立，而 `ObservedObject` 更常引用已存在的物件。
- `ObservedObject` 在 View 的存續期間只保存了訂閱關係，而 `StateObject` 除了保存了訂閱關係外還保持了對可觀察對象的強引用。
- `State` & `StateObject` property 儲存的資料將一直存在，直到 View 的生命結束。
- 若我們想要 View 重新產生時，資料能持續存在不受影響，則須將它宣告為 `State` 或 `StateObject`，它們儲存的資料將存在別的地方，因此不會因為 View 重新生成而被清掉。當 View 被建立和出現後， `State` & `StateObject` 儲存的資料將一直存在，直到 View 的生命結束。
- 而對於外界接收 `ObservableObject` 的 View，該用誰，還是需要根據當下的情況。像是 `NavigationLink` 的 `destination` 中的 View，由於 SwiftUI 對他們在建構時並沒有做 lazy 處理，所以要額外注意。（可以參考詳細介紹）
- 在 iOS 17+ 之後，如果 App 主要使用 Observation 和 SwiftData ，那這三個 Property Wrapper的使用機會可能會降低。

## 參考

- [https://youtu.be/rcRtJyPPjfo?si=ZOazwmmxD94ClxB0](https://youtu.be/rcRtJyPPjfo?si=ZOazwmmxD94ClxB0)
- [https://fatbobman.com/zh/posts/exploring-key-property-wrappers-in-swiftui/](https://fatbobman.com/zh/posts/exploring-key-property-wrappers-in-swiftui/)
- [https://fatbobman.com/zh/posts/swiftui-state/](https://fatbobman.com/zh/posts/swiftui-state/)
- [https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-environmentobject-to-share-data-between-views](https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-environmentobject-to-share-data-between-views)
- https://onevcat.com/2020/06/stateobject/