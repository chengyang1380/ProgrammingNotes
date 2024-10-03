# Explore enhancements to RoomPlan

![New Zealand - Dunedin](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/IMG_9338.jpeg?raw=true)

New Zealand - Dunedin

觀看 **WWDC23 Explore enhancements to RoomPlan** 筆記

## Recap

RoomPlan 使用 ARKit 提供的先進機器學習算法，可以檢測牆壁、窗戶、門以及其他房間的物品。

RoomCaptureView API 可幫助將掃描體驗的結果直接整合到 App 中，最後還可以導出 USDZ 文件

## Custom ARSession support

可結合使用 RoomPlan 和 ARKit 的新方法

我們使用 `RoomCaptureSession` 時，預設用 `ARSession` 作為核心運作

![截圖 2024-09-23 16.20.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_16.20.14.png?raw=true)

但現在 iOS 17 我們可以自定義 `ARSession` 和 `ARWorldTrackingConfiguration`，從而我們可以以新的方式，在同一個工作流程中結合使用 `RoomPlan` 和 `ARKit` 

![截圖 2024-09-23 16.21.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_16.21.03.png?raw=true)

### Examples

Increase immersion: 其中一種結合使用 `RoomPlan` 和自定義 `ARSession` 的方式，就是將 `RoomPlan` 的結果與 `ARKit` 的場景幾何和平面檢測進行整合，已在虛擬內容和真實世界幾何之間創造更加沈浸式的互動

Add photos and videos to RoomPlan scans: 使用 `ARKit` 的高質量圖像捕捉，來收集空間的照片顯示，同時結合 `RoomPlan` 以創造更豐富的房屋 3D 模型圖列表

Seamlessly integrate RoomPlan with your ARKit app: 將 `RoomPlan` 作為現有 AR 體驗的一部分，可以在不破壞現有 `ARAnchors` 的情況下，整合來自 `RoomPlan` 的結果

現在 `RoomCaptureSession` 改成可以在 init input 客製化 `ARSession` 。

還有在 `stop` function 新增參數

![截圖 2024-09-23 17.30.48.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_17.30.48.png?raw=true)

## MultiRoom support

新的 MultiRoom API 用於將單獨的房間掃描合併為更大的結構

原先掃描了多個房間，最後要合併時，會遇到每個 3D Model 的座標原點和方向都是不同的，要合併有困難

![截圖 2024-09-23 17.35.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_17.35.54.png?raw=true)

其次，就算手動拼裝起來，但有些重疊的部分會有問題，例如：牆壁，會變成重複的牆

![截圖 2024-09-23 17.36.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_17.36.50.png?raw=true)

### Capture for MultiRoom

座標系統不同的問題：

我們的目標是要讓所有掃描結果都位於相同的座標系統中，因此我們可以採用兩個方法來解決

- Continuous ARSession
- ARSession relocalization

### Continuous ARSession

在此之前的 `RoomPlan` 中，如果 `RoomCaptureSession` 停止， `ARSession` 就會暫停，所以每次的掃描都具有不同的座標系統

![截圖 2024-09-23 17.41.23.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_17.41.23.png?raw=true)

而現在 API `stop` function 中有新的參數，可將 `pauseARSession` 設置為 false，這樣的話 `ARSession` 可以繼續進行下一次掃描，直到我們再次暫停 `ARSession` 為止，使用這種方法，我們可以確保各次掃描中，執行的都是同一個 `ARSession` ，這樣就可以保證所有掃描結果共用一個世界座標系統

![截圖 2024-09-23 17.45.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_17.45.24.png?raw=true)

![截圖 2024-09-23 18.12.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_18.12.55.png?raw=true)

### ARSession relocalization

這方法適合用於需要分幾次對單個房間進去掃瞄的情況，例如隔天或隔週在對同一個位置進行掃描

主要做法是要把那次掃描裡的 `ARWorldMap` 儲存起來，而後續的掃描可以將上次儲存的 `ARWorldMap` 拿來使用，進行重新定位，然後這樣也可以確保一系列的掃描結果都是共享同一個座標系統

![截圖 2024-09-24 09.57.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_09.57.36.png?raw=true)

![截圖 2024-09-24 10.20.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.20.27.png?raw=true)

![截圖 2024-09-24 10.20.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.20.52.png?raw=true)

### 如何將掃描結果合併成單一個組合結構

在每次掃描中，我們都會使用 `RoomBuilder API` 來生成單獨的 `CapturedRoom` ，然後就像之前展示的，提到的兩個方法 `Continuous ARSession` 和 `ARSession relocalization` ，所有 `CapturedRoom` 都會位於同一個 3D 空間中

![截圖 2024-09-24 10.25.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.25.26.png?raw=true)

下圖是 `RoomBuilder` 的輸出，包含了 3 個 `CapturedRooms` ，然後我們可以使用一個新的合併 API `StructureBuilder` 來將這些 `CapturedRooms` 合併為一個大結構（ `CapturedStructure` ）

![截圖 2024-09-24 10.29.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.29.52.png?raw=true)

接下來看 API 如何使用

![截圖 2024-09-24 10.31.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.31.26.png?raw=true)

![截圖 2024-09-24 10.39.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_10.39.57.png?raw=true)

### Considerations f or MultiRoom

Single-floor residential house: 最適合單層住宅，通常包含一到四個臥室、廚房及客廳

House area: 2000 sq ft: 建議最大總面積不要超過 2,000 平方英尺，約 186 平方公尺，約 56 坪

