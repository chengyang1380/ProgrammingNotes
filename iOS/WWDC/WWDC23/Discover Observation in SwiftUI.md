# Discover Observation in SwiftUI

![New Zealand — Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/IMG_7975.jpg?raw=true)

New Zealand — Queenstown

## What is Observation?

`Observation` 是 Swift 中用於追蹤屬性變化的新功能，透過 macro 對它們進行轉換。在 SwiftUI 裡針對特別的 property 發生變化時，才重新計算 View 的 body。

要使用非常簡單，加上 `@Observable` 即可，這就是依靠 macro 強大的力量，而且就不用使用 Property wrapper 了，例如 `@State`, `@Published`。

```swift
@Observable class FoodTruckModel {    
    var orders: [Order] = []
    var donuts = Donut.all
}
```

接下來介紹一個範例，在這個 `var body: some View { ...` 裡，只有用到 `model.donuts` 所以假設我們按下 `Button` ，整個 View 是會更新的。

但假設現在 `Button` 的功能，換成新增一個 order，但在 View 的 body 裡沒有使用到 orders 的話，整個 View 是不會更新，因為這個 orders 這個 property 是沒有被追蹤的。

![Screenshot 2025-05-15 at 16.22.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-15_at_16.22.55.png?raw=true)

這種機制是 `Observation` 的一個重要優勢，它使得 SwiftUI 能夠**精確地、選擇性地更新 UI**，只重新計算那些真正受到資料變化影響的視圖部分，這能帶來顯著的效能提升。

不像舊的 `ObservableObject` 和 `@Published` 可能會導致更廣泛的視圖更新， `@Observable` 和 SwiftUI 的結合能夠實現更細粒度的更新控制。

那我們現在把它換成 computed property，看看會發生什麼事

我們現在對 `FoodTruckModel` 在新增一個 computed property， `var orderCount: Int { orders.count }`

```swift
@Observable class FoodTruckModel {
  var orders: [Order] = []
  var donuts = Donut.all  
  var orderCount: Int { orders.count }
}

struct DonutMenu: View {
  let model: FoodTruckModel

  var body: some View {
    List {
      Section("Donuts") {
        ForEach(model.donuts) { donut in
          Text(donut.name)
        }
        Button("Add new donut") {
          model.addDonut()
        }
      }
      Section("Orders") {
        LabeledContent("Count", value: "\(model.orderCount)")
      }
    }
  }
}
```

這時候以 View 的角度來看，我們追蹤的是 computed property `orderCount`，但是裡面訪問到了 `orders` 這屬性，這也表示 `orders` 屬性的變化，也會讓 UI 更新。

### @Observation

Macro

Tracks access

Property changes cause UI updates

從中我們可以看到這提升了很大的效能

## SwiftUI property wrappers

透過 `Obserable` SwiftUI 的 property wrappers 比以往更簡單， `State` , `Environment` , `Bindable` 變成三種主要與 SwiftUI 一起使用的 property wrappers。

首先介紹 `State`

使用 @State 修飾的 Model  ，它的生命週期會由擁有它的 View 的生命週期來管理，表示與 View 一致。

範例： 在呈現一個 Sheet 時，會使用一個 @State 變數 (donutToAdd) 來綁定數值到可編輯的欄位上。這代表 donutToAdd 這個 Model 是這個 Sheet View 自己的狀態

![Screenshot 2025-05-16 at 11.32.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_11.32.38.png?raw=true)

再來 `Environment`

它是可以全局訪問的值，可以再多個地方共享，像之前用 `@EnvironmentObject` 一樣，由 super view 傳至 sub view 。

![Screenshot 2025-05-16 at 14.04.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_14.04.50.png?raw=true)

最後是 `@Bindable` 

就像之前的 `@Binding`  一樣，假如這個 Model 是需要雙向綁定的，就是用它，像是要放在 `TextField` 的情境。

Lightweight

Connect references to UI

Uses $ syntax to create bindings

![Screenshot 2025-05-16 at 14.13.09.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_14.13.09.png?raw=true)

### 小結

![Screenshot 2025-05-16 at 14.39.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_14.39.16.png?raw=true)

1. 這個 Model 需要成為 View 自身的狀態嗎？（讓 Model 與 View 生命週期一致）
    
    Yes: `@State` 
    
    No:
    
2. 這個 Model 需要成為 App 全局變數嗎？
    
    Yes: `@Environment`
    
    No:
    
3. 這個 Model 只需要綁定嗎？
    
    Yes: `@Bindable`
    
    No:  `var`
    

## Advanced uses

使用 `@Observable` 的 Model，在 SwiftUI 上讓 view body 使用，都有自動追蹤 model property 變化而更新 View 的能力。

但也有一些情況需要額外自行處理，如果這個 model 內有一個 computed property 不是由內部 stored property 組成，就需要額外2個步驟。

**只有當要觀察的 property 不是透過 `Observable` 類型的 stored property 組合時，才需要。**

這邊就是要手動額外寫

![Screenshot 2025-05-16 at 14.47.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_14.47.57.png?raw=true)

### Computed properties

SwiftUI Tracks access

Composed properties

Manual control when needed 

## ObservableObject

假如我們有舊的程式碼，是用 `ObservableObject` 的，但某天終於可以用 `Observable` ，我們可以看看如何更新程式碼，使用 `Observable` 。

範例1

![Screenshot 2025-05-16 at 15.39.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_15.39.18.png?raw=true)

非常簡單，刪除 conform `ObservableObject` 以及 `@Published` 。

![Screenshot 2025-05-16 at 15.39.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_15.39.39.png?raw=true)

在 View 方面

![Screenshot 2025-05-16 at 15.40.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_15.40.39.png?raw=true)

也是一樣很簡單，刪除 `@ObservedObject` ，及 `@EnvironmentObject` 換成 `@Environment`。

![Screenshot 2025-05-16 at 15.41.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Discover%20Observation%20in%20SwiftUI/Screenshot_2025-05-16_at_15.41.22.png?raw=true)

所以轉換過程就只有3件事

1. 換掉 conform `ObservableObject` ，改用 macro `@Observable` 
2. 刪除 property wrappers `@Published`
3. 只用 `@State` , `@Environment` 和 `@Bindable` 

關於從 `Observable Object` 的 Migrate，可參考[官方文件](https://developer.apple.com/documentation/swiftui/migrating-from-the-observable-object-protocol-to-the-observable-macro)

## Resources

- [https://developer.apple.com/videos/play/wwdc2023/10149/](https://developer.apple.com/videos/play/wwdc2023/10149/)