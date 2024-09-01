# SwiftUI @StateObject @ObservedObject

![Taipei - Tamsui](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/DJI_0096.jpg?raw=true)

Taipei - Tamsui

## 前言

從早期的 SwiftUI （2019），有三個常用的狀態管理 Property Wrapper。

- `@State`
常用在 View 中的私有狀態，Access Control 為 `private`，一般來說都用在 Value Type 的值，所以它是代表作用範圍最小，也是最單純的狀態。
- `@ObservedObject`
假如我們要對 Reference Type 的值做管理，就要讓它實現 `ObservableObject` 協議，然後其 class 中的屬性也要被標記成 `@Published` ，這樣該屬性發生變化時就會自動發出事件，通知有依賴他的 View 進行更新。

```swift
class Model: ObservableObject {
	@Published var value: Int = 0
}
```

- `@EnvironmentObject`
針對 View 有很多層次，要將 `ObservableObject` 物件從頭傳到尾的情境，我們就可以在 Parent View 用 `.environmentObject` 來注入，這樣在他底下的 Child View 都可以透過 `@EnvironmentObject` 來取得值。

![Untitled](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/Untitled.png?raw=true)

> *圖片來自  SwiftUI for Absolute Beginners*
> 

但後續針對 `ObservedObject` 一些特性的問題，而新增了 `@StateObject` ，簡單來說就是若 View 持有一個 Model 是 `@ObservedObject` ，每當 View 被重新繪製時，這個 Model 也會被重新 init，為了不讓它每次重新 init，就可以選擇使用 `@StateObject`。

接下來看幾個範例，讓我們更好理解他們之間的差異

### 前置作業

---

首先先建立一個遵守 `ObservableObject` 的 Model ，在 `init` 和 `deinit` 時都 `print(...)` ，以便我們觀察 Model 的生命週期變化，記得我們在 `init` 時會指定 `type: ObservableObjectType`

```swift
enum ObservableObjectType: String {
    case observedObject
    case stateObject
}

class Model: ObservableObject {
    @Published var value = 0

    let id: Int
    let type: ObservableObjectType

    init(type: ObservableObjectType) {
        self.id = Int.random(in: 0...100)
        self.type = type
        print("\(type.rawValue) model init, id: \(id)")
    }

    deinit {
        print("\(type.rawValue) model deinit, id: \(id)")
    }
}
```

### 範例1

---

```swift
import SwiftUI

struct StateObjectView: View {
    @StateObject var model = ObjectModel(type: "StateObject")
    var body: some View {
        VStack {
            Text("@StateObject count :\(model.count)")
            Button("+1"){
                model.count += 1
            }
        }
    }
}

struct CountViewObserved: View {
    @ObservedObject var model = ObjectModel(type: "ObservedObject")
    var body: some View {
        VStack {
            Text("@Observed count :\(model.count)")
            Button("+1"){
                model.count += 1
            }
        }
    }
}

struct Demo1: View {
    @State private var shouldShowName = false
    var body: some View {
        VStack{
            StateObjectView()
                .padding()
            ObservedObjectView()
                .padding()
        }
    }
 }

#Preview {
    Demo1()
}

```

首先我們分別建立了 `ObservedObjectView` 和 `StateObjectView`，主要的差別就在 model 用 `ObservedObject` 還是 `StateObject` 而已，然後我們要 Preview 的畫面是 `Demo1` ，啟動後，目前功能都很正常，都可以正常的加數字，並且 Console 各自只有**一個** `Model init` 。

```swift
// Console:
observedObject model init, id: 72
stateObject model init, id: 15
```

再來我們多新增一個功能到 `Demo1: View` ，要顯示或隱藏名字的功能，所以現在的 `Demo1` Code 是這樣：

```swift
struct Demo1: View {
    @State private var shouldShowName = false

    var body: some View {
        VStack{
            Text("Name: \(shouldShowName ? "Jobs" : "-")")
            Button("Toggle Name") {
                shouldShowName.toggle()
            }
            StateObjectView()
                .padding()
            ObservedObjectView()
                .padding()
        }
    }
}
```