Lighting: minimum 50 lux: 建議使用 50 勒克司或更高的良好照明，這樣才有更好的影像品質以及 AR 跟蹤的表現

## Accessibility

可以在使用 RoomCaptureView 時有旁白輔助，提供聲音反饋的功能，對看到的內容進行描述

## Representation improvements

RoomPlan 的改進

現在針對掃描的能力有大大的提升，例如：傾斜和彎曲的牆壁、物品的識別：洗碗機、烤箱、水槽等，嵌入式廚房物品

![截圖 2024-09-24 11.37.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_11.37.36.png?raw=true)

單座、L 型或簡單的方形沙發都能檢測

![截圖 2024-09-24 11.38.23.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_11.38.23.png?raw=true)

### CapturedRoom structure

新增了一些東西

![截圖 2024-09-24 11.45.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_11.45.58.png?raw=true)

現在多一個 `Section` 

 Surface 新增的部分

- Polygon，專門處理一些彎曲的東西，例如彎曲的牆壁
- Category → floor 地板

Object 新增的部分

- Attributes，已對 Category 內不同的配置進行更好的描述

Surface 和 Object 共同有的特性多了 Parent

例如窗戶的 Parent 是牆，椅子的 Parent 是桌子，洗碗機的 Parent 是儲物櫃

![截圖 2024-09-24 14.23.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.23.58.png?raw=true)

接下來一些範例，更詳細了解這些改變

![截圖 2024-09-24 14.27.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.27.01.png?raw=true)

前面提到的 Polygon 多邊形的牆壁也能偵測出來

![截圖 2024-09-24 14.30.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.30.39.png?raw=true)

floor 地板也能處理  

![截圖 2024-09-24 14.38.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.38.40.png?raw=true)

洗碗機、烤箱和水槽的 Parent 會嵌在畫面中

![截圖 2024-09-24 14.41.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.41.59.png?raw=true)

我們之前會用 Category 來描述物品，但是會有一些侷限，例如椅子，這個椅子 Category 可以存在多種類型，像是凳子、餐椅及辦公椅等，而且這些椅子的功能也各不相同。

為了更好表示這些物品，現在多了剛剛提到的 Attributes ，來精準描述物品內容

![截圖 2024-09-24 14.43.23.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.43.23.png?raw=true)

## Enhanced export function

導出功能的增強以實現新工作流程

### UUID Mapping

透過專門的 UUID 可以在 USDZ 檔案中與 Room 上的物品作關聯

![截圖 2024-09-24 14.51.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.51.21.png?raw=true)

以前要把 `CapturedRoom` 導出時，有兩個方向，一個是接導出 USDZ 格式的檔案，另外一個則是可以在 Encode 後導出 JSON/Plist 檔案，而現在在 `export` function 多一個參數 `metadataURL: URL?` 。

這 metadata 導出後可以對兩邊（USDZ 及 Metadata ）的資料進行關聯，這樣看已掃描的房間時，就可以查詢 Surface 或 Object 的附加資料

![截圖 2024-09-24 14.54.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_14.54.52.png?raw=true)

### Model Provider

將 Category 和 Attribute 映射到 Model URL

![截圖 2024-09-24 15.10.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.10.36.png?raw=true)

所以最後導出的 USDZ 檔案會包含這些 model （Sections, Surfaces, Category）

![截圖 2024-09-24 15.13.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.13.17.png?raw=true)

### Example

如何建立自己的小型資源目錄 (3D model catalog)

![截圖 2024-09-24 15.21.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.21.16.png?raw=true)

**Parse categories and attributes**

首先解析 `RoomPlan` 支持的 categories 及 attributes，下方的 code 就可以把目錄的內容準備就緒

![截圖 2024-09-24 15.22.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.22.05.png?raw=true)

**Associate models**

接著為每個 category 和 attribute 關聯一個模型 

![截圖 2024-09-24 15.23.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.23.08.png?raw=true)

最後再把索引文件保存成 Plist，並將目錄保存為 Bundle 文件

![截圖 2024-09-24 15.26.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.26.38.png?raw=true)

**Instantiate ModelProvider**

然後實例化一個 Model Provider

每次導出 RoomModel 或希望使用 `Model Provider`  將物品關聯到模型時，可以使用該目錄的 Bundle 文件，生成一個 `Model Provider` ，而我們需要做的就是把 `catalog.categoryAttributes` 找到對應的 `modelURL` ，然後再沒有 attributes 的情況下將 `modelURL` 關聯到目錄

![截圖 2024-09-24 15.44.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.44.04.png?raw=true)

**Export CapturedRoom** 

最後使用這個 instance 導出一個 `CaptruedRoom` ，這個 USDZ 檔其中的 3D 模型就與掃描完全對應了，假如要獲得更好的效果，可以導入到 Blender 等 DCC 工具中，增加光照和陰影

![截圖 2024-09-24 15.50.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Explore%20enhancements%20to%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-24_15.50.34.png?raw=true)

## Wrap-up

Custom ARSession suprot: 自定義的 `ARSession` ，例如可以在掃描過程中捕獲高質量的影像跟聲音 

New MultiRoom experiences: 可以對多個房間進行掃描，並使用新的 `StructureBuilder API` 對整個房屋生成合成 3D 模型

Accessibility improvements: 改善視力不佳用戶的掃描體驗，支持旁白

More accurate representation:  更精準描述掃描的房間

New workflows to customize RoomPlan output: 可以使用最新的導出 API，將自定義目錄中的 3D 模型分配給相應的掃描物品

## 參考

https://developer.apple.com/videos/play/wwdc2023/10192/