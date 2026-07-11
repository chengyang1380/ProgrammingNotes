# Modernize your UIKit app

![**Queenstown Hill Walking Track — New Zealand**](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/IMG_8600.jpg?raw=true)

**Queenstown Hill Walking Track — New Zealand**

# Adaptivity

會介紹 app 的 adaptivity requirements，有很多重大變更以及如何完全可調整大小。

我們的 app 已經可以在許多不同 environment  顯示，有不同 screen size，也可以 side by side (並排)與其他 app 同時執行，甚至有些還做自身並排

在 iPad 上，user 期望 app 能無縫調整大小並移至外接顯示器。在 iPhone 上，user 期望 app 可以 portrait 和 landscape (直向橫向）都正常運作。他們也期望能將 iPhone mirror (鏡像)到 Mac 上，以上在 iOS 和 macOS 27 中，體驗有獲得改善，那改善什麼？可參考下面的第一張的 gif （在 Mac 上縮放 app 大小）。

在這些 environment  執行的 app 現在會受到許多 dynamic changes 的影響，這是任何 native app 都需要支援的。

當 user 在 Mac 上使用 iPhone mirror 時，可以調整 iPhone 的畫面大小，同樣的，假如適用於 iPhone 的 app 在 iPad 上執行時也要可以調整大小，這就像其他 iPad app 一樣，因此，重要的是我們的 app 必須動態調整，以適應執行時的任何可用場景大小。

![ezgif-51dc18b1497692d1.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/ezgif-51dc18b1497692d1.gif?raw=true)

如果 app 是 universal binary，是一個好的起點，但還有很多事情要做，讓 app 完全 adaptive，並動態調整到所在的 environment。

## Legacy API

讓 app adaptive 常見的問題

![Screenshot 2026-07-10 at 17.05.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_17.05.00.png?raw=true)

- App lifecycle
記得不要在使用 AppDelegate，請使用 SceneDelegate
- Main screen
確保未使用 main screen
- User interface idiom
檢查 user interface idiom
- Interface orientation
檢查 interface orientation

## App lifecycle

### **Scene life cycle**

- Basis for an adaptive app
這是自適應 app 的基礎，也是許多其他任務的基礎
- Required when linking iOS 27
使用最新的 SDK building，使用 UIScene lifecycle 是必要的，沒有的話 app 無法啟動

假如還沒有 migrate 到 scene delegate 請查看

- [Make your UIKit app more flexible](https://developer.apple.com/videos/play/wwdc2025/282/) - WWDC25
- [Transitioning to the UIKit-scene-based lifecycle](https://developer.apple.com/documentation/UIKit/transitioning-to-the-uikit-scene-based-life-cycle) - Documentation

## Main screen

接下來，當你的 iPhone app 在 Mac 上 mirroring 時或當 User 把 app 移至 iPad 上的外接顯示器時，與你的 scene 相關聯的 screen 會改變，這表示 code 中，對 main screen 的任何參考，將失去正確性。

重要的是不應該繼續用 application 的 main screen，要從 window scene 動態取得 screen

![Screenshot 2026-07-10 at 17.15.37.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_17.15.37.png?raw=true)

另外在一些 helper function, tool, 非 ui layer 的地方，若當下無法取得 ui environment 資訊（例如取得 view, viewController 的 context 時），將 screen 傳入是比較好的做法，，讓上層 UI components 把當下正確的 screen 資訊當參數傳進來。

但！比獲取正確 screen 有更好的做法，就是完全移除 screen references，還有許多其他 API 更適合 adaptive app。 

存取 screen scale 的做法應替換成 trait collection’s displayScale，view and viewController 會自動更新 trait collection，並提供備援，就算 view 不在 visible 時也一樣，且許多常見的 override 也會被 track，這表示不需要明確監控變更，系統會追蹤哪些 trait collection property 正在被使用，用於常見的版面配置和繪製方法，例如： `layoutSubviews, updateProperties, drawRect` …等等。 

![Screenshot 2026-07-10 at 17.36.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_17.36.36.png?raw=true)

若偵測到受追縱 trait 變更，系統會確保這些 methods 再次被呼叫，讓 UI 自動相應更新，可查看

[Automatic trait tracking](https://developer.apple.com/documentation/uikit/automatic-trait-tracking) - Documentation

額外補充 iOS 18  開始的 Automatic trait tracking，簡單來說就是要簡化 trait 變化的處理過程。

trait ([UITraitCollection](https://developer.apple.com/documentation/uikit/uitraitcollection)) 是什麼呢？他在 UIKit 中用於在 layer 結構中向 ViewController 和 View 傳遞數據，例如 [horizontalSizeClass](https://developer.apple.com/documentation/swiftui/environmentvalues/horizontalsizeclass), [preferredContentSizeCategory](https://developer.apple.com/documentation/uikit/uiapplication/preferredcontentsizecategory)…等。

trait 是要解決 *自適應用戶界面*（Adaptive User Interfaces）的概念，所以 UITraitCollection 就是當前 iOS 在介面上的「環境變數集合」

- **Size Classes (尺寸類別):**
    - `horizontalSizeClass`：水平空間（Compact / Regular）
    - `verticalSizeClass`：垂直空間（Compact / Regular）
- **User Interface Style (外觀風格):**
    - `userInterfaceStyle`：判斷現在是**淺色模式 (Light)** 還是**深色模式 (Dark)**，這是實作暗黑模式最常用的屬性。
- **Dynamic Type (動態字級):**
    - `preferredContentSizeCategory`：使用者設定的文字大小偏好。
- **User Interface Idiom (裝置類型):**
    - `userInterfaceIdiom`：判斷現在執行的設備是 iPhone (`.phone`)、iPad (`.pad`)、Mac (`.mac`) 還是 Apple TV (`.tv`)。
- **Display Scale (螢幕縮放比例):**
    - `displayScale`：螢幕的像素密度（例如 1x, 2x Retina, 3x Super Retina）。

在 automatic trait tracking 不可用時，我們可以透過 `registerForTraitChanges` 來偵測變更

![Screenshot 2026-07-10 at 17.47.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_17.47.29.png?raw=true)

此 method 可讓你設定一個 closure，來接收 view 的 specific trait 發生變更時的通知，且可以使 cache 失效或更新該 view 相關的其他資料。

其他細節可參考 

[Adapting your app when traits change](https://developer.apple.com/documentation/uikit/adapting-your-app-when-traits-change) - Documentation

### **Accessing screen geometry**

screen 的另外一種常見用途是檢查其 bounds 以取得 app 的可用 space (空間量)，在 adaptive environment 中，scene 的可用 space 並不是完整的 screen 

- Scenes are not always full-screen
在 adaptive environment 不要在用 screen bounds，現在是移除時機
- Check `UIWindowScene.effectiveGeometry` 
原本透過 window scene’s effective geometry 可以拿到多少可以用 space，現在需要監控變化的話，請在 scene delegate implement `windowScene:didUpdateEffectiveGeometry:`

![Screenshot 2026-07-10 at 15.34.42.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_15.34.42.png?raw=true)

在 View and ViewController 中要取得可用 space 記得要使用當前 view or view’s superview，不要 reference scene bounds ，這樣可以讓 UI  減少如何呈現方式的依賴（對 environment 也是），讓 viewController 在其他情境中也能更好地調整，例如 inside of a split view controller

![Screenshot 2026-07-10 at 15.42.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-10_at_15.42.03.png?raw=true)

### UIRequiresFullscreen

![Screenshot 2026-07-11 at 13.01.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_13.01.44.png?raw=true)

對 game 來說，調整大小很有挑戰性，因此 UIRequiresFullscreen，在 iPhone 可以調整大小的環境中從 iOS 27 開始支援，其行為也已更新，不在讓 app 完全退出調整大小，而是透過 discrete resizing 調整大小，並遵循你支援的 interface orientations

- Honored on iPhone
- Discrete resizing
- Honors supported orientations

在 discrete resizing 調整大小中，每次 user 變更 scene size 時，這時 system 會將 scene 轉換到新的螢幕設定，以符合該大小，讓 game 始終在可用空間中以完整品質 render。

## User interface idiom

假如有使用到 User Interface idiom trait，要注意，此 trait 對於任何 layout decisions 都不在有意義。

### Use size classes

![Screenshot 2026-07-11 at 13.14.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_13.14.31.png?raw=true)

- Idiom is `phone` on iPad and iPhone Mirroring
當 iPhone app 在 iPad 上執行或在 Mac 上的 iPhone Mirroring 時，它將可以隨意調整大小，但拿到的 user interface idiom 都會 `phone` 。
- No longer valid for size constraints
停止在 code 中為任何 layout decisions 使用 user interface idiom
- Use size classes or view bounds
改用 size classes 來處理 size constraints，例如 collapsing menus 以及為可用空間更新 app 的版面配置

如果需要更精細的控制，請使用周圍 view 的大小。

## Interface orientation

Interface orientation 也一樣，不適合在用於 layout decisions

### Responding to bounds changes

![Screenshot 2026-07-11 at 13.28.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_13.28.00.png?raw=true)

- Supported interface orientations ignored
在 iOS 27 中，App 支援的 interface orientation 是提供系統的偏好設定，當 app 可調整大小的環境中執行時，此設定將被忽略，不該再將 interface orientations 納入任何 layout 計算中
- Always `portrait` in iPhone Mirroring
在 Mac 上的 iPhone Mirroring 中，app 始終都是 `portrait` interace orientations，無論 app 場景的長寬比為何
- Use size classes and scene bounds
app 中任何 interface orientation 的檢查，也應更新為 size classes，這個概念轉變在 iOS 8 中引入

![Screenshot 2026-07-11 at 13.30.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_13.30.47.png?raw=true)

在 WWDC14 上，Bruce Nilo 說：「A device rotation is only an animated bounds change.」

隨著各種裝置尺寸、iPad 上可以調整大小的視窗，以及 Mac 上可以調整大小的 iPhone app，今天這個洞見也比以往任何時候都更具相關性。

在 iOS 27 中，UIView 也 conform 新的 Body protocols - CoreMotion and CoreLocation，這使得設定動作和位置管理變得更加容易，將他們連接到視覺化 motion data 的 View，例如 compass or map view，這確保資料始終在正確的座標空間中。

![Screenshot 2026-07-11 at 13.55.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_13.55.59.png?raw=true)

以上涵蓋了主要的 adaptivity changes

接下來說如何在 app 中測試這些變更，在 Xcode 27 帶來了一個新方式，來跨越不同的 screen size，無需再多個獨立 simulator 或 device 安裝 app。

在新的 Device Hub app 以及 Xcode Previews 中，可以點擊「enter resize mode」icon， 然後就可以拖動裝置邊緣來自由調整大小，一但對結果滿意，請最後確保使用 real device 檢查。

![Screenshot 2026-07-11 at 14.11.10.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.11.10.png?raw=true)

![ezgif-1e7c34578aade1b3.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/ezgif-1e7c34578aade1b3.gif?raw=true)

對於 Device Hub app 的細節可以參考

![Screenshot 2026-07-11 at 14.08.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.08.04.png?raw=true)

[Get the most out of Device Hub](https://developer.apple.com/videos/play/wwdc2026/260/) - WWDC26

# Bars and menus

介紹 tab bars, navigation bars and menus 的新 API

### Tab Bar Controller

在 iPad 上，Tab bar 可以展開為完整的側邊欄呈現方式，以顯示 app 階層的更多 section

![Screenshot 2026-07-11 at 14.27.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.27.03.png?raw=true)

![Screenshot 2026-07-11 at 14.24.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.24.35.png?raw=true)

而在 iPhone 上原本 tab bar 都是在底部，但現在也可以變成 sidebar 形式，在 iOS 27 有新 API

![Screenshot 2026-07-11 at 14.29.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.29.12.png?raw=true)

但注意，與 iPad 不同，這是 app 的選擇，如果 app 選擇 sidebar 的呈現方式，則無法在 UI 中切換 tab bar or sidebar。

### Sidebar availability

系統會判斷是否有足夠空間顯示 sidebar，例如當 horizontal size is regular 時

- `tabBarController.sidebar.isAvailable`
要確定 tab bar 的 sidebar 是否可顯示，可以用 isAvailable property
- Add UI to reach nested tabs
如果目前無法使用 sidebar，記得在 app 的其他部分顯示 nested tab 背後的 UI（就是 sidebar 其他要呈現的 item）

要深入了解更多管理 tab bar 可參考

- [Make your UIKit app more flexible](https://developer.apple.com/videos/play/wwdc2025/282/) - WWDC25
- [Elevate your tab and sidebar experience in iPadOS](https://developer.apple.com/videos/play/wwdc2024/10147/) - WWDC24

在 iOS 27 中，UITabBarController 還可自訂 prominent tab，讓它始終顯眼，即使在 scroll 後 collapses 時 

![Screenshot 2026-07-11 at 14.42.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.42.06.png?raw=true)

以上是 tab bar 的介紹，接下來是 navigation bar

navigation bar 可以在 user scroll 時，以互動式的滑出去不見，這為了讓 app 內容有更多空間顯示

![Screen Recording 2026-07-11 at 14.47.49.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screen_Recording_2026-07-11_at_14.47.49.gif?raw=true)

預設情況下是這樣，但我們也可以自行設定這些最小化行為

![Screenshot 2026-07-11 at 14.53.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_14.53.01.png?raw=true)

### Scroll edge effect

在 scroll 的過程中另一個變更是 edge effect 的 appearance 更新，因此

![Screen Recording 2026-07-11 at 14.58.16.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screen_Recording_2026-07-11_at_14.58.16.gif?raw=true)

![Screenshot 2026-07-11 at 15.10.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.10.08.png?raw=true)

- Review your UI 
我們應該審視一下設計，特別是之前有 override 系統預設值的地方
- Favor `.automatic` over `.soft` & `.hard` 
特別是 automatic 樣式不在切換於現有的 soft 和 hard 樣式之間，而是提供自己的視覺效果以提高清晰度
- Reconsider custom cases
如果之前從 automatic override 了樣式，應重新評估該決定，特別是設為 soft 時，因為這不在符合預設的系統外觀

### Menu image visibility

![Screenshot 2026-07-11 at 15.21.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.21.35.png?raw=true)

- Images not always shown
隨著 Liquid Glass 精緻的外觀，在 menu 元素上設定影像，在某些情境中可能預設不顯示，例如在 iPadOS 和 macOS 的 menu bars
- Set `preferredImageVisibility` 
如果還是需要顯示影像，可以用 preferredImageVisibility property，以 override the default system behavior

這部分可以 review 更新後的 [Human Interface Guidelines - Menus](https://developer.apple.com/design/human-interface-guidelines/menus)

# Apple Intelligence

如何在 app 中支援 Apple Intelligence 以及一些該注意的變更

在 iOS 27 中的 menu 包含一個「Ask Siri」button，讓 user 可以直接從 app 開始與 Siri 對話，這是一個強大的入口點，讓 user 能夠互動他們關心的內容

![Screen Recording 2026-07-11 at 15.23.51.gif](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screen_Recording_2026-07-11_at_15.23.51.gif?raw=true)

### Ask Siri

![Screenshot 2026-07-11 at 15.30.33.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.30.33.png?raw=true)

- Appears automatically when relevant
當有與 Siri 相關的內容時，menu 會自動顯示此項目
- New API for individual views
若要提供更多特定與 app 相關資訊，請使用新的 View Annotations API
- Provide information as `AppEntity` 
透過它，可以用 AppEntities annotate 特定的 view

細節可參考

[Explore advanced App Intent features for Siri and Apple Intelligence](https://developer.apple.com/videos/play/wwdc2026/343/) - WWDC26 

### Drag and drop

![Screenshot 2026-07-11 at 15.37.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.37.46.png?raw=true)

- Apple Intelligence accesses drag and drop content
如果 app 有支援 drag and drop 功能，Siri 可以從 app 拖曳的內容載入資源
- Calls `UIDragInteractionDelegate` 
當從內容 menu 中 call Apple Intelligence 時，system 將 call `UIDragInteractionDelegate` 來載入內容
- Perform animations in `sessionDidMove`
避免在 sessionWillBegin 中，執行動畫或呈現強制回應 UI，drag 工作階段中可以在沒有 user gesture 的情況下啟動，如果 app 有一個在 user 啟動 drag 時顯示的有狀態 UI，要記得把相關的 code 放進 `sessionDidMove`

# Agentic Coding

介紹 new skill，可以幫助 agent 自動處理大部分 modernization work

前面講了很多為了 adaptivity 的變更，感覺工作量很大，但不用擔心，有準備好了，Agentic Coding

![Screenshot 2026-07-11 at 15.43.25.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.43.25.png?raw=true)

在 Xcode 27 的新功能，是一項 app modernization skill，它對於 adaptivity task 瞭若指掌，並且根據專案情境，可以自動完成許多變更

![Screenshot 2026-07-11 at 15.49.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.49.18.png?raw=true)

- Interface orientation references
它還將以 size classes checks 取代 interface orientation checks，
- App lifecycle to scene lifecycle
他甚至會將 app 轉換成 scene lifecycle
- Interactive conversation
對於更複雜的任務，他會訊問澄清問題
- To-Dos for remaining tasks
對於單次工作階段，無法處理的大型任務，他會加入 comments 幫助追蹤未完成的任務

若要在其他 tools 中使用 skill，可以使用此指令匯出 Xcode 使用的 skill，`xcrun agent skills export`

![Screenshot 2026-07-11 at 15.51.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC26/Modernize%20your%20UIKit%20app/Screenshot_2026-07-11_at_15.51.01.png?raw=true)

這將建立 markdown file，然後可以在其他工作流程中匯入

# Next steps

- Use Device Hub and iPhone Mirroring
使用 iOS 27 SDK building app，並在新的 Device Hub app 中，試用可調整大小的 simulator，並在 macOS 27 上的 iPhone Mirroring 測試 app
- Make your app adaptive
找出 app 中需要更靈活的區域
- Run the skill
嘗試 new skill，並發現他可以自動完成多少工作

# Resources

https://developer.apple.com/videos/play/wwdc2026/278/