然後我們嘗試使用它

![Screen Recording 2024-09-01 at 15.57.56.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/Screen_Recording_2024-09-01_at_15.57.56.gif?raw=true)

然後就會發現一件很神奇的事情，原本的 `ObservedObject score` 是有值的（非預設值0），但只要按了 Toogle name 的按鈕後，讓 `Demo1` 這個 View 的 UI 元件更新（View 更新畫面），它就變回預設值0了，而且仔細看 Console，會發現

```swift
// Console:
observedObject model init, id: 72
stateObject model init, id: 15
observedObject model init, id: 7
observedObject model deinit, id: 72
```

`observedObject model` 會變回預設值0，是因為一直被重新 init，這就是 `StateObject` 和 `ObservedObject` 的最大差異！在  `Demo1` 這個 View 裡的生命週期，`StateObject` 會跟著 View，但 `ObservedObject` 並不會，每次 View 更新它就被更新。

所以 `StateObject` 的生命週期比 `ObservedObject` 還長。

### 範例2

---

接下來我們使用 `NavigationView` 加上 `List` ，並沿用剛剛的 View (`StateObjectView` 和 `ObservedObjectView` )。

```swift
import SwiftUI

struct Demo2: View {
    var body: some View {
        NavigationView {
            List {
                NavigationLink("@StateObject", destination: StateObjectView())
                NavigationLink("@ObservedObject", destination: ObservedObjectView())
            }
        }
    }
}

#Preview {
    Demo2()
}
```

![Screen Recording 2024-09-01 at 16.17.40.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/Screen_Recording_2024-09-01_at_16.17.40.gif?raw=true)

但這時看 Console 會發現 `ObservedObject` 已經先被 init 了，而 `StateObject` 卻沒有。

```swift
// Console
observedObject model init, id: 28
```

然後我們在操作時會發現，每當進入 `StateObjectView` 畫面後，都會重新歸零，這時才 init `StateObject`，而 `ObservedObjectView` 並不會。

所以我們知道使用 `NavigationLink` 的 `destination` 中的 View（`StateObjectView` ），當開啟時才會 init `StateObject` 。

**所以在某些情況會發現 `ObservedObject` 的生命週期比 `StateObjectView` 還長，這應該是由於 SwiftUI 對他們在建構時並沒有做 lazy 處理，所以要額外注意。**

### 範例3

---

再來

```swift
import SwiftUI

struct Demo3: View {
    @State private var showStateObjectSheet = false
    @State private var showObservedObjectSheet = false

    var body: some View {
        List {
            Button("Show StateObject Sheet") {
                showStateObjectSheet.toggle()
            }
            .sheet(isPresented: $showStateObjectSheet) {
                StateObjectView()
            }
            Button("Show ObservedObject Sheet"){
                showObservedObjectSheet.toggle()
            }
            .sheet(isPresented: $showObservedObjectSheet) {
                ObservedObjectView()
            }
        }
    }
}

#Preview {
    Demo3()
}
```

![Screen Recording 2024-09-01 at 16.25.09.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/Screen_Recording_2024-09-01_at_16.25.09.gif?raw=true)

不管是 `StateObject` 還是 `ObservedObject` 都是在畫面彈跳出來後才 init，並且畫面只要消失就會 deinit。
所以可以得知他們的兩種 Model 的生命週期都與 View 一致。

### 範例4

---

這邊來一個小考題，來看看目前對於兩者的差異是否有一定的概念

首先先建立一個 `ObservableObject` 的物件，他是我們的骰子

```swift
class DiceObject: ObservableObject {
    @Published var value = 1

    init(value: Int = 1) {
        self.value = value
        print("Dice Created")
    }

    func roll() {
        value = Int.random(in: 1...6)
    }
}
```

再來建立這個骰子的 View，裡面有一個圖片，顯示目前骰子幾點，並且還有一顆按鈕 Play，點下去就可以骰，那我們現在先把 `diceObject` 使用 `@ObservedObject` 。

