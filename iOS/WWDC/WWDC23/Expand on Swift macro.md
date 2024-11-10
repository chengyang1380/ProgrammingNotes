# Expand on Swift macro

![New Zealand — Deer Park Heights Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/IMG_8237.jpg?raw=true)

New Zealand — Deer Park Heights Queenstown

## Why macros?

最早能從一些 protocol 發現這個特性（Derived protocol conformance)，例如 `Codable` 

![Screenshot 2024-10-20 at 23.01.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/Screenshot_2024-10-20_at_23.01.05.png?raw=true)



這樣就可以避免寫重複樣版的程式碼

其實目前在 Swift 中已經有大量的例子，我們開發者只需要寫一些簡單的語法，編譯器就會自動生成一段複雜的程式碼

![Screenshot 2024-10-20 at 23.03.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/Screenshot_2024-10-20_at_23.03.35.png?raw=true)

但假設這時有一些特殊的功能需求無法被滿足，該怎麼辦？

這情境就是 Apple 推出 Swift macro 的用意，我們可以向 Swift 語言擴充自己的需求，而且無需修改編譯器

Extend Swift without changing the compiler

- Eliminate boilerplate
消除模板
- Make tedious things easy
讓繁瑣的事情變單純
- Share with other developers in packages
還可以在 packages 中向其他人分享

## Design philosophy

### Design goals

可能有些人使用過 Objective-C 或其他 C 相關語言，會知道 C 的 macro 有許多限制與坑，但 Swift 和他們有很大的不同，可以避免這些問題，所以 Apple 在設計時考慮了四個目標

- **Distinctive use sites**
- **Complete, type-checked, validated**
- **Inserted in predictable ways**
- **Macros are not magic**

- **Distinctive use sites**
當開發者在使用 macro 時，它應該非常容易找到，Macro 又分成兩種
    - **Freestanding macros 獨立**
        
        ![截圖 2024-10-22 16.26.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_16.26.29.png?raw=true)
        
        - Take the place of an expression or declaration 
        用於替代程式碼中的其他內容
        - Start with a `#` sign
        始終以井號 (`#`) 開頭
    - **Attached macros 附加**
        
        ![截圖 2024-10-22 16.32.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_16.32.52.png?raw=true)
        
        - Attached to another declaration
        在程式碼中聲明上添加屬性
        - Start with an `@` sign
        始終以 at (`@`) 符號開頭

原本 Swift 就可以使用 `#` 和 `@`  表示特殊的編譯器行為，現在 Macros 只是擴展它

- **Complete, type-checked, validated**
輸入輸出的程式碼都應該是完整的，並且需要進行錯誤檢查
    
    ![截圖 2024-10-22 16.41.21.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_16.41.21.png?raw=true)
    
    - 參數必須是完整的 expression
    - 不能傳遞錯誤的型別參數，參數和結果都會進行型別檢查，就像一般的函數一樣
    - Macros 的實現可以驗證輸入，並在出現問題時發出編譯器警告或錯誤

所以開發者可以更容易正確的使用 Macros

- **Inserted in predictable ways**
可預測、並且只能用新增的方式插入程式碼，這代表什麼？表示 Macro 只能增加程式中的可見程式碼，而不能刪除或修改程式碼。
所以我們看下面的圖片，即使我們不知道 `#someUnknownMacro()` 會做什麼，我們還是可以確定他不會刪除對 `finishDoingThingy()` 的呼叫，或將它移動到新函數中，這使得閱讀 Macro 的程式碼更加容易。
    
    ![截圖 2024-10-22 17.46.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_17.46.04.png?raw=true)
    

- **Macros are not magic
他**不該是看不穿的魔法，它只是在程式中增加了更多程式碼，而且我們都可以在 Xcode 中直接看到這過程，我們可以展開其內容，也可以設斷點，而且就算是第三方提供的，也是一樣。然後他也可以編寫單元測試。
    
    ![截圖 2024-10-22 17.50.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_17.50.52.png?raw=true)
    

## Translation model

首先我們先瞭解基本概念，從 Swift 的角度來看，當我們在程式碼中使用 macro 時，它會將該 macro 傳遞到一個這個 macro 實現的特殊編譯器插件，該插件是一個獨立的進程，會在一個安全的沙盒中運行，並且它包含由 macro 作者編寫的自定義 Swift 程式碼，它會處理 macro 的使用，最後返回一個展開 `“expansion”` 

![截圖 2024-10-22 18.15.44.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_18.15.44.png?raw=true)

該 macro 創建的新程式碼片段，這時就可以在開發者的程式中展開 `expansion` 了，並且一起編譯。

![截圖 2024-10-22 18.20.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-22_18.20.28.png?raw=true)

