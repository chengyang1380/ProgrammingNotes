# Build a SwiftUI app with the new design

![New Zealand — Deer Park Heights Queenstown](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/IMG_8360.jpg)

New Zealand — Deer Park Heights Queenstown

## Introduction

![Screenshot 2025-07-21 at 16.00.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.00.38.png)

在新的 iOS 26 以及 macOS Tahoe 有全新的外觀及風格，這次變更的核心就是用於 Control 及 Navigation 的全新自適應材質 Liquid Glass

![Jul-21-2025 16-11-37.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Jul-21-2025_16-11-37.gif)

![Screen Recording 2025-07-21 at 16.12.19.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-21_at_16.12.19.gif)

![Screen Recording 2025-07-21 at 16.13.16.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-21_at_16.13.16.gif)

![Screen Recording 2025-07-21 at 16.20.59.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-21_at_16.20.59.gif)

假如想要深入了解 Liquid Glass 的設計理念思維請看

[WWDC 25 - Meet Liquid Glass](https://developer.apple.com/videos/play/wwdc2025/219/)

[WWDC 25 - Get to know the new design system](https://developer.apple.com/videos/play/wwdc2025/356/)

## App structure

### NavigationSplitView

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/image.png)

`NavigationSplitView` 用於瀏覽一個明確定義的層次結構，這結構中可能包含很多根類別

![Screenshot 2025-07-21 at 16.42.37.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.42.37.png)

使用 `.backgroundExtensionEffect()` ，View 就可以擴展到安全區域的外部，而不會裁切其中的內容

![Screenshot 2025-07-21 at 16.44.24.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.44.24.png)

假如把側邊欄隱藏了，此時 Image 在安全區域之外也會被模糊化處理，這是要讓他一直抱持可見狀態

![Screenshot 2025-07-21 at 16.45.54.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.45.54.png)

### TabView

![Screen Recording 2025-07-21 at 16.48.17-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-21_at_16.48.17-1.gif)

使用 `.tabBarMinimizeBehavior(.onScrollDown)` ，可以滑動時自動縮小

Tab bar accessory

![Screenshot 2025-07-21 at 16.54.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.54.02.png)

使用 `.tabViewBottomAccessory { … }` 

並且會有 scroll 時縮小的效果

![Screen-Recording-2025-07-21-at-16.52.52.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen-Recording-2025-07-21-at-16.52.52.gif)

還可以使用 `@Environment(\.tablViewBottomAccessoryPlacement) var placement` 來處理 View 的位置狀態變化

![Screenshot 2025-07-21 at 16.58.20.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_16.58.20.png)

### Sheets

在 iOS 26 預設嵌入高度（畫面高度6成，如圖），這時表單會使用 Liquid Glass 作為背景

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/image%201.png)

假如故意讓高度比較小時，底部邊緣會向內收縮

![Screenshot 2025-07-21 at 17.05.01.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-21_at_17.05.01.png)

這從最小到最大的整個過程 iOS 都會自動幫我過度背景狀態

![Screen Recording 2025-07-23 at 15.17.45.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_15.17.45.gif)

所以想用 native sheet 的話，不建議客製化背景

![Screenshot 2025-07-23 at 15.26.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_15.26.11.png)

縮放的過渡，搭配 sheet 

![Screen Recording 2025-07-23 at 15.27.49-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_15.27.49-1.gif)

![Screenshot 2025-07-23 at 15.30.16.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_15.30.16.png)

和 sheet 一樣，menu, alert (dialog) and popovers，都有這樣的動畫效果，將焦點從他們的操作吸引到展示的內容上

![Screen Recording 2025-07-23 at 15.27.49-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_15.27.49-2.gif)

![Screenshot 2025-07-23 at 15.34.45.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_15.34.45.png)

![Screen Recording 2025-07-23 at 15.27.49-3.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_15.27.49-3.gif)

![Screenshot 2025-07-23 at 15.39.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_15.39.53.png)

## Toolbars

- Items are placed in Liquid Glass
- Appears above your app’s content
- Adaptive 自適應它背後的內容

Toolbars 是會自動分 group

![Screenshot 2025-07-23 at 15.43.56.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_15.43.56.png)

假如想要客製化 group 的話，可以善用 `ToolbarSpacer(.fixed)` 

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/image%202.png)

他也可以用在底下的 toolbars 

![Screenshot 2025-07-23 at 16.06.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.06.36.png)

但也有些情境不需要這種 group，就可以用 `.sharedBackgroundVisibility(.hidden)` ，將 item 分到自己的 group 上，而不顯示背景

![Screenshot 2025-07-23 at 16.08.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.08.05.png)

假如想加上 native badge，現在也很容易，加上 `.badge(_:)` 即可

![Screenshot 2025-07-23 at 16.10.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.10.34.png)