```swift
struct DiceView: View {
    @ObservedObject var diceObject = DiceObject()

    var body: some View {
        VStack {
            Image(systemName: "die.face.\(diceObject.value).fill")
                .resizable()
                .scaledToFit()
            Button("Play") {
                diceObject.roll()
            }
        }
    }
}
```

最後再把整個遊戲的 View 建立起來，他的變數有一個背景色和骰子 ID，並且還有兩顆按鈕，分別是換背景色，另外一個是更新 `DiceView` 的 ID，這邊是想順便說明 View 的生命週期在兩種情況下會結束：

- 從畫面上移除，這很好理解
- 另外就是 View 的 identity 改變，在這個範例我們可以嘗試

```swift
struct GameView: View {
    @State private var color = Color.white
    @State private var diceID = UUID()

    var body: some View {
        ZStack {
            color
                .ignoresSafeArea()
            VStack {
                DiceView()
                    .id(diceID)
                Button("Random Background Color") {
                    color = Color(red: .random(in: 0...1),
                                  green: .random(in: 0...1),
                                  blue: .random(in: 0...1))
                }
                Button("change dice ID") {
                    diceID = UUID()
                }
            }
            .font(.title)
            .padding()
        }
   }
}

struct Demo4: View {
    var body: some View {
        GameView()
    }
}

#Preview {
    Demo4()
}
```

我們開啟畫面後，Console 就出現一個 Dice Created，按下 Play 可以正常遊玩，但是我們按下 Random Background Color 後，骰子竟然又變成預設值，並且 Console 又出現 Dice Created，這不是我們期望的，我們希望就算變更顏色，骰子還是照舊原本的數值，那我們應該要怎麼改？

另外按下 change dice ID，因為換了 `DiceView` 的 ID，所以 Console 出現 Dice Created，這是我們預期的，因為 View 的 ID 被更換，生命週期結束。

![Aug-15-2024 18-07-16.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftUI%20@StateObject%20@ObservedObject/Aug-15-2024_18-07-16.gif?raw=true)

從前面的範例一得知，`StateObject` 通常比 `ObservedObject` 的生命週期還長，所以我們只要調整一下 `DiceView` 的 `diceObject` 的 property wrapper ，把它從 `ObservedObject` 更換成`StateObject` 就可以了

## 小結

---

- 他們都是訂閱有遵守 `ObservableObject` 協議的物件。
- `ObservedObject` 在 View 的存續期間只保存了訂閱關係，而 `StateObject` 除了保存了訂閱關係外還保持了對可觀察對象的強引用。
- `State` & `StateObject` property 儲存的資料將一直存在，直到 `View` 的生命結束。
- 若我們想要 `View` 重新產生時，資料能持續存在不受影響，則須將它宣告為 `State` 或 `StateObject`，它們儲存的資料將存在別的地方，因此不會因為 `View` 重新生成而被清掉。當 `View` 被建立和出現後，`State` & `StateObject` 儲存的資料將一直存在，直到 `View` 的生命結束。
- 如果不希望 `Model` 狀態在 `View` 更新時丟失，那確實適合用 `StateObject`。但是，如果 `View` 本身就期望每次刷新時獲得一個全新的狀態，那麼對於那些不是自己創建的，而是從外界接收的 `ObservableObject` 來說，`@StateObject` 反而是不合適的。
- 簡單來說對於 `View` 自己創建的`ObservableObject Model` 來說，大概率會用 `@StateObject` ，讓儲存和 `View` 生命週期一致。
- 而對於外界接收 `ObservableObject` 的 `View`，該用誰，還是需要根據當下的情況。像是 `NavigationLink` 的 `destination` 中的 `View`，由於 SwiftUI 對他們在建構時並沒有做 lazy 處理，所以要額外注意。

## 參考

---

- https://fatbobman.com/zh/posts/swiftui-state/
- https://onevcat.com/2020/06/stateobject/
- https://fatbobman.com/zh/posts/stateobject/
- https://medium.com/彼得潘的-swift-ios-app-開發問題解答集/swiftui-view-的生命週期影響-stateobject-state-儲存的資料-ffd4982fcece