所以這就像開發者自己編寫了展開 `expansion` 而不是在呼叫 macro

那 Swift 是怎麼知道 `stringify` 這個 macro 的存在？

答案就是 Macro declarations

![截圖 2024-10-23 10.49.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_10.49.16.png?raw=true)

macro declarations 為 macro 提供 API，開發者可以直接在自己的 module 中編寫 declarations，也可以從其他 library 或 framework 中導入。

它就像一般的 function，需要名稱，可以用 generic，可以傳遞參數，返回結果。

`@freestanding(expression)` 它還可以一個或多個屬性用於指定 macro 的角色 roles（還有例如前面介紹過的 `attached` ），如果要寫一個 macro 就必須考慮它的角色 roles 是什麼

## Macro roles

### A macro role determines

macro roles 是一組規則

- Where it can be used
規定了應用位置和方式
- What types of code it expands into
展開成什麼樣的程式碼
- Where the expansions are inserted
展開插入到程式碼中的位置

最終就是實現我們的目標，可預測僅新增的方式插入展開（前面提到的 Inserted in predictable ways）

![截圖 2024-10-23 11.12.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_11.12.41.png?raw=true)

### `@freestanding`

- **expression 表達式：**

就是我們所說的，執行並產生結果的程式碼單元
例如下面這段程式碼，等號後面就是一個 expression，但是 expression 具有遞歸結構，通常由更小的 expression 組成，例如 `(x + width)` ，這單獨看也是一個 expression，在更小來看 `width` 這個詞也是 expression，因此 `freestanding expression macro` 就是一個可以展開 (`expansion` ) 為表達式 (`expression`) 的 macro。

![截圖 2024-10-23 11.16.42.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_11.16.42.png?raw=true)

這邊來看一個例子

![截圖 2024-10-23 11.41.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_11.41.35.png?raw=true)

我們通常都會使用 `guard` 的方式來拿 optional 的東西，確保安全，這種情境也可以設計一個 macro 來解決。

所以我們希望這個 macro 能夠計算並返回一個值，所以很適合使用 `freestanding(expression)` ，其中還使用 generic 當作型別。

![截圖 2024-10-23 11.46.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_11.46.35.png?raw=true)

最後使用上，就是這樣，並且看 `expansion` ，裡面有一點比叫特別是 `downloadedImage` 這個變數是可以放在錯誤訊息裡面讀取的，在普通的 function 無法實現這點。

![截圖 2024-10-23 11.49.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_11.49.49.png?raw=true)

- **declaration 聲明式**

它可以展開為一個或多個聲明，比如函數、變數或型別

使用情境是這樣，想像我們正在編寫某種需要二維數陣列的統計分析

![截圖 2024-10-23 14.09.42.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.09.42.png?raw=true)

但我們希望陣列中的所有行都具有相同數量的列，因此是不需要「陣列中的陣列」，相反是希望將元素儲存在一個扁平的一維陣列中，然後根據開發者傳入的二維索引計算出一個一維索引

![截圖 2024-10-23 14.09.31.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.09.31.png?raw=true)

最後我們可能會出這樣得的程式碼

`makeIndex` 這函數接受用於二維索引的兩個整數，然後進行一點而算數，將它們轉換為一維索引

![截圖 2024-10-23 14.08.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.08.24.png?raw=true)

但假設今天又多了需要三維的需求，就要開始調整裡面的內容

![截圖 2024-10-23 14.12.12.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.12.12.png?raw=true)

假如一直無限增加，例如多了四維五維，很快就會被幾乎相同的數組類型中搞昏，而且他們又不完全一樣，很難使用 generic 或 protocol exteniosn 或 subclass 或 Swift 語言的特性解決此問題。

![截圖 2024-10-23 14.13.18.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.13.18.png?raw=true)

但這些 struct 都是聲明的，所以此情境就適合使用 declaration macro 來創建它們

![截圖 2024-10-23 14.46.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.46.55.png?raw=true)

![截圖 2024-10-23 14.46.30.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_14.46.30.png?raw=true)

### `@attached`

顧名思義，它可以附加到特殊的聲明上，這表示他們有更多的訊息可以使用， `freestanding macro` 只能使用它們接收的參數，但 `attached macro` 可以訪問它們附加的聲明。

它們經常檢查該聲明並存中提取名稱、型別和其他訊息。

- **peer 對等**

它可以附加到任何聲明上，不僅僅是變數、函數和型別，甚至包括導入和運算符聲明，並且可以在它旁邊插入新的聲明，因此如果將這個 macro 附加到方法或屬性上，那麼它就可以生成該型別的成員。

