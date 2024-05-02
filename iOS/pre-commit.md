# pre-commit

![IMG_0568.JPG](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/IMG_0568.jpg?raw=true) Photo in New Zealand — Franz Josef Glacier

[pre-commit](https://pre-commit.com) 簡單來說，就是當我們在 git 上要 commit 時會先檢查程式碼的工具，可以輕鬆規範我們的程式碼風格。

# 安裝

---

## 1.  安裝 pre-commit

使用 [homebrew](https://brew.sh/):

```bash
brew install pre-commit
```

再來我們就可以檢查有沒有安裝好

```bash
$ pre-commit --version
```

這時應該可以拿到安裝好的版號，我這邊是 `pre-commit 3.7.0`

## 2.  新增 pre-commit 配置檔

我們在專案底下，與 `.git` 是同一層，新增 `.pre-commit-config.yaml` 

```bash
.
├── README.md
├── .git
├── .pre-commit-config.yaml
└── someFile
```

再來我們可以用 

```bash
$ pre-commit sample-config
```

快速生一個簡單的範本

```yaml
# See https://pre-commit.com for more information
# See https://pre-commit.com/hooks.html for more hooks
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v3.2.0
    hooks:
    -   id: trailing-whitespace
    -   id: end-of-file-fixer
    -   id: check-yaml
    -   id: check-added-large-filesa
```

我們可以看到裡面的 

- repo: repository 存放位置 [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
- rev: 對應版本為 v.3.2.0
- id: 類似一些規則，可以在[這裡](https://pre-commit.com/hooks.html)找到對應的說明，可以找合適的 hooks 來用

範例上的這4個 hooks 說明：

- `trailing-whitespace` 去除每行結尾的空白
- `end-of-file-fixer` 確保每個檔案最後 1 行是空行
- `check-yaml` 檢查 repo 中的 yaml 檔格式是否正確
- `check-added-large-files` 防止大檔案被 commit 進 repo

## 3.  安裝 git hook scripts

我們執行 `pre-commit install` 來安裝

```yaml
$ pre-commit install
```

這時我們應該會得到這個訊息

```yaml
pre-commit installed at .git/hooks/pre-commit
```

我們現在操作 `git commit` 時會自動執行 `pre-commit` 了

## 4.  執行

再來我們就可以測試看看執行的結果，隨便改幾個檔案，再來執行

```bash
$ git commit -m 'test pre-commit'
```

或者可以執行 

```bash
$ pre-commit run --all-files
```

結果

```bash
[WARNING] Unstaged files detected.
[INFO] Stashing unstaged files to /Users/luan/.cache/pre-commit/patch1713779107-92771.
[INFO] Initializing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Installing environment for https://github.com/pre-commit/pre-commit-hooks.
[INFO] Once installed this environment will be reused.
[INFO] This may take a few minutes...
Trim Trailing Whitespace.................................................Passed
Fix End of Files.........................................................Passed
Check Yaml...........................................(no files to check)Skipped
Check for added large files..............................................Passed
[INFO] Restored changes from /Users/luan/.cache/pre-commit/patch1713779107-92771.
[feature/1.0.0/add_pre_commit 0ce46de9] test pre-commit
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### 使用 Sourcetree
被檢查出有問題
![截圖 2024-04-25 14.25.06.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/%25E6%2588%25AA%25E5%259C%2596_2024-04-25_14.25.06.png?raw=true)

檢查都通過
![截圖 2024-04-25 14.26.16.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/%25E6%2588%25AA%25E5%259C%2596_2024-04-25_14.26.16.png?raw=true)

# 最後

---
假如有特別情境，想暫時跳過檢查，可以

```bash
git commit --no-verify -m "YOUR COMMIT MESSAGE"
```

在看各團隊需要什麼檢查規則，例如我這邊額外加了 swiftLint 的規範。

```yaml
# See https://pre-commit.com for more information
# See https://pre-commit.com/hooks.html for more hooks
repos:
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v4.6.0
  hooks:
  - id: trailing-whitespace
  - id: end-of-file-fixer
  - id: check-yaml
  - id: check-added-large-files

- repo: https://github.com/realm/SwiftLint
  rev: 0.54.0
  hooks:
  - id: swiftlint
    name: SwiftLint
    description: "Check Swift files for issues with SwiftLint"
    entry: swiftlint --fix --strict --config .swiftlint.yml
    language: swift
    types: [swift]
```

# 參考

---

- [https://pre-commit.com](https://pre-commit.com/)
- [https://myapollo.com.tw/blog/pre-commit-the-best-friend-before-commit/](https://myapollo.com.tw/blog/pre-commit-the-best-friend-before-commit/)
- [https://blog.kyomind.tw/pre-commit/](https://blog.kyomind.tw/pre-commit/)
- [https://www.mropengate.com/2019/08/pre-commit-git-hooks_4.html](https://www.mropengate.com/2019/08/pre-commit-git-hooks_4.html)
