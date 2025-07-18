# Build a UIKit app with the new design

![New Zealand — Deer Park Heights Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/IMG_8299.jpg?raw=true)

New Zealand — Deer Park Heights Queenstown

## Tab views and split views

UITabBarController, UISplitViewController 有更新 Liquid Glass 外觀

### UITabBarController

minimize tab bar on scroll

![Jun-11-2025 11-01-12.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Jun-11-2025_11-01-12.gif)

```swift
tabBarController.tabBarMinimizeBehavior = .onScrollDown
```

再更進階的做法還有這個範例， `UITabAccessory`

![Screenshot 2025-06-11 at 11.07.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.07.26.png)

![Screenshot 2025-06-11 at 11.06.21.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.06.21.png?raw=true)

![Screenshot 2025-06-11 at 11.07.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.07.01.png?raw=true)

![Screenshot 2025-06-11 at 11.09.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.09.10.png?raw=true)

對於這個細節 UIKit: Automatic trait tracking 以及 `updateProperties()` ，可以前往 [WWDC25 - What’s new in UIKit](https://developer.apple.com/videos/play/wwdc2025/243/) 來查看更多細節

簡單介紹一下 `Update Properties` 

- UIViewController 與 UIView 都有新增一個方法 `updateProperties()` ，用於更新 UI
- 它是輕量級的 UI 更新方式，不會觸發完整的 layout 過程，所以 `layoutSubviews()` 和 `viewWillLayoutSubviews()` 不會觸發。常見情境如下：
    - 更改 tab bar 或 badge 等 UI 內容
    - 顯示或隱藏某個小 UI 元件
    - 無需移動或者調整 View 大小
- 可以透過 call `setNeedsUpdateProperties()` 來手動觸發更新
- 可以自動追蹤 `@Observable Object`

### Tab bar and sidebar

iPad 上的 tab bar 以及 sidebar 都也有 Liquid Glass，使用 `UITabBarController` 它們可以懸浮在 App 上方

![Screenshot 2025-06-11 at 11.33.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.33.19.png)

![Screenshot 2025-06-11 at 11.33.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-06-11_at_11.33.02.png)

使用 `UITab` 及 `UITabGroup` ，app 就可以自動適應，在 iPad 上切換 tab bar 或 sidebar 非常容易

更多細節可以查看

[WWDC24 Elevate your tab and sidebar experience in iPadOS](https://developer.apple.com/videos/play/wwdc2024/10147/) ([Doc](https://developer.apple.com/documentation/uikit/elevating-your-ipad-app-with-a-tab-bar-and-sidebar))

[WWDC25 Make your UIKit app more flexible](https://developer.apple.com/videos/play/wwdc2025/282/)

`UIBackgroundExtensionView`

![Screenshot 2025-07-10 at 11.32.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_11.32.01.png)

![Screenshot 2025-07-10 at 11.34.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_11.34.02.png)

## Navigation and toolbars

![Screenshot 2025-07-10 at 11.49.32.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_11.49.32.png)

### Default grouping

navigation bar 和 toolbar 的按鈕系統會自動分組，每個 group 共享一種玻璃背景

![Screenshot 2025-07-10 at 11.56.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_11.56.49.png)

Shared glass backgrounds for:

- Image buttons
- Bar button item groups with multiple items

Separate glass backgrounds for:

- Text buttons
- `SystemItem.done` and `SystemItem.close`
- `Style.prominent`  buttons

假如不想要被系統自動決定 group，可以使用 `.fixedSpace(0)` 來獨立

![Screenshot 2025-07-10 at 11.55.09.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_11.55.09.png)

假如不想用 native 的玻璃色，也可以直接指定 `tintColor`

![Screenshot 2025-07-10 at 13.47.04.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_13.47.04.png)

假如連背景都想要客製化，可以調整 `style = .prominent`

![Screenshot 2025-07-10 at 13.47.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_13.47.36.png)

假如有個需求是要把所有的按鈕都均分開來，通常會用 `flexibleSpace` ，但在 Liquid Glass 默認情況下遇到 `flexibleSpace` 會讓他們從 group，單獨出來

![Screenshot 2025-07-10 at 13.52.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_13.52.01.png)

但假如我們還是想要有 Liquid Glass 背景，而且要讓他們是一個 group 的話，可以用

`flexibleSpace.hidesSharedBackground = false`

![Screenshot 2025-07-10 at 13.51.26.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_13.51.26.png)

### Title area updates

這次 iOS 26 `UINavigationBar` ，在標題和大標題區域有更多客製化了

Subtitle：新增副標題

Attributed strings：可以對標題跟副標題更多客製化調整

Custom views：可以指定 custom view 增加互動式元件，例如 Button

Large title in scroll views

在這範例中可以看到一些新設計

- Serach bar 被放在底下的 toolbar 裡
- 使用新的 subtitle

![Screenshot 2025-07-10 at 14.05.06.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_14.05.06.png)

也可以 custom view，例如 `largeSubtitleView` 

![Screenshot 2025-07-10 at 14.06.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_14.06.45.png)

### Review bar customizations

默認情況現在 navigation bar or toolbar 都是透明的

假如想使用 native 的 Liquid Glass 要刪除任何背景 custom 設置

Backgrounds

- `UIBarAppearance`
- `backgroundColor`

Custom view items （例如 Toolbars，以下這些都會影響 liquid glass）

- Backgrounds
- Spacing

### Edge Effect

![Jul-10-2025 14-17-01.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Jul-10-2025_14-17-01.gif)

這個新的 Edge Effect 並非只限於系統工具欄使用，可以自行搭配使用，覆蓋 scroll view 的邊緣

![Screenshot 2025-07-10 at 14.26.13.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_14.26.13.png)

像這個例子是上邊緣 UI 元件太多太雜了，已經不適合使用 Edge Effect，這時候可以直接使用 `.hard` ，這樣幾乎看不到背景了，就不會太雜亂

![Screenshot 2025-07-10 at 14.27.58.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_14.27.58.png)

### Push and pop transitions

![Jul-10-2025 14-35-35.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Jul-10-2025_14-35-35.gif)

有更多的 push and pop 的過渡客製化

Interruptible in iOS 26

Touches are always enabled during transitions

細節可參考

[WWDC 24 Enhance your UI animations and transitions](https://developer.apple.com/videos/play/wwdc2024/10145/)

### Content backswipe

- Entire content area can be used for the backswipe
終於不是在左邊邊緣才可以執行返回操作，IG 很早就有做這功能了，很方便
- Works in areas with no other interactions
這 backswipe 會自動檢查是否存在其他有衝突的交互操作，例如 table view cell 的 swipe，就無法返回

![Screenshot 2025-07-10 at 14.53.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-10_at_14.53.16.png)

- New `interactiveContentPopGestureRecognizer` to set up failure requirements 
假如要讓自定義手勢優先於 content backswipe 的操作，就需要用到這個 `interactiveContentPopGestureRecognizer`

## Presentations

![Screen Recording 2025-07-10 at 14.57.03.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-10_at_14.57.03.gif)

Menus 會自動有這效果，假如彈出窗口來自 toolbars 也會呈現這新動畫

![Screen Recording 2025-07-10 at 15.03.09 (1).gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-10_at_15.03.09_(1).gif)

也可以把這個縮放過渡效果用在表單，present 某個 viewController，就在那個 viewController 加上 `preferredTransition = .zoom`，如下圖

![Screenshot 2025-07-17 at 15.55.04.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_15.55.04.png)

![Untitled.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Untitled.gif)

在 iOS 26 還有一個 present 的過渡效果，但一樣要刪除任何自訂義的背景

![Screen Recording 2025-07-17 at 15.57.50.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_15.57.50.gif)

之前在 iPad 上操作 alert，有些都會直接在那顆按鈕的旁邊跳出選單，現在 iOS 也會，所以需要在 alertController 上設置 `popoverPresentationController.sourceItem`  or  `sourceView` ，這樣才會有這新的過渡效果

![Screenshot 2025-07-17 at 16.06.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.06.34.png)

![Screen Recording 2025-07-17 at 16.03.07.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_16.03.07.gif)

### Inline alerts and action sheets

- No cancel button, tap outside to dismiss
剛剛上述的這種操作表單 (action sheets) ，點擊外面的任何地方就會取消，所以不用 cancel button，
- If the source is not set, alert appears centered and has a cancel button
假如沒指定 source，那麼這個 action sheets 會置中，而且還會有 cancel button

## Search

現在 search bar 很多都會從上面自動移動到底下，並使用 toolbar，這樣的目的是更輕鬆使用搜尋功能

![Screenshot 2025-07-17 at 16.24.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.24.08.png)

假如你原本就有 toolbar 在底下，可以直接用 `searchBarPlacementBarButtonItem` 

![Screenshot 2025-07-17 at 16.27.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.27.05.png)

### Toolbar search

在 iPad 上的搜尋與 macOS toolbar 同樣的方式，將 search bar 放置在 navigation bar 的後置邊緣，假如要啟用這功能就用 `searchBarPlacementAllowsExternalIntegration = true`

![Screenshot 2025-07-17 at 16.30.09.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.30.09.png)

### Search as a tab

Displayed within the tab bar

假如搜尋功能常常需要被用到，可以考慮把 search bar 放在 tab bar 上，讓他在切換 tab 後，一樣可以馬上搜尋，但記得直接使用 `UITabBarController` ，這樣畫面就可以獨立顯示一個 search 的 tab，點擊之後可以先放一些常用的結果或提示，如下圖，鍵盤還沒有升起。

![Screen Recording 2025-07-17 at 16.35.10.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_16.35.10.gif)

假如想要在點擊 search tab 時，自動啟用輸入文字搜尋，可以設定 `automaticallyActivesSearch = true`

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/image.png)

![Screen Recording 2025-07-17 at 16.38.51.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_16.38.51.gif)

### Search as a dedicated view

也可以考慮把 search bar 放在 side bar or tab bar 中

假如設定 `preferredSearchBarPlacement = .integratedCentered` 把 search bar 放在置中，當 tab bar 又出現時，他就會自動置中 

![Screenshot 2025-07-17 at 16.46.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.46.57.png)

![Screen Recording 2025-07-17 at 16.48.23.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_16.48.23.gif)

## Controls

![Screenshot 2025-07-17 at 16.58.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_16.58.42.png)

### Switch

記得要檢查 layout，因為 size 跟以前不一樣，在交互時，會自動有 Liquid Glass 的效果

![Screen Recording 2025-07-17 at 17.00.25.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_17.00.25.gif)

### Buttons

除了普通按鈕，還有另外兩種 glass 質感外觀可以設定

`.glass()` 就是常規，用 `.prominentGlass()` 是可以設定自己色調的

![Screenshot 2025-07-17 at 17.03.30.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_17.03.30.png)

### Sliders

![Screen Recording 2025-07-17 at 17.06.31.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-17_at_17.06.31.gif)

在 iOS 26 上還支援刻度，可以透過 `trackConfiguration` 設定

![Screenshot 2025-07-17 at 17.09.47.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_17.09.47.png)

也可以把 thumb 拿掉，很適合放媒體播放用

![Screenshot 2025-07-17 at 17.50.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-17_at_17.50.49.png)

## Custom elements

![Screenshot 2025-07-18 at 10.15.27.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.15.27.png)

要記住 Liquid Glass 的設計理念，它跟 `UIBlurEffect` 很像，但使用場景不一樣

Liquid Glass 的設計用為互動層，它會懸浮在內容上方，給人一種觸手可及的感覺，還提供用戶可操作的控制元件

例如 Map app，這些按鈕懸浮在地圖上方，它們是獨特的控制層，呈現效果很自然，因此這場景使用是很好的選擇

![Screenshot 2025-07-18 at 10.18.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.18.10.png)

還有當 sheet 在展開時，那些按鈕就會消失，這是要防止玻璃元素之間的重疊，並保持單層玻璃懸浮效果的視覺完整性

![Screen Recording 2025-07-18 at 10.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_10.gif)

假如想要把玻璃質感應用在自己客制的 View，可使用 `UIVisualEffectView` + `UIGlassEffect`

![Screenshot 2025-07-18 at 10.42.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.42.57.png)

預設情況玻璃是膠囊形狀的，假如想自定義形狀，可用 `cornerConfiguration` API

![Screenshot 2025-07-18 at 10.44.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.44.39.png)

也支援 dark mode

![Screenshot 2025-07-18 at 10.45.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.45.11.png)

也可以將玻璃質感新增到現有的玻璃 container 中，一層包一層，它會自動適應外觀

![Screenshot 2025-07-18 at 10.45.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.45.55.png)

![Screenshot 2025-07-18 at 10.47.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_10.47.42.png)

尺寸越大越不透明，越小就越透明，而且也自動支援 light/dark mode

![Untitled.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Untitled%201.gif)

![Screenshot 2025-07-18 at 11.00.29.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.00.29.png)

也可以自行新增 label 或 image 等內容，一樣支援 dark mode，會根據背景調整顏色

![Screenshot 2025-07-18 at 11.01.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.01.43.png)

![Screen Recording 2025-07-18 at 11.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_11.gif)

顏色的部分也可在客製化 `tintColor` 和 label 上的 `textColor = .label` 

![Screenshot 2025-07-18 at 11.06.08.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.06.08.png)

也可以客製化 glass 顏色

![Screenshot 2025-07-18 at 11.07.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.07.39.png)

假如想讓 glass，像 Button 一樣，有些交互動畫，可使用 `isInteractive = true`

![Screenshot 2025-07-18 at 11.14.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.14.16.png)

![Screen Recording 2025-07-18 at 11-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_11-2.gif)

最後假如不需要使用 glass 時，要將它隱藏，直接用 `effect = nil` ，記得不要調整 alpha 而隱藏，這樣才能確保以合適的動畫效果顯示或隱藏

![Screenshot 2025-07-18 at 11.16.57.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.16.57.png)

![Screen Recording 2025-07-18 at 11.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_11%201.gif)

剛剛上述的範例，都只有一個 view，假如有多個交互時，玻璃效果會有額外的內置行為，Liquid Glass 可以無縫融合不同的形狀

要動態合併 glass，可以使用 `UIGlassContainerEffect` 配置 `UIVisualEffectView` ，假如有空間的話，他們還是會獨立呈現，只有相互靠近才會合併

![Screenshot 2025-07-18 at 11.24.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.24.53.png)

可控制他們之間的間距

![Screenshot 2025-07-18 at 11.29.28.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_11.29.28.png)

![Screen Recording 2025-07-18 at 11.26.13.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_11.26.13.gif)

![Screenshot 2025-07-18 at 13.45.09.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_13.45.09.png)

![Screen Recording 2025-07-18 at 13.41.41-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_13.41.41-1.gif)

![Screenshot 2025-07-18 at 13.46.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screenshot_2025-07-18_at_13.46.44.png)

![Screen Recording 2025-07-18 at 13.41.41-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20UIKit%20app%20with%20the%20new%20design/Screen_Recording_2025-07-18_at_13.41.41-2.gif)

### UIGlassContainerEffect

- Enables animations between glass elements
- Uniform adaptation

當然 `UIGlassContainerEffect` 不是只有加一些動畫效果，它還有強制執行統一的適應，玻璃會動態適應背景，但依舊保持一致的外觀

## Next steps

- Build your app with Xcode 26 
這樣就可以看到很多新設計的內容，在 app 的呈現
- Start with standard UI 
如果有客製化元件，請判斷是不是用 native 更合適
- Consider your custom use cases

## Resources


- [Video](https://developer.apple.com/videos/play/wwdc2025/284)
- [**Adopting Liquid Glass**](https://developer.apple.com/documentation/TechnologyOverviews/adopting-liquid-glass)
- [**Human Interface Guidelines**](https://developer.apple.com/design/human-interface-guidelines)