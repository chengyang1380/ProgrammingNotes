# Create parametric 3D room scans with RoomPlan

![**New ZealandTe Anau**](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/APC_3795_copy.jpeg?raw=true)
New Zealand - Te Anau

觀看 **Create parametric 3D room scans with RoomPlan** 筆記

## Recap

21 年推出 Object Capture，拍攝真實世界的物體照片，透過 RealityKit 的 Photogrammetry API，合成 App 用的 3D 模型

![截圖 2024-09-20 16.41.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-20_16.41.47.png?raw=true)

那在 Object Capture 之前，還有 Scence Reconstruction API ，可以幫助我們粗略理解，所在空間的幾何結構，並支持在 App 中的 AR 應用

![截圖 2024-09-20 16.50.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-20_16.50.39.png?raw=true)

今年推出新的 framework `RoomPlan` 

它可以支持 LiDAR 的 iPhone, iPad 掃描房間，生成房間參數化的 3D 模型，也可以在 App 中使用房間內的物品

RoomPlan 使用由 ARKit 提供支持的複雜機器學習算法，用於檢測牆壁、窗戶、門及房間內的物品，例如壁爐、沙發、桌子和櫥櫃

而 RoomCaptureView API 使用 RealityKit 實時呈現掃瞄進度

### Applications

這是 Apple 首次剔除了複雜的實施過程（機器學習和計算機視覺算法），我們可以以全新的方式與房間交互

幾個層面敘述 RoomPlan 的應用：

Interior design: 可以預覽牆壁顏色變化，還可以準確計算重新粉刷房間所需的油漆量

Architecture: 輕鬆預覽並實時編輯對其房間佈局的更改

Real estate: 無縫獲得房源的平面圖和 3D 模型

E-commerce:  可以展示實際空間中的物品，而吸引客戶

要使用 RoomPlan 主要有兩種方式

- Scanning experience API：開箱即用掃描體驗
- Data API: 支持我們 app 中，用最合適的案例方式，使用實時參數數據

## Scanning experience API

### RoomCaptureView

它是 UIView 的 subclass

World space feedback 處理世界空間掃描反饋的呈現

Realtime model generation 實時房間模型生成

Coaching and user guidance 輔導和用戶指導

掃描期間會用動線線條呈現檢測到的牆壁、窗戶、開口、門和房間物品

![截圖 2024-09-20 17.16.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-20_17.16.05.png?raw=true)

而且還會實時生成交互式 3D 模型，讓我們一目瞭然了解掃描進度

![截圖 2024-09-20 17.16.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-20_17.16.46.png?raw=true)

### `RoomPlan` API 使用

![截圖 2024-09-23 09.55.32.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_09.55.32.png?raw=true)

最後可以輸出 USDZ 格式的結果檔案