如果將它附加到頂層的函數或型別上，它會生成新的頂層聲明，這些特性讓他變得非常靈活。

這邊說一個情境，假設我們在維護一個 framework，其中有一個 function 使用 `async` 的方式回傳結果，但我們還是需要針對一些用戶，使用 callback (completion handler) 的方式，所以要調整成下面那樣，假設有好多的 function 都是要改這樣的架構，這情境可以使用到 `attached(peer) macro` 

![截圖 2024-10-23 15.00.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.00.38.png?raw=true)

使用上就會變成這樣

![截圖 2024-10-23 15.03.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.03.57.png?raw=true)

![截圖 2024-10-23 15.04.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.04.56.png?raw=true)

- **accessor 訪問器**

它們看有附加到變數和下標上，並為其新增訪問器如 `get, set, willSet, didSet` 

使用情境大概是這樣，假設我們有一堆基本上包裝字典的型別，並且允許使用屬性來取得內容，例如下圖這樣，假設我們除了這三個字串（name, height, birth_date）之外還有其他訊息，它們很有可能會被忽略掉，這三個屬性需要計算 getter 和 setter，手動寫是很繁瑣的事，而且我們不能使用 `Property wrapper` ，因為無法取得它們所使用的型別上其他儲存屬性。

![截圖 2024-10-23 15.08.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.08.49.png?raw=true)

這時我們可以寫一個 `attached(accessor) macro` 來解決，其中一個參數是 `key` ，假設遇到上述的一些特別 key 欄位，例如 `birth_date` ，就可以傳遞，若傳 nil ，則會直接使用 property 的名稱作為 key。

![截圖 2024-10-23 15.14.32.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.14.32.png?raw=true)

![截圖 2024-10-23 15.16.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.16.05.png?raw=true)

![截圖 2024-10-23 15.16.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.16.24.png?raw=true)

雖然這樣改進很不錯，但這裡還存在一些樣板程式碼，那就是相同的 `DictionaryStorage` 屬性，要對每個屬性前面都加，這樣就要重複寫好幾次。 

這時可以透過一些內置屬性解決，讓整個型別或 Extension 來處理。

下個要介紹的 `attached(memberAttribute) macro` 也能這樣，附加到型別或 Extension 上，並且為附加的任何成員新增屬性。

- **memberAttribute 成員屬性**

這次比較特別，不是新增一個全新的 macro ，而是沿用上一個 `DictionaryStorage` 

![截圖 2024-10-23 15.22.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.22.35.png?raw=true)

這是一種非常有用的建立 macro 的技術

**Role composition**

- A macro may have multiple attached roles
- Swift will expand all roles applicable where the macro was used
- At least one role must be applicable

除了兩個 `freestanding macro roles`  外，其他的都可以組合任意 macro roles，因為 `freestanding` 的角色在某些情況下，Swift 無法確定使用哪個。

而 `attached macro roles` 不會， Swift 會將所有角色進行合適的展開 (expand 程式碼)，不論在哪裡應用它們，但其中要至少有一個角色適用於該位置。

所以 `DictionaryStorage` 當作例子

將它附加到一個型別上，Swift 會展開成員屬性 `memberAttribute` 的角色

如果把它附加到一個 property 上，它就會展開 `accessor` 的角色

但如果把它附加到 function 上，就會得到一個 compiler 的錯誤了！

像下圖這樣， `@DictionaryStorage` 可以附加整個型別，就不用單獨一個一個去附加到每個屬性，這個 macro 的邏輯會跳過某些成員，比如初始化， `dictionary` 屬性以及像 `birth_date` 這樣，已經有 `DictionaryStorage` 的屬性，所以就是對任何儲存屬性新增 `DictionaryStorage` 這個 attribute。

![截圖 2024-10-23 15.34.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.34.34.png?raw=true)

這樣的改進很棒，但我們還有很多樣板程式碼可以消除，那就是初始化和儲存資料的屬性，它們是由 `DictionaryRepressentable` 這個 protocol 要求來的，但它們在任何使用 DictionaryStorage 的型別中都是相同的，這樣就很適合靠 macro 去新增就好，就不用手動處理，這時就可以派下一個要被介紹的角色成員 `member` 了。

![截圖 2024-10-23 15.41.29.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.41.29.png?raw=true)

- member 成員

它也一樣可以應用型別和擴展 Extension，但它不是向現有成員新增屬性，而是新增全新的成員，因此可以新增方法、屬性、初始化等等。

也可以向 class 和 struct 新增儲存屬性或向 enum 新增 case。

回來我們的 DictionaryStorage macro，往裡面新增一個 member role，而且裡面還有一個名為 `dictionary` 的屬性，還有 `init(dictionary:)` 

