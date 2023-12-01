# 使用 Fastlane Match，Azure Pipeline

# 前言

目前公司 iOS 專案的 CI/CD 的部分，是使用 Fastlane，但我們沒有雲端的機器可以 run macOS，只能靠地端，而且地端沒有額外的電腦機器可以提供，最後只能 run 在自己的電腦上。

為了要解決這 CI/CD run 在自己電腦上的問題，公司這陣子打算使用 Azure DevOps Pipelines，在使用過程中遇到一個很大的痛點，**Provisioning Profile**，目前專案是使用 Xcode automatically manage signing ，所以 Provisioning Profile 都會自動管理，只需要管理好 Certificate，而目前的配置在 Azure DevOps Pipelines 的 .yml 上，會需要類似這樣，會需要主動提供 Certificate 及 Profile。

```yaml
- task: InstallAppleCertificate@2
      inputs:
        certSecureFile: 'xxxCertification.p12'
        certPwd: $(p12pwd)
        keychain: 'temp'

- task: InstallAppleProvisioningProfile@1
      inputs:
        provisioningProfileLocation: 'secureFiles'
        provProfileSecureFile: 'xxx.mobileprovision'
```

從 .yml 看，我們會需要額外管理 provisioning profile ，這樣的維護成本很高，因為假如你有不同的環境，或者有不同的 target ，就會變得很複雜，而且可能還有 debug, beta, release 環境，最後就有可能有

```swift
// 主要 target
com.xxx.debug
com.xxx.beta
com.xxx.release

// 其他 target
com.www.debug.WidgetExtension
com.xxx.debug.NotificationServiceExtension
...
```

在 Azure Pipelines .yml 上

```yaml
- task: InstallAppleProvisioningProfile@1
      inputs:
        provisioningProfileLocation: 'secureFiles'
        provProfileSecureFile: 'com.xxx.debug.mobileprovision'

- task: InstallAppleProvisioningProfile@1
      inputs:
        provisioningProfileLocation: 'secureFiles'
        provProfileSecureFile: 'com.xxx.beta.mobileprovision'

- task: InstallAppleProvisioningProfile@1
      inputs:
        provisioningProfileLocation: 'secureFiles'
        provProfileSecureFile: 'com.xxx.release.mobileprovision'

- task: InstallAppleProvisioningProfile@1
      inputs:
        provisioningProfileLocation: 'secureFiles'
        provProfileSecureFile: 'com.www.debug.WidgetExtension.mobileprovision'
...
```