toolbars 除了 group 以及 badge 以外，新設計還引入其他變化，可以使用單色宣染，單色可以減少視覺干擾，強調 app 的內容並確保內容清晰可讀。

一樣可以使用 `.tint(_:)` 和  `buttonStyle(.borderedProminent)` 來著色，但希望這樣做是為了傳達特定含義，例如行動指引或下一步操作的指引，而不只是為了提供視覺效果

![Screenshot 2025-07-23 at 16.15.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.15.02.png)

Edge Effect，這是一個應用於系統工具欄下內容的細微模糊和淡入淡出的效果

![Screen Recording 2025-07-23 at 16.25.42.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_16.25.42.gif)

假如 app 裡在 toolbars 後面有任何額外的背景或變暗效果，請記得清除，避免造成干擾
⚠️

![Screenshot 2025-07-23 at 16.42.48.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.42.48.png)

✅

![Screenshot 2025-07-23 at 16.43.09.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.43.09.png)

假如遇到複雜的 ui 元素在上面，例如日曆 app，可以用 `scrollEdgeEffectStyle(.hard)` 

![Screenshot 2025-07-23 at 16.44.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.44.44.png)

## Search

### Search patterns

所有平台的兩個關鍵搜尋模式均有一些重大更新

- Search in the toolbar
search field 放在 toolbar 的話，會放在螢幕底部，要讓他觸手可及，但在 iPad 和 Mac 上，顯示在 toolbars 右上角
- Search as a page
第二種模式將 search field 作為 tab bar 上的專用頁面

**Search in the toolbar**

![Screenshot 2025-07-23 at 16.49.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.49.41.png)

可以直接在 `NavigationSplitView` 上加 `.searchable(text:)` 

![Screenshot 2025-07-23 at 16.52.33.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.52.33.png)

在 iPhone 上會自動顯示在底部

![Screenshot 2025-07-23 at 16.53.35.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.53.35.png)

根據設備大小、toolbar 按鈕的數量和其他因素，系統可能會選擇將 search filed 最小化爲一個 toolbar button

![Screenshot 2025-07-23 at 16.56.24.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_16.56.24.png)

![Screen Recording 2025-07-23 at 16.58.14.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_16.58.14.gif)

**Search as a page**

假如把 search 做在 tab 上，通常表示需要專用的搜尋頁面完成，在 Apple 的所有平台上的 app 幾乎都用這種模式

![Screenshot 2025-07-23 at 17.00.38.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.00.38.png)

假如要在 tab bar 上面加 search 功能

```swift
Tab(role: .search) { ... }
	.searchable(text: $searchText)
```

![Screenshot 2025-07-23 at 17.06.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.06.39.png)

點擊之後，代表到了專門的搜尋頁面，在此可以準備一些瀏覽建議讓 user 操作，也可以等 user 在點擊一次 search bar 進行鍵盤輸入搜尋

![Screen Recording 2025-07-23 at 17.05.33.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_17.05.33.gif)

而在 Mac or iPad 上，當點選 search bar 時，search bar 會顯示在 App 瀏覽建議的正上方

![Screen Recording 2025-07-23 at 17.10.31.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_17.10.31.gif)

 

## Controls

Refreshed look for controls

Familiar look and feel across devices

![Screenshot 2025-07-23 at 17.17.43.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.17.43.png)

### Capsule buttons

border button 現在默認具有膠囊形狀，從而與新設計系統的彎曲保持協調

![Screenshot 2025-07-23 at 17.20.11.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.20.11.png)

### Button shapes

現在不管什麼 size 都抱持圓角形狀

![Screenshot 2025-07-23 at 17.21.53.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.21.53.png)

### Button sizes

大小也有針對新設計進行更新

![Screenshot 2025-07-23 at 17.24.00.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.24.00.png)

可以透過 `.controlSize(_:)` 來調整，而且可以控制單個或整個都

![Screenshot 2025-07-23 at 17.25.17.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.25.17.png)

對於最重要、最醒目的操作，新設計系統現在也支援超大尺寸按鈕 (Extral Large)

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/image%203.png)

### Glass button styles

![Screenshot 2025-07-23 at 17.28.41.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.28.41.png)

### Sliders

透過 init 裡的 `step` 可以加上刻度

![Screenshot 2025-07-23 at 17.48.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.48.19.png)

也可以使用 `SliderTick` 手動放置單個刻度標記 

![image.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/image%204.png)

還可以將其軌道填充的起始位置設定在特定地方，這對於處理那些可能會從非起始默認值向左或向右調整的值，會很有用，例如在播放時選擇更快或更慢的速度值

在 init 時，使用 `neutralValue`

![Screenshot 2025-07-23 at 17.53.02.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_17.53.02.png)

### Menus

現在 Mac or iOS 平台，使用 menu 會得到差不多風格的 UI