![截圖 2024-10-23 15.53.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.53.59.png?raw=true)

### Macros can’t see each others’ expansions

我們可能會好奇，像這個 macro 裡有一堆 role，這些 role 使用相同的程式碼時，哪一個會被先展開 (expand 生成程式碼) ，Apple 這邊的答案是，**這並不重要**，每個 macro 都只能看到原始版本的 code，而不會看到其他 macro 提供的展開 (expand 生成程式碼) ，所以是不用擔心順序問題。

![截圖 2024-10-23 15.56.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_15.56.35.png?raw=true)

所以新增了 `member role` 後，已經不用手動在寫初始化和固定的屬性 `var dictionary:[ String: Any]` 了

![截圖 2024-10-23 16.03.53.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.03.53.png?raw=true)

但最後還有一小段的樣板程式碼需要消除，就是 `DictionaryRepresentable` 這 protocol 的一致性。

這情境要解決就是要靠下一個要介紹的角色 `conformance` 了。

- conformance 一制性

它可以為型別或者 Extension 新增一致性，所以現在我們在 `DictionaryStorage` 的 macro 加上最後的 role `conformance` 。

![截圖 2024-10-23 16.08.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.08.28.png?raw=true)

這樣就不用在額外遵守  `DictionaryRepresentable` 了

![截圖 2024-10-23 16.20.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.20.59.png?raw=true)

![截圖 2024-10-23 16.22.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.22.14.png?raw=true)

### 小結

原本將一個雜亂，充滿重複程式碼並難以重複使用的東西，透過強大的 macro 完成簡化

![截圖 2024-10-23 16.26.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.26.56.png?raw=true)

![截圖 2024-10-23 16.27.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.27.52.png?raw=true)

![截圖 2024-10-23 16.28.05.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.28.05.png?raw=true)

而且想像一下，假設現在有10個或20個可以使用 `DictionaryStorage` 的東西，現在處理起來變得超容易！

## Macro implementation

前面都在討論概念，現在要來實作了 💪

macro 的實現， 它位於等號之後，而且它可以是另一個 macro，可能只是參數重新排列一下

![截圖 2024-10-23 16.31.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.31.52.png?raw=true)

![截圖 2024-10-23 16.32.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.32.40.png?raw=true)

通常，我們會使用外部的 macro， 什麼是外部 macro？它是由 compiler plugin 實現的一種 macro，最一開始有提過

當 compiler 看到正在使用 macro 時，它會單獨在 process 中啟動一個 plugin 並要求它展開 (expand 生成程式碼) 該 macro，而 `#externalMacro` 就定義了這種狀態（讓他們連結起來）

![截圖 2024-10-23 16.34.38.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.34.38.png?raw=true)

所以 macro 的聲明與其他 API 一起位於普通的 library，但是實現位於單獨的 compiler plugin module。

![截圖 2024-10-23 16.37.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.37.20.png?raw=true)

現在我們在重新來看 `DictionaryStorage` 這個 macro，它具有 `attached member role` ，所以該型別會被新增一個儲存屬性和一個初始化。

接下來，來看一個簡單的 member macro 實作範例

![截圖 2024-10-23 16.44.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.44.56.png?raw=true)
首先是第一行的 `import SwiftSyntax` ，它是由 Swift project 維護一個 package，可以幫助解析、檢查、操作和生成 Swift source code，所以隨著 Swift 語言的發展，社群的貢獻者在不段更新 SwiftSyntax，因此它支持 Swift compiler 的所有功能。


SwiftSyntax 將 source code 表示為一種特殊的樹狀結構，例如下圖在這段程式碼中的 `struct Person` 會被表示為 `StructDeclSyntax` 的型別實例 instance，它還是具有屬性，而且每個 StructDeclSyntax 屬性都代表了，struct 聲明中的某部分。

例如像 property 列表位於 `attributes` 中

關鍵字 `struct` 位於 `structKeyword` 中

struct 的名稱位於 `identifier` 中

帶有大括號的主體和結構體成員位於 `memberBlock` 中

而像是 `modifiers` 是表示 struct 聲明所具有的屬性，但這個範例 `struct Person` 沒有，所以是 nil。

![截圖 2024-10-23 16.52.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_16.52.26.png?raw=true)

這些屬性中的有些語法節點，被稱為 `token` ，代表源文件中的一段特定文本，例如名稱、關鍵字或一些標點符號，並也包含該文本及其周圍的空格和註解等，細枝末節的其他內容，如果想要深入解析語法樹，可以找到一個覆蓋源文件每個字節的 token 節點，但一些節點，例如 `attributes` 屬性中的 `AttributeListSyntax` 節點和 `memberBlock` 屬性中的 `MemberDeclBlockSyntax` 節點並不是 token，它們在自己的屬性中有子節點，比方說，如果我們查看 `memberBlock` 屬性的內部，會發現一個左側大括號的 token，成員列表的 `MemberDeclListSyntax` 節點

