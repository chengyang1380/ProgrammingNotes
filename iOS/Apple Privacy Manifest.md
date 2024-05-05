# Apple Privacy Manifest


![Lake Matheson](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/IMG_0687.jpg?raw=true)

Lake Matheson

## TLDR

Apple 規定從 2024/05/01 開始，要上傳至 App Store 的話，都要提交 Privacy Manifest，[詳細情況（官網）](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/adding_a_privacy_manifest_to_your_app_or_third-party_sdk)。

## 什麼是 Privacy Manifest?

可以參考 WWDC 2023 [Get started with privacy manifests](https://developer.apple.com/videos/play/wwdc2023/10060) 介紹。

這個隱私清單 (Privacy Manifests) 主要就是讓開發人員對使用者數據如何搜集及應用的說明，並且**包括第三方套件**，但目前不是所有的第三方套件需要提供，只有規定內的，詳情請見[官方文件](https://developer.apple.com/support/third-party-SDK-requirements/)，很多知名的都有，例如：Alamofire, Firebase, RxSwift…。

那除了我們主要的 Target 需要，記得其他 Extension 也需要，常見的就是我們的 Notification, Widget 之類的，都需要新增對應的 Privacy Manifests。

Privacy Manifests 它是一個 `.xcprivacy` 的檔案，但內容跟我們常見的 `.plist` 一樣，都是 XML、Property list，只要在 Xcode 15 以上的版本，就有預設模板可以新增，記得**不要修改檔案的名稱**。

![截圖 2024-05-02 17.22.20.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-02_17.22.20.png?raw=true)

新增後只有看到 App Privacy Configuration

![截圖 2024-05-02 18.03.00.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-02_18.03.00.png?raw=true)

只要在 App Privacy Configuration 中按新增

![截圖 2024-05-02 18.03.11.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-02_18.03.11.png?raw=true)

可以發現總共有四大項

## Privacy Manifest 如何填寫？

主要就是要填寫上述的四大項，我們從最簡單的開始看

- `NSPrivacyTracking` **Privacy Tracking Enabled**:
填入 Boolean 即可，指我們的 App 或第三方 SDK 是否[追蹤用戶](https://developer.apple.com/app-store/user-privacy-and-data-use/)。
    
![截圖 2024-05-03 15.35.58.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_15.35.58.png?raw=true)
    

---

- `NSPrivacyTrackingDomains` **Privacy Tracking Domains**:
填入 Array<String>，列出 App 或第三方 SDK 參與追蹤的網域。

    如果使用者在 [App 追蹤透明度(ATT, AppTrackingTransparency)](https://developer.apple.com/documentation/apptrackingtransparency) 不接受追蹤，則對這些網域的網路請求會失敗，並且 App 會收到錯誤。

    如果 `NSPrivacyTracking` (**Privacy Tracking Enabled**) 設定 YES(true) ，就必須設定一個 domain，設定 NO(false) 則可以為0個或多個。

    可以透過 Xcode 的 Instruments 功能檢查狀況，詳情[官方文件](https://developer.apple.com/documentation/xcode/detecting-when-your-app-contacts-domains-that-may-be-profiling-users)，但經過實測目前（Xcode 15.2）這個檢查方法有問題，在 [Apple Develop Forums](https://forums.developer.apple.com/forums/thread/744659) 有討論。
    
![截圖 2024-05-03 11.25.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_11.25.04.png?raw=true)

最後填寫會長類似這樣

![截圖 2024-05-03 11.49.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_11.49.04.png?raw=true)

假如有使用到 Firebase 相關服務可參考:
- [https://github.com/firebase/firebase-ios-sdk/issues/11490](https://github.com/firebase/firebase-ios-sdk/issues/11490)
- [https://firebase.google.com/docs/ios/app-store-data-collection](https://firebase.google.com/docs/ios/app-store-data-collection?hl=zh-cn)

---

- `NSPrivacyCollectedDataTypes` **Privacy Nutrition Label Types**:
填入 Array<Dictionary> ，主要就是描述 App 和第三方 SDK 收集的數據類型（例如：姓名、電子郵件、電話號碼）的詳細資訊，我們可以在 **Privacy Nutrition Label Types** 項目按下新增按鈕，預設如下。
    
    ![截圖 2024-05-03 14.04.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_14.04.07.png?raw=true)
    
    這部分可以直接參考 App Store Connect 的 App Privacy 來設定。
    
    ![截圖 2024-05-03 14.14.49.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_14.14.49.png?raw=true)
    
    例如，我們看「聯絡資訊：姓名」這項，就像下方這樣填
    
    - Collected Data Type: 姓名 → Name
    - Lined to User: 會與使用者身份連結 → YES
    - Used for Tracking: 因為沒有 → NO
    - Collection Purposes: 用於 App 功能 → App Functionality
    
    ![截圖 2024-05-03 16.18.26.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_16.18.26.png?raw=true)
    
    接下來就詳細的介紹各項
    
    - `NSPrivacyCollectedDataType` **Collected Data Type:**
    填入 String，敘述 App 或第三方 SDK 收集的資料類型，要填寫的內容請參考 **[Report the categories of data your app or third-party SDK collects](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_data_use_in_privacy_manifests#4250555)** 清單選擇。
    
    ![截圖 2024-05-03 16.27.40.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_16.27.40.png?raw=true)
    
    ![截圖 2024-05-05 下午5.41.55.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-05_%25E4%25B8%258B%25E5%258D%25885.41.55.png?raw=true)
    
    - `NSPrivacyCollectedDataTypeLinked` **Linked to User:**
    填入 Boolean，App 或第三方 SDK 是否將此資料類型連結到使用者的身份。 如何定義？詳情[可以看這](https://developer.apple.com/app-store/app-privacy-details/#linked-data)。

    - `NSPrivacyCollectedDataTypeTracking` **Used for Tracking:**
    填入 Boolean，App 或第三方 SDK 是否使用此數據類型進行追蹤。

    - `NSPrivacyCollectedDataTypePurposes` **Collection Purposes:**
    填入 Array<String>，App 或第三方 SDK 收集資料的原因。 要填寫的內容請參考 [Report the reasons your app or third-party SDK collects data](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_data_use_in_privacy_manifests#4250556) 清單。
        
    ![截圖 2024-05-03 14.34.07.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_14.04.07.png)
        
    ![截圖 2024-05-03 14.26.42.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_14.26.42.png)
        

---

- `NSPrivacyAccessedAPITypes` **Privacy Accessed API Types**:
填入 Array<Dictionary>，其 Dictionary 的內容格式為 `NSPrivacyAccessedAPIType: NSPrivacyAccessedAPITypeReasons` ，要敘述App 或第三方 SDK 使用隱私 API 原因，例如大家幾乎都有用的 **[User defaults APIs](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api#4278401)**，詳情[官方文件](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api)。

    - `NSPrivacyAccessedAPIType` **Privacy Accessed API Type:**
    填入 String，使用到的 API 項目選填，詳情請參考[官方文件](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api)。
        
        ![截圖 2024-05-03 15.09.39.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_15.09.39.png)
        
    - `NSPrivacyAccessedAPITypeReasons` **Privacy Accessed API Reasons:**
    填入 Array<String>，使用的原因，依照選單上的選擇就好，詳情請參考[官方文件](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files/describing_use_of_required_reason_api)。
        
        ![截圖 2024-05-03 15.02.49.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_15.02.49.png)
        

> 關於這個 **Privacy Accessed API Type**，還可以使用腳本快速掃瞄，請參考 **[Scanners for possible use of "iOS required reason API"](https://github.com/Wooder/ios_17_required_reason_api_scanner?utm_source=substack&utm_medium=email)。**
> 

## 第三方 SDK

---

假如使用的第三方 SDK 沒有在 Apple 列出的[名單](https://developer.apple.com/support/third-party-SDK-requirements/)，則不需處理。

若有的話，就可以查看該 SDK 是否有提供 privacy manifest，有的話就升級上去，但這時最怕就是有一些 SDK 太久沒更新，結果更新，原本的 code 要改動很多，所以假設不打算更新 SDK 也可以：

- 將對應的 SDK 下載最新版下來
- 自己新增 manifest （參考最新 SDK 中 manifest 文件）
- push 到自己的 github 上
- 透過 cocoapods 引用新增的 manifest 文件的 SDK
- 更新 .podspec 文件
    - `s.resource_bundles = {'fmdb' => ['src/privacy/PrivacyInfo.xcprivacy']}`
- 在 project 使用 cocoapods 導入
    - `pod 'FMDB', :git => "git地址", :branch => "master”`

## 最後

全部填寫完後，可以 Archive

![截圖 2024-05-03 17.13.10.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_17.13.10.png)

右鍵，Generate Privacy Report

![截圖 2024-05-03 17.14.05.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_17.14.05.png)

最後就可以看到完整報告，包含第三方 SDK，如下圖，其中專案有用到 Facebook SDK，可以看到它有使用 Device ID。

![截圖 2024-05-03 17.27.15.png](https://raw.githubusercontent.com/chengyang1380/ProgrammingNotes/main/Images/Apple%20Privacy%20Manifest/%25E6%2588%25AA%25E5%259C%2596_2024-05-03_17.27.15.png)

## 參考

- [https://www.jianshu.com/p/9a9ec34395e2](https://www.jianshu.com/p/9a9ec34395e2)
- [https://developer.apple.com/documentation/bundleresources/privacy_manifest_files](https://developer.apple.com/documentation/bundleresources/privacy_manifest_files)
- [https://blog.csdn.net/lyh1083908486/article/details/137250581](https://blog.csdn.net/lyh1083908486/article/details/137250581)
- [https://juejin.cn/post/7329732000087425064](https://juejin.cn/post/7329732000087425064)
- [https://medium.com/@langlive_tech/preparing-for-ios-17-privacy-manifests-sdk-signatures-and-api-required-reasons-82f81741fbb2](https://medium.com/@langlive_tech/preparing-for-ios-17-privacy-manifests-sdk-signatures-and-api-required-reasons-82f81741fbb2)
- [https://github.com/Wooder/ios_17_required_reason_api_scanner?utm_source=substack&utm_medium=email](https://github.com/Wooder/ios_17_required_reason_api_scanner?utm_source=substack&utm_medium=email)
- [https://zhgchg.li/posts/9a05f632eba0/](https://zhgchg.li/posts/9a05f632eba0/)