![Screenshot 2025-07-23 at 18.14.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_18.14.42.png)

![Screenshot 2025-07-23 at 18.15.19.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_18.15.19.png)

### Corner concentricity

Apple 出的很多有元件的圓角，都與他的容器完美契合對齊，這稱為 Corner concentricity （角同心性），像下面這張圖的 button 與他的 container 的角落應該要共享同一個角中心

![Screen Recording 2025-07-23 at 22.48.53-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-23_at_22.48.53-2.gif)

所以自己客製化的 View 要實現這件事，可以使用 `.rect(corner: .containerConcentric)`

![Screenshot 2025-07-23 at 22.46.34.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-23_at_22.46.34.png)

想採用新設計的最佳方式，使用標準 native 的元件最容易（Toolbars, Search bar, Controls），但我們有時候還是需要客製化元件，這時候就要看下一章節的介紹了

## Liquid Glass effects

像是 Map app 就是很好的 liquid glass 使用例子，他自定義的 Control (Button) 都有懸浮在 app 內容上

![Screen Recording 2025-07-24 at 09.27.05.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-24_at_09.27.05.gif)

假如要在 Control 加上 Glass effect，可以使用 `.glassEffect()` ，而 SwiftUI 會自動自適應調整，以便在彩色的背景上，也可以保持良好的閲讀性

![Screenshot 2025-07-24 at 09.33.18.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_09.33.18.png)

形狀也可以調整

![Screenshot 2025-07-24 at 09.38.14.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_09.38.14.png)

假如有特別需要強調的元件，可以使用 `.tint()` ，但記得跟前面在 toolbars 一樣，當有要傳達特定含義才用，不要只是為了提升視覺效果，讓他顯眼就使用

![Screenshot 2025-07-24 at 09.42.44.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_09.42.44.png)

在 iOS 上對於自定義 Control 或有互動性的元件，要將 `interactive` 加到 `.glassEffect()` 中，這樣這 glass effect 會透過縮放、彈跳和閃爍，對 user 做出一些互動反應，這就跟 toolbar or slider 一樣，提供這種互動效果

![Screenshot 2025-07-24 at 09.51.36.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_09.51.36.png)

![Screen Recording 2025-07-24 at 09.49.30.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-24_at_09.49.30.gif)

假如有多個客製化 glass 元件，我們可以把它們組合在一起，要使用 `GlassEffectContainer` ，這樣的使用對於正確的視覺效果非常重要

![Screen Recording 2025-07-24 at 10.00.54.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-24_at_10.00.54.gif)

### Glass sampling

glass 材質會反射和折射光線，並會從附近內容中選擇顏色，這種效果可以透過從一個比自身更大的區域中進行內容採樣來實現（要仔細看邊緣）

![Screen Recording 2025-07-24 at 09.57.18-2.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-24_at_09.57.18-2.gif)

但 glass 效果之間是無法進行採樣的

![Screenshot 2025-07-24 at 10.05.50.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_10.05.50.png)

假如有這需求，也可以把這兩個元件放進同個 `GlassEffectContainer` 

![Screenshot 2025-07-24 at 10.07.55.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_10.07.55.png)

這邊來一個實際的 `GlassEffectContainer` 使用範例

![Screen Recording 2025-07-24 at 10.09.44-1.gif](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screen_Recording_2025-07-24_at_10.09.44-1.gif)

把 Control 包在 `GlassEffectContainer` 裡，並且加上 `@Namespace var namespace` 以及 `.glassEffectID(_:in:)` 

![Screenshot 2025-07-24 at 10.13.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/refs/heads/main/Images/WWDC/WWDC25/Build%20a%20SwiftUI%20app%20with%20the%20new%20design/Screenshot_2025-07-24_at_10.13.42.png)

## **Next steps**

- Build with Xcode 26: 記得要使用 Xcode 26 並 build 在 iOS 26 才可以看到這個 glass effect
- Audit your apps: 要好好審查自己的 app 流程，確認是不是有什麼畫面要調整，尤其要注意 sheet, toolbar 那些元件的背景色是否可以移除，否則很容易跟 glass effect 衝突
- Consider your custom use cases: 使用 Liquid Glass 打造有表現力的元件，讓 App 脫穎而出

## Resources

- [Video](https://developer.apple.com/videos/play/wwdc2025/323)
- [Applying Liquid Glass to custom views](https://developer.apple.com/documentation/SwiftUI/Applying-Liquid-Glass-to-custom-views)
- [Landmarks: Building an app with Liquid Glass](https://developer.apple.com/documentation/SwiftUI/Landmarks-Building-an-app-with-Liquid-Glass)
- [Adopting Liquid Glass](https://developer.apple.com/documentation/TechnologyOverviews/adopting-liquid-glass)