![截圖 2024-10-23 17.28.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_17.28.41.png?raw=true)

SwiftSyntax 本身就是一個龐大的主題，所以在此不會討論太多細節，想要深入了解的話可參考

- [WWDC23 Write Swift Macros](https://www.notion.so/WWDC-23-106c733a4f1380f18078c85d5e791037?pvs=21)
- [SwiftSyntax](https://swiftpackageindex.com/swiftlang/swift-syntax/600.0.0-prerelease-2024-07-30/documentation/swiftsyntax)

![Screenshot 2024-11-10 at 15.48.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/Screenshot_2024-11-10_at_15.48.16.png?raw=true)

回到一開始的程式碼，第二行是 `SwiftSyntaxMacros` ，它提供了編寫 macro 所需的 protocol 和 types (型別) 。

第三行則是 `SwiftSyntaxBuilder` ，它提供了方便的 API，用於建構語法樹，以表示新生成的代碼，不使用它也是可以寫 macro，但它真的非常方便好用，Apple 強烈推薦多利用它。 

再來注意我們會遵守 `MemberMacro` protocol

![截圖 2024-10-23 18.20.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.20.26.png?raw=true)

 

### Macro role protocols

每個角色都有對應的 protocol，那像我們剛剛的例子 `DictionaryStorage` macro 具有四種角色，所以它就需要遵守4個相應的 protocol。但為了快速建立觀念學習，我們先只關注一個就好。

![截圖 2024-10-23 18.20.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.20.58.png?raw=true)

再來可以看到我們有一個 static function，這是 `MemberMacro` protocol 所需的，當使用 macro 時 Swift compiler 會呼叫它來擴展 (expand) 成員角色。

還有所有的展開 (expansion) 方法都是 static 的，所以 Swift 實際上並不創建 `DictionaryStorageMacro` 型別的 instance。

![截圖 2024-10-23 18.23.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.23.14.png?raw=true)

然後每個展開 (expansion) 方法都會返回 SwiftSyntax 節點，這些節點都會被插入到 source code 中，像是 `member macro` 會展開 (expand) 為一個聲明列表，作為成員新增到型別中，因此 member macro 的展開 (expansion) 方法返回一個 `DeclSyntax` 節點的陣列。

![截圖 2024-10-23 18.28.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.28.47.png?raw=true)

再來看到主體內部，會看到該陣列正在創建我們希望這個 macro 新增的初始化和儲存屬性，這裡的內容看起來像普通的字串，但實際上並不是，這些字串被寫入了 `DeclSyntax` 的預期位置，所以以 Swift 的角度來看，會當作它們是 soruce code 的片段，並要求 Swift 解析器將其轉換為 `DeclSyntax` 節點，這是 `SwiftSyntaxBuilder` library 提供的便利之一。

![截圖 2024-10-23 18.32.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.32.54.png?raw=true)

最後在遵守其他要用的 role 對應協議。

![截圖 2024-10-23 18.39.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.39.04.png?raw=true)

那假設我們不小心在使用時，用錯會怎麼樣？例如我們只能用在 struct 上，但不小心寫在 enum 上，會發生什麼？

![截圖 2024-10-23 18.40.59.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.40.59.png?raw=true)

那 `attached member` role 會嘗試新增一個儲存 `dictionary` 的屬性，這樣很明顯會錯誤，enum 無法寫一個儲存屬性，雖然這時會被 compiler 警告很好，但這個錯誤訊息其實有點讓人困惑，因為有可能使用的人並不清楚 `DictionaryStorage` macro 嘗試建立一個儲存屬性，那我們該怎麼辦？

![截圖 2024-10-23 18.42.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.42.01.png?raw=true)

前面有提到這也是 Swift 的目標之一，允許 macro 檢測其輸入中的錯誤，並發出自定義的錯誤，因此我們可以修改 macro 的實現來生成一個清楚明暸的錯誤訊息，讓我們的 `DictionaryStorage` macro 只能應用於 struct 中，這樣用的人就能更好了解錯誤。

要達成顯示明確的錯誤的關鍵點在這， `expansion` 方法的參數，對於不同的 role 具體的參數會略有不同，對於 `member macro` 而言，有三個參數

第一個 `attribute: AttributeSyntax` ，這是開發者為使用該 macro 而編寫的實際 `DictionaryStorage` 屬性

第二個 `declaration: DeclGroupSyntax` ，它是遵守了  `DeclGroupSyntax`  的型別，以下這幾個 struct, enum, class, actor, protocol, extension 的節點都要遵守它，因此這個參數為我們提供了開發者附加屬性的聲明。

第三個 `context: MacroExpansionContext` ，它是遵守 `MacroExpansionContext` 的一種型別，當 macro 實現需要與 compiler 交流時，就會使用了，它可以執行一些不同的操作，包括發出錯誤和警告

![截圖 2024-10-23 18.46.23.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-23_18.46.23.png?raw=true)

再來我們來看一個例子，如何運用這三個參數

首先我們可以用到 `declaration` 這個參數得知使用的地方是什麼，例如是 struct `StructDeclSyntax` 還是 enum `EnumDeclSyntax` 。

![截圖 2024-10-24 10.25.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.25.57.png?raw=true)

所以我們就可以寫一個 guard-else 來對 `declaration` 這個參數做判斷

![截圖 2024-10-24 10.28.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.28.16.png?raw=true)

這樣假如遇到不是 struct 的時候該怎麼辦？最直覺想到的一定就是直接使用普通的 Swift Error，但這樣做並不能對輸出結果進行很好的控制，所以有一個更好的做法，雖然比較複雜，但是輸出後的彈性非常高。

![截圖 2024-10-24 10.31.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.31.00.png?raw=true)

首先是要建立一個 `Diagnostic` 型別的 instance，可以想像它就像是骨科醫生透過 X 光片來診斷骨頭受傷一樣，轉到程式裡就是 compiler 或 macro 要查看錯誤程式碼的語法樹來診斷錯誤或警告。

![截圖 2024-10-24 10.33.47.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.33.47.png?raw=true)

仔細看上圖我們需要兩個參數建立 `Diagnostic` 

第一個是錯誤發生的語法節點，這樣 compiler 就知道要標記哪一行為錯誤，然後我們想要指向用戶寫的 `DictionaryStorage` 屬性，這東西剛好可以用方法的參數 `attribute` 。

第二個是希望 compiler 生成的訊息，可以透過創建自定義型別，然後傳遞來提供訊息。

接著我們來看怎麼寫一個 `DiagnosticMessage` 

可以把它想像成跟寫普通 Swift Error 一樣，可以用 enum，當然也是可以用其他型別

然後其中有一個 `severity: DiagnosticSeverity` ，它代表這個診斷是錯誤 (error) 還是警告 (warning)。

再來是 `message: String` 就是要傳遞出去的錯誤訊息

最後是 `diagnosticID: MessageID` ，其中的 domain 應該使用 plugin 的 module 作為名稱，並使用某種唯一值當作 ID，所以選擇用 enum 的話就可以很方便使用 rawValue 當作唯一值。

![截圖 2024-10-24 10.41.43.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.41.43.png?raw=true)

所以我們現在有一個客製化的 `Diagnostic` ，把它建立之後，就可以透過 `context` 呼叫 `.diagnose()` ，就完成了

![截圖 2024-10-24 10.49.14.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.49.14.png?raw=true)

![截圖 2024-10-24 10.49.30.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.49.30.png?raw=true)

也有更高級的做法，把 Fix-It 新增到 `Diagnostic` ，這樣 Xcode 的 Fix 按鈕就會自動調整。

### Building syntax trees

- SwiftSyntax initializers and methods

假如我們已經確定 macro 被正確使用，我們還是需要實際創建展開 (`expansion`)，所以 SwiftSyntax 為此提供了多種不同的工具，語法節點是不可變的，但他們有很多 API 可以創建新節點或返回現有節點的修飾版本。

- SwiftUI-style syntax builders

SwiftSyntaxBuilder 會新增 SwiftUI 風格的語法構建器，其中一些子節點由尾隨閉包指定，例如下圖的多維陣列 macro 可以使用語法構建器，為其創建的型別，生成適當數量的參數

![截圖 2024-10-24 10.57.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_10.57.08.png?raw=true)

- Literals with interpolations

我們創建的 `DictionaryStorage` 屬性和初始化的字串字面量功能也支持插值，這些功能在不同情況下都非常有用，所以我們有可能需要將幾個功能組合在一起，特別是處理複雜的 macro，但是字串字面量功能尤其適用於生成大量程式碼的語法樹，接下來簡單說明插值功能。

前面有說到 `unwrap` macro，它可以使用一個 optional value (`downloadedImage`)和一個訊息字串，並展開為包裝在閉包中的 `guard let` ，這類型的程式碼結構大多都是相同，但有些內容都是根據具體的使用位置訂製的，讓我們重點關注在 `guard let` ，看看要如何編寫一個函數來生成該語句。

![截圖 2024-10-24 11.02.52.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.02.52.png?raw=true)

首先把剛剛那句放在一個輔助方法中 (`makeGuardStmt`)，它會返回一個語句語法節點，然後我們可以新增插值，來替換所有需要根據其使用位置而不同的內容。 

![截圖 2024-10-24 11.08.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.08.54.png?raw=true)

首先新增正確的訊息字串，新增一個參數 `message: ExprSyntax` ，它是一個 `ExprSyntax` 節點，然後將其插入。這邊 message 不是用 String 而是用一個語法節點 (`ExprSyntax`)，是因為這邊不能插入 String，這是一個安全機制，防止不小心插入無效的程式碼。

![截圖 2024-10-24 11.10.56.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.10.56.png?raw=true)

接下來來看 guard let 那行的的變數名稱，它算是一個 token 而不是一個表達式 (expression)，所以我們要新增一個 `TokenSyntax` 參數並將其插入。

![截圖 2024-10-24 11.16.53.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.16.53.png?raw=true)

然後還有 else 後的變數名稱（也算是表達式 expression，它是一個純字串）在錯誤訊息中，這是一個棘手的狀況，Swift macro 的特點之一是當它失敗時，會打印出試圖 unwrap 的程式碼，這表示我們需要建立一個 String 字面量 (literals)，其中包含語法節點的 String 化版本。

首先將前綴從 Statement Syntax 字面量中提取出來，然後放在一個變數中 (messagePrefix)，然後再用插入該 String (messagePrefix 變數)，但這邊要注意，是使用 `literal:` 開頭的特殊插值，當執行此操作時，SwiftSyntax 會將字串的內容新增為 String 字面量 (literal)，這種方法也適用於從 macro 計算的其他型別訊息中建立 string literal，比如 Int, Boolean, Array, Dictionary 甚至 Optional。

![截圖 2024-10-24 11.21.36.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.21.36.png?raw=true)

我們現在在變量中建立 String，讓他在消息中使用正確的程式碼，只需要再新增一個參數，並將其 `description` 屬性插入到 String 中即可，我們不需要做任何特殊的轉語義操作， `literal:` 插值會自動檢測 String 中是否包含特殊字符並新增轉語義，或切換到原始字面量 (literal) 以確保程式碼有效，因此 `literal:` 插值讓我們能更輕鬆做出正確操作。

![截圖 2024-10-24 11.29.13.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.29.13.png?raw=true)

最後是文件和行數，這有點麻煩，因為 compiler 沒有告訴 macro 要展開 (expansion) 的原始位置，但 macro 展開 context 有一個可以用來生成特殊語法節點的 API，compiler 會將其轉換為帶有原始位置訊息的字面量 (literal)，那怎麼做呢？

我們新增另一個參數  `context: MacroExpansionContext` ，然後使用它的 `location(of:)` 方法，這方法會返回一個 Object，該 Object 可以為我們提供任何節點的位置，然後生成語法節點，如果該節點是 macro 創建的而不是 compiler 的話，傳遞回來的節點會是 nil，但我們知道 `origionalWrapped` 是用戶編寫的參數之一，因此它的位置永遠不會是 nil，我們可以安全地使用 `!` 來解開它 (變數 `origionalLoc`)。

![截圖 2024-10-24 11.37.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.37.03.png?raw=true)

所以現在可以將檔案和行數調整一下，就完成了。

![截圖 2024-10-24 11.51.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_11.51.01.png?raw=true)

## Writing correct macros

接下來，來聊聊怎麼讓他更好的運作，我們先來看看一些問題

- 名稱衝突

假設今天我們再用剛剛介紹的 `#unwrap` macro，其中變數名稱可能剛好重複，例如下圖這樣，在 message 參數中也使用了某個變數名稱，這名稱剛好與 macro 展開 (expansion) 的內容中有變數名稱衝突，這時 compiler 會尋找最近的那個，這樣就會導致錯誤，實際上我們要用的是另外一個

![截圖 2024-10-24 12.00.10.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_12.00.10.png?raw=true)

可能會想透過調整一個使用者不太可能用到的名稱來解決問題，但這不是根本解決問題

![截圖 2024-10-24 13.45.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_13.45.06.png?raw=true)

這邊就來介紹 macro expand 中 content 的 `makeUniqueName`  方法的作用，它會返回一個用戶程式中或任何其他 macro expand 中都絕對不會被使用的變數名稱，這樣就能根本解決問題。

![截圖 2024-10-24 13.46.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_13.46.06.png?raw=true)

### Making names visible

- Swift macros don’t prevent name conflicts

我們可能會想為什麼 Swift 不要自動處理這個問題就好了？某些語言具有所謂的 `hygienic` (衛生) macro 系統，也就是 macro 內部的名稱與外部的名稱是不相同的，這樣就不會發生名稱衝突了。

- Sometimes you want to access names from outside your macro

Swift 不這樣是有原因的，因為 Apple 發現有很多 macro 本身需要使用外部的名稱，像是 `DictionaryStorage` macro，它在型別上使用了一個 `dictionary` 的屬性，如果 macro 內部的 `dictionary` 與外部的 `dictionary` 意味著不同的東西，那麼 macro 就很難正常運作了

- Sometimes you even want to introduce new names
    - But you need to declare the names you’re adding

有時候我們甚至需要引用一個全新的名稱，以供非 macro 的程式碼訪問使用，對等(peer), 成員(member) 和聲明(declraration) macro 基本上都是為此而存在的，但他們存在時，他們需要聲明他們要新增的名稱，以便 compiler 知道他們的存在，並且他們要在 role 屬性內執行此操作。

例如以下這樣，像我們下方的這些宣告，都有一個 `name:` 參數，其中還有指向 `dictionary` 和 `init` 兩個名稱。 

![截圖 2024-10-24 13.57.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_13.57.50.png?raw=true)

### Name specifiers

我們可以使用五種名稱限制

- overloaded

表示 macro 會新增與他所附加的內容

- prefixed(<some prefix>)

表示 macro 新增具有相同基本名稱的聲明，但會新增指定的前綴

- suffixed

也是一樣，但他是新增後綴而不是前綴

- named(<some name>)

表示 macro 具有特定的、固定的基本名稱

- arbitrary

表示 macro 新增的聲明，有其他名稱，但這些名稱，無法使用這些規則來描述，使用 `arbitrary` 的情況是最普遍的，例如前面的多維陣列 macro 的例子，會宣告一種型別其他名稱根據其參數之一計算得出的。

選擇對的名稱，會讓 compiler 和 code 等工具的速度變得更快。

看到這，相信我們已經迫不及待想要寫一個 macro 了，先寫一個 expand 時插入日期和時間的 macro 就好，但請注意！我們不能寫這樣子的 macro，為什麼呢？

![截圖 2024-10-24 14.08.50.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_14.08.50.png?raw=true)

### Don’t use outside information

- Only use the information the compiler provides
    - Otherwise, tools won’t know when they need to re-expand a macro

macro 只能使用 compiler 提供給他們的訊息，compiler 假設  macro 的實現是純函數，並且如果他提供的數據沒有改變，那麼展開 (expand) 的結果也不能改變，如果繞過這個規則，可能會導致不一致的行為。

- Macros don’t have access to file systems or network

Macros 系統的設計在防止一些，可能違反此規則的行為，compiler plugin 會在沙盒中運行，沙盒可以阻止 macro 實現讀取磁盤上的文件或訪問網路。 

- Sandbox can’t stop you, but you still shouldn’t:
    - Insert API results like current time, process ID, or random numbers
    - Save information in global variables between expansions

但是沙盒並不能阻止所有不良行為，你可以使用 API 獲取日期或隨機數等訊息，或者將一個展開 (expansions) 中的訊息保存在全局變數中，並在另一個展開 (expansions) 中使用它，但是如果你這樣做，你的 macro 可能會表現異常，所以不要這樣做！ 

### Testing your macros

- Test your macro implementations to see if they expand as you expect
- Use standard tools like XCTest
- Highly recommended!

最後來談談測試，我們的 macro plugin 只是一個普通的 Swift Module，這表示我們可以正常地寫單元測試。

SwiftSyntaxMacrosTestSupport 中的 `assertMacroExpansion` 輔助，可以檢查 macro 是否被正確展開，只需要給他一個 macro 的範例以及他應該展開後的程式碼。

![截圖 2024-10-24 14.18.27.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC23/Expand%20on%20Swift%C2%A0macro/%25E6%2588%25AA%25E5%259C%2596_2024-10-24_14.18.27.png?raw=true)

## Wrap-up

- Macros let you create language features that “expand” into complicated code

macro 可以讓我們設計的新語言特性，然後就可以減少一些樣板程式碼

- Declared in a library, but implemented in a Swift program run in a sandbox

通常會用 library 與其他 API 一起生宣告 macro，但實際上是在一個單獨的 plugin 中實現 macro，該 plugin 會在一個安全的沙盒中執行 Swift 程式碼

- Roles express what a macro can do

macro 的 roles 會告訴我們，應在何處使用它，以及展開如何集成到程式其餘部分中

- Write macro tests with XCTest

而且我們應該為我們的 macro 編寫單元測試

## Resources

- https://developer.apple.com/videos/play/wwdc2023/10167