# Discover advancements in iOS camera capture: Depth, focus, and multitasking

![IMG_81992.jpeg](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/IMG_81992.jpeg?raw=true)

New Zealand — Deer Park Heights Queenstown

觀看 **WWDC22 Discover advancements in iOS camera capture: Depth, focus, and multitasking** 筆記

## LiDAR Scanner

LiDAR 掃描儀的 API 在 iPadOS 13.4 的 ARKit 中首次引用，細節可參考 WWDC20 Explore ARKit 4

在 iOS 15.4 有一個新的功能，可以讓 App 使用 `AVFoundation` 訪問 LiDAR 掃描儀

- `AVCaptureDevice.DeviceType`
- `.builtInLiDARDepthCamera`

LiDAR 深度相機可以提供多種格式

![截圖 2024-09-18 16.33.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_16.33.52.png?raw=true)

LiDAR Depth Camera

- Absolute depth
- Real-world scale
- Great for computer vision

這讓它很適合測量等計算視覺的任務

TrueDepth, Dual, Dual Wide, and Triple

- Relative, disparity-based depth
- Uses less power
- Great for photo effects

而且三顆鏡頭都會啟動，可以生成更相對，基於視差的深度，這會消耗更少的電量，非常適合需要呈現照片效果的 App

`AVFoundation` 使用 `AVDepthData` 來表示深度

使用 `AVFoundation` 會比用 `ARKit` 好嗎？

![截圖 2024-09-18 16.49.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_16.49.04.png?raw=true)

假如要知道更多 `AVFoundation` 的 depth 細節，可參考 WWDC17 [Capturing Depth in iPhone Photography](https://developer.apple.com/wwdc17/507)

## Face-drive AF/AE

iOS 15.4 有一項新功能 `Prioritize faces` ，焦點和曝光系統將為臉部優先（iPhone 13 Pro 首次出現電影模式，在 iOS 15.4 才開放此 API）

![截圖 2024-09-18 16.59.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_16.59.19.png?raw=true)

## Advanced streaming

`AVCaptureSession` manages data flow

`AVCaptureInput` provides data

- Cameras and Microphones

`AVCaptureOutput` delivers data

- Video, audio, photos, and more

![截圖 2024-09-18 17.20.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.20.26.png?raw=true)

### Multiple video outputs

現在 iOS 16, iPad OS 16 可以一次使用多個 `AVCaptureVideoDataOutputs`

並且可以 Customize 

- Resolution
- Stabilization
- Orientation
- Pixel Format

![截圖 2024-09-18 17.30.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.30.28.png?raw=true)

更多 `AVCaptureVideoDataOutput` 可參考 [**TN3121: Selecting a pixel format for an AVCaptureVideoDataOutput**](https://developer.apple.com/documentation/Technotes/tn3121-selecting-a-pixel-format-for-an-avcapturevideodataoutput)

iOS 16/ iPad OS 16 除了剛上述說的可以使用多個影片數據輸出，還可以從 `AVCaptureVideoDataOutput` 和 `AVCaptureAudoiDataOutput` 接收數據時，使用 `AVCaptureMovieFileOutput` 進行錄製。

![截圖 2024-09-18 17.34.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.34.21.png?raw=true)

![截圖 2024-09-18 17.35.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.35.13.png?raw=true)

## Multitasking camera access

之前在多項任務同時執行時，例如在 iPad 上可以一次同時多個視窗，同時開著相機 app 和其他 app（例如遊戲），但這樣會影想相機的效能，導致幀數不夠或延遲，最終導致相機鏡頭的輸入不佳。

提供良好的相機使用體驗是首要任務

但現在可以了多任務了，只是會提醒使用者影片的品質會變差

![截圖 2024-09-18 17.40.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.40.44.png?raw=true)

在 iOS 16 後新增的 API

![截圖 2024-09-18 17.41.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.41.46.png?raw=true)

### Fullscreen camera experience

但為了品質好，不要讓其他 app 搶資源，也可以限制，例如 ARKit 不支持在處理多任務時使用相機

### Maintaining performance while multitasking

真的需要時也可以採取一些措施，例如降低 fps 速率，分辨率

Responding to system pressure notifications 

- Lowering frame rate

Choosing less demanding video formats

- Lower-resolution
- Binned
- Non-HDR

想了解更多保持性能的最佳實現細節，可參考 [**Accessing the camera while multitasking on iPad**](https://developer.apple.com/documentation/avkit/accessing-the-camera-while-multitasking-on-ipad)

現在 App 的用戶可以在 iPad 上進行多任務處理時並接聽視訊電話

![截圖 2024-09-18 17.48.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.48.49.png?raw=true)

`AVKit` 在 iOS 15 中引入了 API  

![截圖 2024-09-18 17.49.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Discover%20advancements%20in%20iOS%20camera%20capture%20Depth/%25E6%2588%25AA%25E5%259C%2596_2024-09-18_17.49.04.png?raw=true)

此部分的細節可參考 [**Adopting Picture in Picture for video calls**](https://developer.apple.com/documentation/avkit/adopting-picture-in-picture-for-video-calls)

## Wrap-up

### LiDAR Scanner

展示使用 `AVFoundation` 的 LiDAR 掃描儀達成 streaming 並傳輸深度資料

### Face-driven AF/AE

透過新的 API，可以快速臉部渲染效果（自動對焦及曝光）

### Advanced streaming

為 App 量身訂製的高級 `AVCaptureSession` streaming 配置

### Multitasking camera access

您的 App 如何在處理多工任務實使用相機

## 參考

- https://developer.apple.com/videos/play/wwdc2022/110429/