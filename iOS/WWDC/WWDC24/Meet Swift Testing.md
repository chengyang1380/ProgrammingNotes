# Meet Swift Testing

![New Zealand — Deer Park Heights Queenstown](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/IMG_8347.jpg?raw=true)
New Zealand — Deer Park Heights Queenstown

- Building blocks 先了解核心概念
- Common workflows 討論幾種常見的工作流程，包括自定義測試或使用不同參數重複測試的方法。
- Swift Testing and XCTest 介紹 Swift Testing 和 XCTest 之間的相互關係
- Open source 這個新項目在開源社區中的角色

## Building blocks

### Test functions

- Annotated using `@Test`
- Can be global functions or methods in a type
- May be `async` or `throws`
- May be  global actor-isolated (such as `@MainActor` )

![截圖 2024-08-19 14.31.28.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.31.28.png?raw=true)

以前使用 `XCTest` 要執行測試的 function 都要用 test 開頭命名，例如

```swift
func test_doSomething() throws { ... }
```

現在加上 `@Test` 就不用了

### Expectations

Validate expected conditions

- Use `#expect(...)` to validate that an expected condition is true
- Accepts ordinary expressions and operators
- Captures source code and subexpression values upon failure

![截圖 2024-08-19 13.50.01.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_13.50.01.png?raw=true)

### Required expectations

有時出現預期失敗，可能會希望提前結束測試，這時可以用 `#require` 

- Use `try #require(...)`  to stop the test if the condition is false
    
    ![截圖 2024-08-19 13.52.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_13.52.54.png?raw=true)
    
- Can unwrap optional values, and stop test when `nil`
    
    ![截圖 2024-08-19 13.53.37.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_13.53.37.png?raw=true)
    

### Traits

- Add descriptive information about a test
- Customization whether a test runs
- Modify how a test behaves

![截圖 2024-08-19 14.04.02.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.04.02.png?raw=true)

### Suites

- Group related test functions and suites
- Annotated using `@Suite`
    - Implicit for types containing `@Test`  functions or suites
- May have stored instance properties
- Use `init` and `deinit` for set-up and tear-down logic, respectively
- Initialized once per instance `@Test` method

以前我們使用 `XCTest` 都用 class，例如 

```swift
final class SomeTests: XCTestCase { ... }
```

現在建議使用 struct

![截圖 2024-08-19 14.21.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.21.17.png?raw=true)

## Common workflows

![截圖 2024-08-19 14.33.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.33.22.png?raw=true)

### Tests with conditions

### Runtime conditions

Controlling when tests run

- Specify a runtime-evaluated condition for a test using `.enabled(if:...)`
- Test will be skipped if condition is false

![截圖 2024-08-19 14.39.04.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.39.04.png?raw=true)

- Unconditionally disable a test using `.disabled(...)`
- Comment describing reason is included in results
- 這個方法比註解掉測試碼更好，因為他會驗證測試內的程式碼是否可編譯。

![截圖 2024-08-19 14.43.54.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.43.54.png?raw=true)

- Pair any trait with `.bug(...)` to reference an issue in a bug-tracking system

![截圖 2024-08-19 14.46.22.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.46.22.png?raw=true)

- Use `@available(...)`  to specify an OS availability condition

![截圖 2024-08-19 14.49.15.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.49.15.png?raw=true)

- Use `@available(...)` instead of checking at runtime using `#available(...)`
    
    ![截圖 2024-08-19 14.50.11.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.50.11.png?raw=true)
    

### Tests with common characteristics

利用 `.tag(...)` ，在左邊導航列可以找到相關被 tag 的測試，各 function 也可以在 `@Test` 上加 `.tag` 

![截圖 2024-08-19 14.58.41.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_14.58.41.png?raw=true)

進階一點可以額外寫一個 struct 把 tag 寫在這層，原本各自 function 內的 tag 就可以移除了

![截圖 2024-08-19 15.01.57.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_15.01.57.png?raw=true)

**Test Tags**

Associate tests which have things in common to:

- Run all tests with a specific tag
- Filter by tag or see insights in Test Report

Shared among tests anywhere in a project

Tags can be local to a project or shared

擴充 Tag

```swift
extension Tag {
    @Tag static var formatting: Self
    @Tag static var networking: Self
}
```

Recommended practices

Prefer tags over test names when including/excluding in a Test Plan

Use the most appropriate trait type

- Example: `.enabled(if: ...)`  instead of `.tags(...)` to represent a condition

更進階的用法可以看 
[Go further with Swift Testing WWDC24](https://developer.apple.com/wwdc24/10195)


### Tests with different arguments

假如我們有許多內容很像的測試，只是某部分參數不同，這時候就很適合用 **Parameterized Tests**「參數化測試」

![截圖 2024-08-19 15.25.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_15.25.24.png?raw=true)

優化後

![截圖 2024-08-19 15.29.24.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_15.29.24.png?raw=true)

再來一個例子，以下這個做法用 `for-loop` 循環重複多次單個測試，但這個做法不是最好的，最好就是用 parameterized test function

![截圖 2024-08-19 15.44.35.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_15.44.35.png?raw=true)

優化後

![截圖 2024-08-19 17.41.34.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_17.41.34.png?raw=true)

**Parameterized testing**

- View details of each argument in results
- Re-run individual arguments to debug
- Run arguments in parallel

更進階的用法可以看 

Go further with Swift Testing WWDC24

https://developer.apple.com/wwdc24/10195

## Swift Testing and XCTest

![截圖 2024-08-19 17.47.08.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_17.47.08.png?raw=true)

![截圖 2024-08-19 17.47.17.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_17.47.17.png?raw=true)

![截圖 2024-08-19 17.50.03.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_17.50.03.png?raw=true)

鼓勵使用 struct，因為是 value type，這樣做可以避免無意的狀態共享而導致的錯誤

**Migrating from XCTest**

✅ Recommended practices  

- Share a single unit test target
    - Swift Testing tests can coexist with XCTests
- Consolidate similar XCTests into a parameterized test
- Migrate each XCTest class with only one test method to a global `@Test` function
- Remove redundant “test” prefix from method names

⚠️ Supported functionality 

Continue using XCTest for tests which:

- Use UI automation APIs (such as `XCUIApplication` )
- Use performance testing APIs (such as `XCTMetric` )
- Can only be written in Objective-C

Avoid calling `XCTAsssert(...)`  from Swift Testing tests, or `#expect(...)` from XCTests

更多細節可參考 [**Migrating a test from XCTest**](https://developer.apple.com/documentation/testing/migratingfromxctest)

## Open source

它可以用於

- Apple Platforms
- Linux
- Windows

而且在這些平台上都有通用的程式碼庫 

![截圖 2024-08-19 18.03.07.png](https://github.com/chengyang1380/ProgrammingNotes/blob/main/Images/WWDC/WWDC24/Meet%20Swift%20Testing/%25E6%2588%25AA%25E5%259C%2596_2024-08-19_18.03.07.png?raw=true)

## Wrap-up

- Improve quality with Swift Testing
- Customize tests with traits
- Contribute in open source

最後可以了解更多細節
[Go further with Swift Testing WWDC24](https://developer.apple.com/wwdc24/10195)


## 參考

- https://wwdcnotes.com/documentation/wwdcnotes/wwdc24-10179-meet-swift-testing/
- https://leocoout.medium.com/welcome-swift-testing-goodbye-xctest-7501b7a5b304
- https://developer.apple.com/documentation/testing/migratingfromxctest
- https://developer.apple.com/videos/play/wwdc2024/10179/
- https://www.youtube.com/watch?v=WFnkNcvLnCI