# Meet Object Capture for iOS

![New Zealand - Greenpoint Ship Graveyard](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/DJI_0259.jpeg?raw=true)
New Zealand - Greenpoint Ship Graveyard

觀看 **WWDC23  Meet Object Capture for iOS** 筆記

## Object Capture recap

先拍攝各種角度的照片，再透過 Mac 的 Object Capture API，經過幾分鐘轉換，最後變成 3D 模型

![截圖 2024-09-19 09.57.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_09.57.22.png?raw=true)

然而現在可以直接透過 iPhone 直接做到這些事情

![截圖 2024-09-19 09.59.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_09.59.55.png?raw=true)

![截圖 2024-09-19 10.00.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_10.00.20.png?raw=true)

![截圖 2024-09-19 10.01.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_10.01.19.png?raw=true)

![截圖 2024-09-19 10.01.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_10.01.49.png?raw=true)

但需要花到好幾分鐘的時間，上面的範例預計就要15分鐘才生成，最後產出 USDZ 格式的模型。

Sample code →  [**Scanning objects using Object Capture**](https://developer.apple.com/documentation/realitykit/scanning-objects-using-object-capture)

另外也可參考 WWDC 24 [Discover area mode for Object Capture](https://developer.apple.com/wwdc24/10107/) 

## More objects with LiDAR

原本的 Object Capture 系統在面對足夠紋理細節的物體，表現最佳

![截圖 2024-09-19 10.51.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_10.51.07.png?raw=true)

但在 23 年 Apple 也進一步升級，使用了 LiDAR 掃瞄器，對一些紋理不足的物體進行重構，所以更好掃描了。

![截圖 2024-09-19 10.52.33.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_10.52.33.png?raw=true)

### Challenging objects

避免表面反光、透明或包含纖細結構的物體進行建模

- Reflective
- Transparent
- Too-thin structures

## Guided capture

Apple 建議盡可能用多種角度拍攝圖像

### Automatic capture

![截圖 2024-09-19 11.58.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_11.58.57.png?raw=true)

提供一個刻度圓盤來輔助拍攝

### Capture feedback

- Light

拍攝前確保環境有良好的照明，以便獲得準確的顏色，太暗會跳出提醒

![截圖 2024-09-19 14.02.11.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.02.11.png?raw=true)

- Speed

要緩慢而流暢繞物體移動，保持穩定，防止出現模糊的影像，過快的移動也會有提醒

![截圖 2024-09-19 14.04.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.04.16.png?raw=true)

- Distance

保持適當的距離，使物體在取景匡內的位置恰到好處，太近或太遠也會提醒

![截圖 2024-09-19 14.05.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.05.39.png?raw=true)

- Field of view

固定好機位，假如移動到視窗太遠的地方也會被提醒

![截圖 2024-09-19 14.06.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.06.28.png?raw=true)

### Flipping objects

適合翻轉：

- 翻轉不宜變形，例如石頭
- 紋理豐富的物體也推薦進行翻轉

不適合翻轉：

- 容易因為移動而變形，像是 Pizza 就不那麼適合了
- 有對稱或重複紋理的物體，這樣可能對系統產生誤導
- 此外無紋理的物體在 Object Capture 系統中難以實現，因為通常都需要足夠的紋理，來縫合不同區塊

![截圖 2024-09-19 14.12.53.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.12.53.png?raw=true)

### Flippable

Apple 也有提供 API 來確保物體是否有足夠的紋理支持翻轉拍攝

- 3 orientations: 推薦三種方向進行掃描，以便獲取各種角度的圖像
- Diffuse lights: 最好使用漫射光進行照明，以最大程度減少物體表面的反光及陰影
- Visual overlap: 在多輪掃描中使影像重疊也很重要，所以當次與下次的掃描，某些影像應該要重複重疊

### Non-flippable

- 3 heights: 若物體真的不能翻轉，Apple 建議從三種不同高度進行拍攝，以獲取不同角度
- Textured background: 拍攝無紋理物體時，建議使用有紋理的背景，使主體顯得突出

### Supported devices on iOS

- iPhone 12 Pro and laster with LiDAR sensor
- iPad Pro 2021 and later with LiDAR sensor

## iOS API

![截圖 2024-09-19 14.40.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_14.40.58.png?raw=true)

從剛剛的 demo 得知，我們使用 Object Capture 有兩個步驟

1. 影像捕捉
2. 模型重建

### Image Capture API

影像捕捉 API 分為兩部分

- ObjectCaptureSession
    
    在捕獲的過程中觀察和控制狀態工作流
    
- ObjectCaptureView (SwiftUI)
    
    展示拍攝素材，並基於 session 狀態，自動調整它所呈現的 UI 元素
    
    而使用 ObjectCaptureView，它是不帶任何 2D 文本或按鍵，讓我們可以自定義 View 的外觀，並更輕鬆整合到現有的 App 中
    

### Object Capture states

![截圖 2024-09-19 15.02.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.02.28.png?raw=true)

![截圖 2024-09-19 15.03.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.03.14.png?raw=true)

因為他是 reference type，推薦將 session 以持久狀態存儲在基於真實數據模型中，直到 session 完成。

![截圖 2024-09-19 15.13.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.13.50.png?raw=true)

透過 `ObjectCaptrueSession.start(imageDirectory:configuration:)` ，先決定將捕捉的圖像儲存在何處，或透過 `ObjectCaptureSession.Confoguration.checkpointDirectory` 來檢查目錄

![截圖 2024-09-19 15.21.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.21.35.png?raw=true)

再來使用 `ZStack` 包著 `ObjectCaptureView(session:)` 並把剛剛準備的 session 傳入就可以使用了，而且他附帶著對準的輔助視窗，輔助我們將物體放置框框內

![截圖 2024-09-19 15.22.33.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.22.33.png?raw=true)

我們可以透過 `session.state` 來判斷是否到 `.ready` 的狀態，是的話可以呈現一顆按鈕，來提供開始掃描物體的功能（使用 `session.startDetecting()` ）

![截圖 2024-09-19 15.22.11.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.22.11.png?raw=true)

在 demo 中，還有一個 Reset button，可以讓我們從狀態 **Detecting** 返回 **Ready**

![截圖 2024-09-19 15.28.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.28.07.png?raw=true)

![截圖 2024-09-19 15.29.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_15.29.47.png?raw=true)

假如狀態切到 `.detecting` ，表示我們偵測到物體，此時我們就可以開始拍攝（使用 `session.startCapturing()` ）

![截圖 2024-09-19 17.21.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.21.59.png?raw=true)

為了讓掃描得更準，Apple 推薦進行三輪掃描

所以接下來有兩條路可以走

第一，取決是否要翻轉物體，這樣就可以拍攝到前次沒拍到的部分，例如物體底部，此狀況可以 call `beginNewScanPassAfterFlip()`  

![截圖 2024-09-19 17.24.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.24.26.png?raw=true)

第二，不想要翻轉拍攝物體，但可以選擇不同的高度拍攝，這時可以 call `beginNewScanPass()` ，這時會重置捕捉刻度盤，但 session 會處於拍攝狀態

![截圖 2024-09-19 17.26.33.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.26.33.png?raw=true)

![截圖 2024-09-19 17.27.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.27.49.png?raw=true)

再來，三輪拍攝都結束後，會觸發 `session.userCompletedScanPass` 可以出現按鈕讓 user 呼叫 `session.finish()` 

![截圖 2024-09-19 17.30.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.30.01.png?raw=true)

然後 session 會等待數據進行存儲，存完後就會自動移至 Completed

![截圖 2024-09-19 17.30.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.30.47.png?raw=true)

但也有可能遇到儲存失敗，此時就需要重新生成 session

### Point Cloud view

![截圖 2024-09-19 17.32.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.32.27.png?raw=true)

透過 `ObjectCapturePointCloudView(session:)` ，可以展示剛剛拍攝的物體，而且點擊互動，進行各種角度預覽。  

最後拍攝完建立模型

![截圖 2024-09-19 17.33.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.33.52.png?raw=true)

細節可以參考 WWDC 21 [Create 3D models with Object Capture](https://developer.apple.com/wwdc21/10076)

### Detail level on iOS

![截圖 2024-09-19 17.37.37.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.37.37.png?raw=true)

為了讓手機端可以生成模型和預覽優化，所以在 iOS 上支持簡化這一細節等級，重建的模型包含漫反射、環境光遮蔽和髮線的紋路映射，均為異動設備顯示而設計

**假如產生更多細節紋路，還是要把圖像傳到 Mac 上建立**

也可以透過 Mac 再加入拍攝細節

![截圖 2024-09-19 17.40.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.40.03.png?raw=true)

其他細節要參考 WWDC 23 [Meet Reality Composer Pro](https://developer.apple.com/wwdc23/10083)

甚至有可能不需要寫任何 code，因為 Object Capture 已經整合到了新 macOS App，就是 Reality Composer Pro

## Reconstruction enhancements

Increased performance on Mac: 目前已大大提升 Mac 上模型質量和重建速度

Estimated processing time: 還提供預計重建時間

Pose output: 位姿輸出，可以要求每幅圖像的高質量位姿，每項位姿都包含了拍攝該圖片時預計的相機位置和朝向 

![截圖 2024-09-19 17.49.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.49.38.png?raw=true)

![截圖 2024-09-19 17.50.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.50.20.png?raw=true)

Custom detail level: 自定義細節等級

![截圖 2024-09-19 17.50.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Meet%20Object%20Capture%20for%20iOS/%25E6%2588%25AA%25E5%259C%2596_2024-09-19_17.50.39.png?raw=true)

## 參考

https://developer.apple.com/videos/play/wwdc2023/10191/