![截圖 2024-09-23 09.56.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan//%25E6%2588%25AA%25E5%259C%2596_2024-09-23_09.56.04.png?raw=true)


## Data API

這部分是提供在掃描期間，訪問底層數據結構的權限，可以從頭到尾建立自訂義的可視化掃描體驗

### Basic workflow

Scan → Process → Export

### Scan

![截圖 2024-09-23 10.47.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_10.47.12.png?raw=true)

![截圖 2024-09-23 10.55.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_10.55.00.png?raw=true)

`var previewVisualizer: Visualizer!`  可以自定義類型來顯示結果 

`arView.session = captureSession.arSession` 這樣 `arView` 就繪製平面和邊界框


![截圖 2024-09-23 11.07.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_11.07.14.png?raw=true)

- `func captureSession(_ session: RoomCaptureSession, didUpdate room: CapturedRoom)`

這是為了得到實時的 `CaptureRoom` 數據結構，接著就可以使用 `previewVisualizer` 來更新 3D 模型的 AR View，這樣就會即時更新並回饋給操作者。

- `func captureSession(_ session: RoomCaptureSession, didProvide instruction: Instruction)`

提供指令結構 （`Instruction` ），其中還有實時回饋，透過 `prviewVisualizer` 在掃描過程中就可以引導操作者

### Instructions

Move closer to wall 物品的距離

Move away from wall 物品的距離

Slow down 掃描速度 

Turn on light 房間照明調節

Low texture 專注房間中的有更多紋理的特定區域

透過 `Instruction` 的上述特性，可以在掃描時可以實時回饋指導操作者

### Process

![截圖 2024-09-23 13.58.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_13.58.07.png?raw=true)

此部分使用 `RoomBuilder` 來處理掃描到的數據，並生成最終的 3D 模型

![截圖 2024-09-23 14.01.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.01.27.png?raw=true)

先生成 `RoomBuilder` instance

![截圖 2024-09-23 14.03.48.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.03.48.png?raw=true)

再透過 `captureSession(_ session: didEndWith data: error:)` 最終處理

它會在呼叫 `session.stop()` 或者錯誤發生而停止時觸發此 delegate

最後再透過 `roomBuilder.captureRoom(from:)` 異步處理掃描數據，並建構最終的 3D 模型

### Export

![截圖 2024-09-23 14.09.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.09.03.png?raw=true)

![截圖 2024-09-23 14.14.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.14.49.png?raw=true)

![截圖 2024-09-23 14.19.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.19.19.png?raw=true)

![截圖 2024-09-23 14.20.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.20.01.png?raw=true)

![截圖 2024-09-23 14.21.09.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.21.09.png?raw=true)

以下這些都是在 `RoomPlan` 中支持的物品類型

![截圖 2024-09-23 14.22.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.22.06.png?raw=true)

最後再透過 `export(to:)` 輸出成 `USD` 和 `USDZ` 格式的檔案

Cinema 4D 的範例圖

![截圖 2024-09-23 14.24.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.24.47.png?raw=true)

![截圖 2024-09-23 14.25.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC22/Create%20parametric%203D%20room%20scans%20with%20RoomPlan/%25E6%2588%25AA%25E5%259C%2596_2024-09-23_14.25.17.png?raw=true)

## Best practices

Recommended conditions 優質掃描的理想環境

- Single residential rooms 適合單個房間
- Room size: 30 ft by 30 ft 適合的房間大小為 30 * 30 英尺 = 9 * 9 公尺 = 約 26 坪
- Lighting: minimum 50 lux 建議亮度要達到 50 勒克斯或更高，一般客廳在夜間的亮度通常就是 50 勒克斯，或者像是安全梯、車庫的亮度
- Lidar-enabled iPhone and iPad Pro models 硬體方面支持所有有 LiDAR 的裝置

Room selection 選擇房間時要注意的

- Challenging conditions 有些情境會有高難度的挑戰
    - Full-height mirrors or glass 例如全高鏡子和玻璃會讓 LiDAR 傳感器難以產生預期的輸出
    - High ceilings 較高的天花板也可能超出 LiDAR 傳感器的掃描範圍
    - Dark surfaces 難以掃描非常昏暗的表面

Scanning considerations 掃描方面的注意事項

- Preparing your room 對於精准度要求高的 App 可以事前準備
    - Opening the curtains 拉開窗簾，增加更多自然光
    - Closing doors 關門可以避免意外掃描到房間外的區域
- Instruction delegate 在掃描期間給操作者提供關於紋理、距離及速度的各種回饋
    - Texture
    - Distance
    - Speed
    - Lighting

Thermal considerations 散熱方面的注意事項

- Challenging conditions 注意設備的電池和散熱狀況，不要一次掃描超過5分鐘
    - Repeated scans
    - Long scans over 5 minutes

## Wrap-up

Intuitive scanning experience

Powerful data API for scene understanding

Fully parametric USD representations

其他討論

- [WWDC22 Qualities of a great AR experience](https://developer.apple.com/wwdc22/10131)
- [WWDC21 Dive into RealityKit 2](https://developer.apple.com/wwdc21/10074)

## 參考

- https://developer.apple.com/videos/play/wwdc2022/10127/

## Resources

- [Create a 3D model of an interior room by guiding the user through an AR experience](https://developer.apple.com/documentation/roomplan/create_a_3d_model_of_an_interior_room_by_guiding_the_user_through_an_ar_experience)
- [RoomPlan](https://developer.apple.com/documentation/RoomPlan)