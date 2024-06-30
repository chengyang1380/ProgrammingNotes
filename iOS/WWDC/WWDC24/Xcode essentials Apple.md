# Xcode essentials | Apple

主要是看了 WWDC24 Xcode essentials | Apple 的筆記，重新認識了 Xcode，發現許多方便的功能。

先附上影片連結：

- [https://youtu.be/EN7-6Oj7cL0?si=UH9_xbO9D3df2B-0](https://youtu.be/EN7-6Oj7cL0?si=UH9_xbO9D3df2B-0)
- [https://developer.apple.com/wwdc24/10181](https://developer.apple.com/wwdc24/10181)

## 前情提要

寫程式的週期像是 Edit → Debug → Test → Commit ，在新專案，要在 code 裡面找東西來修改很容易，因為檔案不多。

![截圖 2024-06-14 14.33.37.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.33.37.png?raw=true)

但是隨著專案和團隊成長，開始要找到正確的程式碼就變成了問題，程式碼越來越龐大。

新增功能或修復錯誤需要更多的時間，因為要找到正確的 Code 調整。

![截圖 2024-06-14 14.35.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.35.59.png?raw=true)

以下內容若有用 ✅ 符號開頭，表示個人覺得很實用不錯的技巧

## Edit

![截圖 2024-06-14 14.41.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.41.07.png?raw=true)

### Find the right content

使用左邊側邊欄的搜尋

![截圖 2024-06-14 14.45.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.45.04.png?raw=true)

請善用 Filter

![截圖 2024-06-14 14.45.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.45.47.png?raw=true)

✅ 並且還有篩選功能，可以篩選出正在變更的程式碼（有 git 狀態的檔案）

![截圖 2024-06-14 14.46.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.46.19.png?raw=true)

假如忘記了檔案名稱，要很模糊的搜尋，這時推薦用 Find Navigator，有時候我們搜尋的結果太多，可以用底下的 Filter 再一次篩選，也可以在 Filter 上寫出檔案名稱

![截圖 2024-06-14 14.58.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_14.58.29.png?raw=true)

✅ 或者有時候真的搜尋結果太多了，可以按下 `Command` 加上，檔案左上角的箭頭

![截圖 2024-06-14 15.00.25.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.00.25.png?raw=true)

它就可折疊起來

![截圖 2024-06-14 15.00.45.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.00.45.png?raw=true)

假如確認過後，發現前三筆的結果不需要用到，也可以選起，按刪除鍵，讓搜尋結果內容更單純，這只會對當前搜尋結果移除，檔案依然存在。

![截圖 2024-06-14 15.02.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.02.05.png?raw=true)

可以在搜尋欄下方的 In Project，來選定搜尋目標，更精準找檔案

![截圖 2024-06-14 15.04.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.04.02.png?raw=true)

![截圖 2024-06-14 15.04.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.04.18.png?raw=true)

然後也可以選 Custom Scopes…，這樣可以選擇不同的搜尋範圍。

![截圖 2024-06-14 15.06.30.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.06.30.png?raw=true)

而且如果你發現經常使用相同的搜尋範圍，還可以儲存他，下次就可以快速使用。

另外在搜尋欄上的 Find > Text > Containing。

![截圖 2024-06-14 15.08.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.08.01.png?raw=true)

可以選擇用 Descendent Types，對於取得類別層次結構的概述非常有幫助。

![截圖 2024-06-14 15.09.15.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.09.15.png?raw=true)

✅ 還有時候我們會複製一些文字並貼上搜尋，但這時原本更先前複製的訊息就會丟失了，而現在有一個解決方案，直接在程式碼內把想搜尋的地方，選起後按下 `Command + E`，就會將選起的地方，貼到搜尋欄上，這時就不會使用到剪貼簿，原本複製到剪貼簿的內容就不會被影響。

### Move between files

首先是 Xcode 裡的 tab，仔細看有兩種，一種是正常的（會常駐在那，除非主動關閉它），一種是斜線（隱式的，只要你切到其他檔案他就會隱藏了）

![截圖 2024-06-14 15.19.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_15.19.41.png?raw=true)

右鍵 `Keep Open` 也可以讓隱式的改成常駐

![截圖 2024-06-14 17.40.10.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.40.10.png?raw=true)

而在旁邊還有後退和前進的按鈕，可以用快捷鍵 `Control + Command + 左 or 右` 來快速切換

![截圖 2024-06-14 17.47.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.47.58.png?raw=true)

按住後還有完整的歷史紀錄

![截圖 2024-06-14 17.49.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.49.04.png?raw=true)

在後退和前進按鈕的左邊，還有相關選單

![截圖 2024-06-14 17.51.37.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.51.37.png?raw=true)

可以查看最近使用的檔案

![截圖 2024-06-14 17.51.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.51.56.png?raw=true)

最右邊還有配置編輯器 UI

![截圖 2024-06-14 17.52.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.52.46.png?raw=true)

中間的按鈕是控制編輯器的佈局

![截圖 2024-06-14 17.53.25.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.53.25.png?raw=true)

再來可以利用 Tab 下面的那欄檔案路徑，來快速切換檔案，而且還有搜尋的功能（打開後直接輸入文字）即可搜尋

![截圖 2024-06-14 17.55.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_17.55.01.png?raw=true)

✅ 使用 `Command + Shift + J` ，很適合要在目前的文件附近建立新文件時，因為他會在左邊側邊欄直接選起當前的檔案

![截圖 2024-06-14 18.02.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_18.02.47.png?raw=true)

可以透過按著 `Option + 拖曳` 來快速複製檔案

![截圖 2024-06-14 18.05.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-14_18.05.21.png?raw=true)

可以自行用 `#warning` 並附上一條訊息

![截圖 2024-06-17 10.55.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_10.55.17.png?raw=true)

善用 `Bookmark` ，可以快速切換你需要的檔案，當作一個 TODO List

![截圖 2024-06-17 10.56.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_10.56.59.png?raw=true)

![截圖 2024-06-17 10.58.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_10.58.19.png?raw=true)

### Leverage shortcuts

✅ `Shift + Command + O` 快速打開檔案

![截圖 2024-06-17 11.11.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_11.11.16.png?raw=true)

假如在快速打開檔案的搜尋中加 `/` ，變成文件路徑，而且還可以加 `:` ，代表行數。

![截圖 2024-06-17 11.16.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_11.16.01.png?raw=true)

假如主要的目的是要對比檔案，可以在這時按著 `Option` 鍵在點擊開檔案，會變成比較檔案的畫面

![截圖 2024-06-17 11.17.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_11.17.07.png?raw=true)

![截圖 2024-06-17 11.17.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_11.17.55.png?raw=true)

✅ 打開快速操作，可以透過 `Command + Shift + A` ，可以直接使用自然語言下關鍵字，搜尋 Xcode 的指令

![截圖 2024-06-17 14.14.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.14.46.png?raw=true)

✅ 快速重新命名，`Command + Control + E` ， ⚠️ 但是只能在同個檔案裡

![截圖 2024-06-17 14.26.33.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.26.33.png?raw=true)

可以對一個 variable or function 快速找到呼叫他的人

![截圖 2024-06-17 14.29.11.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.29.11.png?raw=true)

✅ 快速排版參數，讓他們變成多行，`Control + M` ，但注意要把游標移到 `(` 的前一格或者之後使用才有效，並要在 `)` 之內

![截圖 2024-06-17 14.31.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.31.06.png?raw=true)

![截圖 2024-06-17 14.32.46.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.32.46.png?raw=true)

✅ 快速移動游標，透過按著 `Command` 或者 `Option` 加上 `左右箭頭按鍵` 

`Command` 是大範圍

`Option` 是小範圍

多個游標，按著 `Control + Shift` ，然後再點擊要放游標的地方，就會有多個

![截圖 2024-06-17 14.39.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.39.12.png?raw=true)

習慣使用 vim 的話，有一個 Vim Mode

![截圖 2024-06-17 14.48.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.48.31.png?raw=true)

可以透過 `<#...#>` 來寫一個 placeholder

![截圖 2024-06-17 14.51.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_14.51.19.png?raw=true)

### Get the most out of git

想快速查看上次是誰改的，對那行 Code 右鍵，Show Last Change for Line

![截圖 2024-06-17 15.13.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.13.06.png?raw=true)

![截圖 2024-06-17 15.13.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.13.44.png?raw=true)

### Edit 小總結

![截圖 2024-06-17 15.16.42.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.16.42.png?raw=true)

## Debug

### Setting Breakpoints

✅ 面對這種 throw error 有時不好找到哪拋出 error

![截圖 2024-06-17 15.22.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.22.06.png?raw=true)

這時可以在左邊側邊欄，Breakpoints 的 tab 裡，點擊左下角的 `+` ，點選 `Swift Error Breakpoint` 

![截圖 2024-06-17 15.20.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.20.22.png?raw=true)

設定後就會自動斷在有 Error 拋出時斷下來

![截圖 2024-06-17 15.22.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.22.51.png?raw=true)

✅ 對 breakpoint 設定 Condition

![截圖 2024-06-17 15.25.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.25.18.png?raw=true)

✅ 有時候我們設定斷點，但不想要中斷執行，從中想記一些 Log，這時可以善用 Action 做 Log 紀錄，並且把 `Automatically continue after evaluating actions` 打開

![截圖 2024-06-17 15.26.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.26.34.png?raw=true)

✅ 往前拖移 green program counter (斷點時的綠色 bar），但也有副作用，會很容易出現奇怪的狀態或者直接斷掉不運行

![截圖 2024-06-17 15.37.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.37.59.png?raw=true)

![截圖 2024-06-17 15.38.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.38.17.png?raw=true)

✅ 假如沒有調整過程式碼，建議直接用 `Command + Control + R` ，Run Without Building 節省時間，或者你已經調整過了程式碼，但是想要在嘗試一次舊的程式碼，也可以這樣用

![截圖 2024-06-17 15.42.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.42.07.png?raw=true)

✅ Xcode 16 提供的新功能，可以快速彙整這個斷點是怎麼走來的

![截圖 2024-06-17 15.44.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.44.22.png?raw=true)

在下面設定斷點的地方可以查看此模式

![截圖 2024-06-17 15.50.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.50.55.png?raw=true)

### Using the Console

通常我們在 Debug，會使用 `print` 並搭配 Macros

![截圖 2024-06-17 15.51.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_15.51.55.png?raw=true)

✅  但比起 `print` ，這邊更推薦使用 `os_log` （需要先 `import OSLog` ）

再透過最底下的按鈕設定，可以出現更多細節

![截圖 2024-06-17 16.23.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.23.59.png?raw=true)

而且還可以直接快速跳到該檔案的位置

![截圖 2024-06-17 16.25.19.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.25.19.png?raw=true)

還可以再加按空白鍵，顯示更多訊息

![截圖 2024-06-17 16.30.45.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.30.45.png?raw=true)

### 更多有關 Debug 的事情可以看

![截圖 2024-06-17 16.46.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.46.28.png)

- **WWDC24: Run, Break, Inspect: Explore effective debugging in LLDB | Apple**
    
    [https://developer.apple.com/wwdc24/10198](https://developer.apple.com/wwdc24/10198)
    
    https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://www.youtube.com/watch?v=PsW3RQN9R_Q&ved=2ahUKEwiNiqvkneKGAxXVi68BHTWtBv0QtwJ6BAgREAI&usg=AOvVaw3N5Vzu6LoEwoS4seTpL3oI
    

或者

- **WWDC23: Debug with structured logging | Apple**
    
    [https://developer.apple.com/wwdc23/10226](https://developer.apple.com/wwdc23/10226)
    
    [https://youtu.be/VDMjaCrqmJE?si=Mi4j6Wnh-pz91VUP](https://youtu.be/VDMjaCrqmJE?si=Mi4j6Wnh-pz91VUP)
    

或者

- WWDC22: Debug Swift debugging with LLDB
    
    [https://developer.apple.com/wwdc22/110370](https://developer.apple.com/wwdc22/110370)
    

## Test

### Test Navigator

善用左邊側邊欄的 Filter 工具，例如只找出沒跑過的測試

![截圖 2024-06-17 16.56.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.56.29.png?raw=true)

假如你正在寫某個測試，它失敗了，可以透過 `Command + Control + Option + G` 快速重新再跑一次該 test case

![截圖 2024-06-17 16.54.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.54.12.png?raw=true)

或者可以使用 `Test Without Building` ( `Command + Control + U` )

![截圖 2024-06-17 16.58.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_16.58.38.png?raw=true)

測試的各種狀態，[官方文件](https://developer.apple.com/documentation/xcode/running-tests-and-interpreting-results)

![截圖 2024-06-17 17.00.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_17.00.20.png?raw=true)

![截圖 2024-06-17 17.04.51.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_17.04.51.png?raw=true)

### Running tests

✅ 假如有些測試不太穩定，可能是 race condition 或一些非確定性行為，這時可以使用 `Run 'xxx()' Repeatedly...` 

![截圖 2024-06-17 17.06.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_17.06.24.png?raw=true)

![截圖 2024-06-17 17.20.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_17.20.21.png?raw=true)

Xcode Cloud，目前有免費額度 25 hours/月

### Test Plans

![截圖 2024-06-17 18.17.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_18.17.28.png?raw=true)

從 Product → Test Plan 裡可以新增編輯

![截圖 2024-06-17 18.20.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-17_18.20.55.png?raw=true)

Test Plan 是什麼？

- 彈性的測試，可以選擇不同的配置下執行某些測試，而不避每次都手動更改設置
- 測試多個本地化

### Code Coverage

![截圖 2024-06-18 13.56.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_13.56.22.png?raw=true)

### Test Report

![截圖 2024-06-18 13.57.15.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_13.57.15.png?raw=true)

## Distribute

---

### TestFlight

付費的開者帳號可以分發給 10,000 名 Beta 測試人員，可以透過 Email 或發布連結邀請他們

### Archiving

![截圖 2024-06-18 14.04.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_14.04.20.png?raw=true)

TestFlight Internal Only: 會跳過 App 送審機制，只能提供給提供者或組織中的 Beta 測試人員，不能給外部測試人員使用

這邊再次推薦使用 Xcode Cloud，他會與 TestFlight 緊密連結，自動部署版本，以便測試人員獲得最新版

### Organizer

使用快捷鍵 `Command + Option + Shift + O` 開啟

![截圖 2024-06-18 14.07.39.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_14.07.39.png?raw=true)

可以在左邊側邊欄找到 `Feedback` ，查看使用 Beta 版的人員的回饋

![截圖 2024-06-18 14.09.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_14.09.14.png?raw=true)

也可以看 App 啟動速度與狀況

![截圖 2024-06-18 14.10.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Xcode%20essentials%20%7C%20Apple/%25E6%2588%25AA%25E5%259C%2596_2024-06-18_14.10.08.png?raw=true)

推薦專案經理或產品行銷合作夥伴，都可以多善用這些工具，來幫助推動產品方向