所以就可以知道處理一堆 provisioning profile 的話，管理維護成本極高，例如過期之後要更換，或有新的 device ，這些情況都要更新 profile，所以這時機很適合使用 Fastlane [Match](https://docs.fastlane.tools/actions/match/)。

---

# 設定

先準備好你儲存 certificate 及 provisioning profile 的地方，選項有：

1. git
2. google_cloud
3. s3
4. gitlab_secure_files

這邊我是使用 `git` ，記得在 git 帳號中配置好 SSH Key，以便後續身份驗證這段。

再來要在電腦安裝好 fastlane，並且在你的專案底下也 init 完成，然後我們就可以執行

```bash
fastlane match init
```

![2023-11-13 17.12.04.png](https://github.com/sweet4018/iOS-Learning-Notes/blob/main/Azure%20pipeline%20%2B%20Fastlane%20Match/images/%25E6%2588%25AA%25E5%259C%2596_2023-11-13_17.12.04.png)

步驟都完成後可以看到我們在專案下的 fastlane 檔案內多了一個 Matchfile 的檔案

![2023-11-13 17.16.36.png](https://github.com/sweet4018/iOS-Learning-Notes/blob/main/Azure%20pipeline%20%2B%20Fastlane%20Match/images/%25E6%2588%25AA%25E5%259C%2596_2023-11-13_17.16.36.png)

default 內容是這樣

![2023-11-13 17.18.25.png](https://github.com/sweet4018/iOS-Learning-Notes/blob/main/Azure%20pipeline%20%2B%20Fastlane%20Match/images/%25E6%2588%25AA%25E5%259C%2596_2023-11-13_17.18.25.png)

那我們要來調整一下內容

- `git_url` 在 run `fastlane match init` 過程中有輸入的 URL，後續也可以在這調整。
- `git_branch` 我們先指定 git 的主要分支就好， `master` or `main` ，假如有多個團隊的話，這時就可以把 branch 名稱設定為不同的團隊名稱。
- `app_identifier` 輸入我們專案的 BundleID ，也可以填多個，像是 App 包含的 Target Extension。
- `user_name` 這裡就輸入 Apple 開發者帳號，但在這建議使用 [Apple Store Connect API](https://developer.apple.com/app-store-connect/api/) ，不要用帳號的方式登入，因為有雙重驗證的麻煩，其他 fastlane 對 App Store Connect 相關的事情也建議都用 [Apple Store Connect API](https://developer.apple.com/app-store-connect/api/)。

設定 App Store Connect API 很容易，簡單幾個步驟，首先到 App Store [後台](https://appstoreconnect.apple.com/access/api)選擇**使用者與存取權限**，選擇**金鑰**，在點擊新增的按鈕，**這邊需要管理員權限的 Apple ID 帳號登入**，然後新增後下載 API 金鑰（一個 .p8 的檔案），保存到專案資料夾的 fastlane 目錄，**注意：金鑰只能下載一次，永遠不會過期，要妥善保管。**

![2023-11-13 17.55.19.png](https://github.com/sweet4018/iOS-Learning-Notes/blob/main/Azure%20pipeline%20%2B%20Fastlane%20Match/images/%25E6%2588%25AA%25E5%259C%2596_2023-11-13_17.55.19.png)

然後在我們的 Fastfile 可以使用 App Store Connect API Key 了，會類似這樣

```jsx
# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane

api_key = app_store_connect_api_key(
    key_id: "xxxxxxx",
    issuer_id: "xxxxxxx-xxxx-xxxx-xxxxxxxxxxx",
    key_filepath: "./xxx.p8", # 金鑰 .p8 檔案
    duration: 1200, # optional (maximum 1200)
    in_house: false # optional but may be required if using match/sigh
)

lane :release do
	......
  upload_to_app_store(
        api_key: api_key,
        force: true
        skip_screenshots: true,
        submit_for_review: true,
  )
end
```

最後 Matchfile 檔案可能就是這樣

```yaml
git_url("git@bitbucket.org:xxx/certificates.git")

storage_mode("git")

git_branch("master")

app_identifier(["com.xxx.debug", "com.xxx.debug.NotificationServiceExtension", "com.xxx", "com.xxx.NotificationServiceExtension"])

# 假如有分環境的話可以
type 'development'
app_identifier ["com.xxx.debug", "com.xxx.debug.NotificationServiceExtension"]

for_lane :release do
    type 'appstore'
    app_identifier ["com.xxx", "com.xxx.NotificationServiceExtension"]
end
# 或者可以不用在這設定，可以直接在 Fastfile 內，要執行 match 的地方設定好參數

ENV['MATCH_GIT_PRIVATE_KEY'] = 'fastlane/gitPrivateKeyPath' # 你的 git private key 路徑
ENV['MATCH_PASSWORD'] = 'xxx'
ENV['MATCH_KEYCHAIN_PASSWORD'] = 'xxx'
```

這時候就可以執行看看，**但注意是這樣會重新建立新的 certificate 和 profile** ，因為在 git 上還沒有任何 certificate 和 profile ，所以他會判斷需要重新建立。

```yaml
fastlane match development

fastlane match adhoc

fastlane match appstore
```

假如已經有憑證了，就可以先把檔案上傳到 git 上

1.準備 Certificate (.cer) 的檔案，可以到 [Apple Developer](https://developer.apple.com/account/resources/certificates/list) 網站上下載，或者從 Keychain 輸出

2.準備 Private key (.p12) ，一樣可以從 Keychain 輸出，過程中需要輸入密碼的部分，可以用空的密碼

![2023-11-21 11.43.11.png](https://github.com/sweet4018/iOS-Learning-Notes/blob/main/Azure%20pipeline%20%2B%20Fastlane%20Match/images/%25E6%2588%25AA%25E5%259C%2596_2023-11-21_11.43.11.png)

再來使用 Terminal(終端機)  cd 至專案目錄

```yaml
fastlane match import --readonly true --type development
#type 可以依照你要的環境，例如還有 appstore
```

過程中端機會依序跟你索取檔案，只要給檔案路徑即可

```yaml
Certificate (.cer) path:
# 輸入 .cer 檔案路徑

Private key (.p12) path:
# 輸入 .p12 檔案路徑

Provisioning profile (.mobileprovision or .provisionprofile) path
or leave empty to skip this file:
# 這邊可以直接跳過，由他自動產
```

另外一個方法是不透過 fastlane match 上傳，而是直接上傳至自己的 git ，但這樣過程還需自行加密

```yaml
openssl aes-256-cbc -k "<password>" -in "<fileYouWantToDecryptPath>" -out "<decryptedFilePath>" -a -e -md [md5|sha256]
```

所以建議直接使用 fastlane match 上傳比較省事。

另外假如需要清除現有的 Certificate 及 Profile 的話，有兩個方式

1. 直接到 git 上把對應的檔案刪除，添加下面的 code 到 Fastfile

```yaml
lane :match_development do
    match(type: 'development', readonly: fasle)
end
```

再來到專案目錄執行 `fastlane match_development`

1. 在專案目錄執行

```yaml
fastlane match nuke development
# 依據自己要的環境調整 development, distribution, enterprise
```

---

# 使用

在 Azure Pipeline .yml 上就會變得很單純，不用在管理憑證相關的事情

```yaml
# 原本需要寫這些
- task: InstallAppleCertificate@2
  displayName: 'Install Apple p12 cert'
  inputs:
    certSecureFile: 'xxx.p12'
    keychain: 'temp'
     
- task: InstallAppleProvisioningProfile@1
  displayName: 'Install Apple Mobile Provisioning Profile'
  inputs:
    provisioningProfileLocation: 'secureFiles'
    provProfileSecureFile: 'xxx.mobileprovision'

...
```

現在只需要

```yaml
# 確保能連線到儲存 certificates 及 profiles 的地方，例如 bitbucket
- task: InstallSSHKey@0
  inputs:
    knownHostsEntry: '$(knownHostsEntry)'
    sshPublicKey: '$(sshPublicKey)'
    sshKeySecureFile: 'sshkey'

# 這邊可以檢查是否有連線成功
- script: ssh -T git@bitbucket.org
  displayName: 'Testing the connectivity of the Bitbucket SSH key'

- script: gem install fastlane
  displayName: 'Install fastlane'

- task: CmdLine@2
  displayName: 'Deploy to Firebase Distribute or App Store'
  inputs:
    script: fastlane test

...
```

在 Fastfile 部分可能就這樣

```yaml
platform :ios do
  desc "Push a new test build to the Firebase"
  lane :test do

  setup_ci()

  match(
    api_key: api_key,
    type: "development",
    clone_branch_directly: true,
    readonly: true
  )

  build_app(
    workspace: "yourProject.xcworkspace",
    scheme: "yourProjectScheme",
    configuration: "Debug",
    clean: true,
    export_options: {
    method: "development",
    }
  )

  firebase_app_distribution(
    firebase_cli_token: ENV["FIREBASE_TOKEN"],
    app: "1:xxxxxxxxxxx:ios:xxxxxxxxxxxxxxx",
    groups: "ios-team, pm-team, qa-team",
    release_notes_file: "fastlane/release-notes-for-firebase.txt",
  )

  version = get_version_number(
    xcodeproj: "yourProject.xcodeproj",
    configuration: "Debug",
    target: "yourProjectTarget"
  )

  build_number = get_build_number(xcodeproj: "yourProject.xcodeproj")

  file_path = "release-notes-for-firebase.txt"
  content = File.read(file_path)

  slack(
    username: "Google Firebase",
    icon_url: "https://cdn-images-1.medium.com/max/1200/1*ti5CnGh_T4Kqy5aCTLJRcg.png",
    message: "Push a new test build to the Firebase",
    success: true,
    slack_url: "https://hooks.slack.com/services/xxxxxxxx",
    default_payloads: [],
    payload: {
      "Version" => "#{version} (#{build_number})",
      "Change log" => content,
    }
  )

  end
end
```

---

# 參考了

[上傳現有的 Certificate 和 Profile](https://sarunw.com/posts/how-to-manually-add-existing-certificates-to-fastlane-match/) 

[https://stackoverflow.com/questions/48576134/fastlane-match-with-multiple-apps](https://stackoverflow.com/questions/48576134/fastlane-match-with-multiple-apps)

[https://kaylla-wen.medium.com/使用-fastlane-管理開發憑證-8ffc6cc13cf8](https://kaylla-wen.medium.com/%E4%BD%BF%E7%94%A8-fastlane-%E7%AE%A1%E7%90%86%E9%96%8B%E7%99%BC%E6%86%91%E8%AD%89-8ffc6cc13cf8)

[https://juejin.cn/post/7121617118100979748](https://juejin.cn/post/7121617118100979748)

[https://hackmd.io/@hoclosdorcoor/fastlane](https://hackmd.io/@hoclosdorcoor/fastlane)

[https://pokeduck.dev/2021/01/21/iOS/fastlane-match](https://pokeduck.dev/2021/01/21/iOS/fastlane-match)

[https://citysite1025.medium.com/如何在-fastlane-中使用現有的-certification-進行自動化-f00e98b874f6](https://citysite1025.medium.com/%E5%A6%82%E4%BD%95%E5%9C%A8-fastlane-%E4%B8%AD%E4%BD%BF%E7%94%A8%E7%8F%BE%E6%9C%89%E7%9A%84-certification-%E9%80%B2%E8%A1%8C%E8%87%AA%E5%8B%95%E5%8C%96-f00e98b874f6)

[https://www.jianshu.com/p/deaf0f8d1f02](https://www.jianshu.com/p/deaf0f8d1f02)
