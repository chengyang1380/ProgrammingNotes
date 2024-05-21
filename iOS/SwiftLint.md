# SwiftLint


![IMG_8289副本.jpeg](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/IMG_8289%25E5%2589%25AF%25E6%259C%25AC.jpeg?raw=true)

[SwiftLint](https://github.com/realm/SwiftLint) 簡單來說就是檢查程式碼是否有符合規範，那規範的部分是基於 [Kodeco’s Swift](https://github.com/kodecocodes/swift-style-guide)

在 Xcode 上 SwiftLint 會幫忙檢查 error 或者 warning，例如下圖這樣

![截圖 2024-04-22 14.34.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-22_14.34.17.png?raw=true)

# 安裝


我這邊用了

**使用 [Homebrew](http://brew.sh/)：**

```bash
brew install swiftlint
```

**使用 [CocoaPods](https://cocoapods.org/)：**

將下方程式碼加到你的 Podfile 即可：

```bash
pod 'SwiftLint', '0.54.0' # 建議安裝某個特定的版本
```

下次執行 `pod install` 時將會把 SwiftLint 的二進位檔案和依賴下載到 `Pods/` 目錄下並且將允許你透過 `${PODS_ROOT}/SwiftLint/swiftlint` 在 Script Build Phases 中呼叫 SwiftLint。

自從 SwiftLint 支援安裝某個特定版本後，安裝指定版本的 SwiftLint 是目前建議的做法相比較於簡單地選擇最新版本安裝的話（例如透過 Homebrew 安裝的話）。

> 請注意這會將 SwiftLint 二進位檔案、所依賴的二進位檔案和 Swift 二進位程式庫安裝到 `Pods/` 目錄下，所以不建議將此目錄新增至版本控制系統（如 git）中進行追蹤。
> 

# 使用


### **Xcode**

到 Xcode 的 `Build Phases` ，按下 + 新增一個腳本

![截圖 2024-04-22 14.43.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-22_14.43.02.png?raw=true)

輸入

```bash
if which swiftlint > /dev/null; then
  swiftlint
else
  echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```

![截圖 2024-04-22 14.44.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-22_14.44.24.png?raw=true)

或者透過 CocoaPods 安裝的話，也可以直接

```yaml
"${PODS_ROOT}/SwiftLint/swiftlint"
```

假如是使用 Xcode 15 的話，要注意，因為 Build Settings 進行了重大更改，它將 `ENABLE_USER_SCRIPT_SANDBOXING` 的預設值從 `NO` 改為 `YES`。 因此，SwiftLint 會遇到與缺少檔案權限相關的錯誤，通常報錯訊息為： `error: Sandbox: swiftlint(19427) deny(1) file-read-data.`

若要解決此問題，只需要手動將 `ENABLE_USER_SCRIPT_SANDBOXING` 設定為 `NO`，以針對 SwiftLint 配置的特定目標。

![截圖 2024-04-22 14.47.53.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-22_14.47.53.png?raw=true)

### **搭配 pre-commit**

安裝及基本使用可以到[這裡](https://github.com/chengyang1380/ProgrammingNotes/blob/main/iOS/pre-commit.md)看

使用之後，每次 commit 的檔案都會被檢查

![截圖 2024-04-25 14.26.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-25_14.25.06.png?raw=true)

![截圖 2024-04-25 14.25.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/SwiftLint/%25E6%2588%25AA%25E5%259C%2596_2024-04-25_14.26.16.png?raw=true)

### **Fastlane**

可以用 [fastlane 官方的 SwiftLint 功能](https://docs.fastlane.tools/actions/swiftlint) 來運行 SwiftLint

```yaml
desc "SwiftLint"
lane :lint do
	swiftlint(
  		mode: :lint,
  		strict: true,
  		config_file: ".swiftlint.yml",
  		raise_if_swiftlint_error: true,
  		ignore_exit_status: false
	)
end
```

# 規則

現在目前已經超過 200 條規則了，那 SwiftLint 的團隊還是希望有更多的貢獻，所以期待大家提交 [Pull Requests](https://github.com/realm/SwiftLint/blob/main/CONTRIBUTING.md)。

 在[這裡](https://realm.github.io/SwiftLint/rule-directory.html)可以看到規則的更新列表及更多訊息。

那我們可以在我們的專案目錄下，新增一個 `.swiftlint.yml` 的文件，來配置 SwiftLint。

一些常見的設定：

- `disabled_rules`: 關閉某些預設開啟的規則。
- `opt_in_rules`: 有些規則預設是關閉的，想要開啟可以在這打開。
- `only_rules`: 不可以和 `disabled_rules` 或 `opt_in_rules` 並列。 類似一個白名單，只有在這個清單中的規則才是開啟的。

```yaml
disabled_rules: # 執行時排除掉的規則
  - colon
  - comma
  - control_statement
opt_in_rules: # 有些規則是預設關閉的，所以你需要手動啟用
  - empty_count # 可以透過執行以下指令來尋找所有可用的規則：`swiftlint rules`
# 或者，透過取消對該選項的註解來明確指定所有規則：
# only_rules：# 若使用，請刪除 `disabled_rules` 或 `opt_in_rules`
#   - empty_parameters
#   - vertical_whitespace

included: # 執行 linting 時包含的路徑。 如果出現這個 `--path` 會被忽略。
  - Sources
excluded: # 執行 linting 時忽略的路徑。 優先權比 `included` 更高。
  - Carthage
  - Pods
  - Sources/ExcludedFolder
  - Sources/ExcludedFile.swift

# 如果值為 true，SwiftLint 將把所有警告視為錯誤
strict: false
```

一些常用的規則

```yaml
type_body_length:
  - 300 # warning
  - 400 # error
# 或者也可以同時進行明確設定
file_length:
  warning: 500
  error: 1200
# 命名規則可以設定最小長度和最大程度的警告/錯誤
# 此外它們也可以設定排除在外的名字
type_name:
  min_length: 4 # 只是警告
  max_length: # 警告和錯誤
    warning: 40
    error: 50
  excluded: iPhone # 排除某个名字
identifier_name:
  min_length: # 只有最小長度
    error: 4 # 只有錯誤
  excluded: # 排除某些名字
    - id
    - URL
    - GlobalAPIKey
```

一個範例 .swiftlint.yml

```yaml
disabled_rules:
  - colon
  - comma
  - control_statement
  - line_length
  - identifier_name
  - cyclomatic_complexity
  - type_body_length
  - force_cast
  - trailing_newline
  - function_body_length
  - large_tuple
  - todo
  - trailing_comma

excluded:
  - Pods
  - R.generated.swift
  - fastlane
  - Frameworks

reporter: "xcode" # reporter type (xcode, json, csv, checkstyle)
```

# 自動修正


有些規則是可以自動修正的，像是額外空格，只要進行 SwiftLint 分析，就可以自動修正。

1. 可以在 Xcode 的 `Build Phase` ，新增一個腳本

```bash
export PATH="$PATH:/opt/homebrew/bin"
if which swiftlint > /dev/null; then
  swiftlint --fix && swiftlint
else
  echo "warning: SwiftLint not installed, download from https://github.com/realm/SwiftLint"
fi
```

> 請確保在對檔案執行 `swiftlint autocorrect` 之前有對它們做過備份，否則的話有可能導致重要資料的遺失。因為在執行自動更正修改某個檔案後很有可能導致先前產生的程式碼檢查資訊無效或不正確，所以執行程式碼更正時標準的檢查是無法使用的。
> 

1. 也可以用 Fastlane，把 mode 參數改為 `fix` 

```yaml
desc "SwiftLint"
lane :lint do
	swiftlint(
  		mode: :fix,
  		strict: true,
  		config_file: ".swiftlint.yml",
  		raise_if_swiftlint_error: true,
  		ignore_exit_status: false
	)
end
```

# 最後


所以我們在寫 code 的時候，會層層把關 coding style，以下總共三關

- 第一關就是用我們的 IDE (Xcode) 在 build code 時
- 第二關就是在 commit 時，透過 pre-commit 檢查
- 第三關就是透過 CI/CD 流程中，發 PR 時觸發 Fastlane 來檢查