# Bolts-Swift

`Bolts-Swift` 是 Facebook 做的套件，最主要就是處理一些非同步的事情，然後好串連再一起，避免 `callback hell` ，另一個比較有名的代表就是 `PromiseKit` ，也是解決一樣的問題。

🔍 [PromiseKit](https://github.com/mxcl/PromiseKit)
🔍 [Bolts-Swift](https://github.com/BoltsFramework/Bolts-Swift)

## 為何需要使用這些類型的套件？

使用這些套件，最常見的情境就是網路請求，我們不可能是 sync (同步)處理，一定會 async (非同步)，那最單純的不用套件的寫法就類似長這樣
```swift
func loadUserID(completionHandler: ((Result<String, Error>) -> Void)?) {
  // send request...
  if response.isSuccess {
    completionHandler?(.success("A001"))
  } else {
    completionHandler?(.error(SomeError())))
}
```
使用時就是
```swift
loadUserID { result in
  switch result {
  case .success(let id):
    print(id)
  case .failure(let error):
    print(error.localizedDescription)
  }
}
```
目前這樣好像還不錯，使用上沒有什麼問題，但假如需求變成：加載完用戶的 ID 後，要接著馬上 call 其他 API，那經典的 `callback hell` 可能就會看見了。
```swift
loadUserID { result in
  switch result {
  case .success(let name):
    self.loadOtherAPI { result in
      switch result {
      case .success(let data):
        print(data)
        self.loadOtherAPI { result in
          switch result {
          case .success(let data):
            print(data)
          case .failure(let error):
            print(error.localizedDescription)
          }
      }
      case .failure(let error):
        print(error.localizedDescription)
      }
    }
    print(name)
   case .failure(let error):
    print(error.localizedDescription)
  }
}
```
這根本是惡夢的開始...

## 換成使用 Bolts 處理

首先 function 的部分
```swift
func loadUserID() -> Task<String> {
  let taskSource = TaskCompletionSource<String>()
  // send request...
  taskSource.set(result: "A001")
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}

func loadOtherAPI() -> Task<String> {
  let taskSource = TaskCompletionSource<String>()
  // send request...
  taskSource.set(result: "Other API")
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}
```
再來使用時就變成
```swift
func basicUse() {
  loadUserID().continueOnSuccessWithTask { userID -> Task<String> in
    print(userID)
    return self.loadOtherAPI()
  }.continueOnSuccessWithTask { otherAPIResult -> Task<String> in
    print(otherAPIResult)
    return self.loadOtherAPI()
  }.continueOnSuccessWith { otherAPIResult in
    print(otherAPIResult)
  }.continueOnErrorWith { error in
    print(error.localizedDescription)
  }
}
```
就不會造成 `callback hell` 了

## Bolts 的基本觀念

### 單一個任務時
```swift
loadUserID().continueWith { task in
  guard let userID = task.result else {
  // handle error...
    print((task.error ?? SomeError()).localizedDescription)
    return
  }
  // handle success
  print(userID)
}
```
也可以寫成
```swift
loadUserID().continueOnSuccessWith { userID in
  // handle success
  print(userID)
 }.continueOnErrorWith { error in
  // handle error...
  print(error.localizedDescription)
}.continueWith { task in
  print("continueWith :\(task)")
}
// 執行結果：
// A001
// continueWith :Task: success()
```
這個範例中的 `continueWith` 等於先跑 success 或 error 後，下一個會跑的地方，所以可以放一些不管結果如何都要處理的 code ，像是我們 call API 前可能會先呼叫出 `UIActivityIndicatorView` (loading 動畫），後續不管成功失敗都要把它 `hide` ，就很適合放在這。

這些 `contiuneWith, onSuccess, onError` 的 *順序* 其實是很重要的，假設把上述例子的順序調整一下
```swift
loadUserID().continueWith { task in
  print("continueWith :\(task)")
}.continueOnSuccessWith { userID in
  // handle success
  print(userID)
 }.continueOnErrorWith { error in
  // handle error...
  print(error.localizedDescription)
}
// 執行結果：
// continueWith :Task: success("A001")
// ()

```
兩者的執行結果很明顯是不一樣的，在這個範例的 `continueWith` 後的 `continueOnSuccessWith` 的 `result`，已經不是 `userID`，是 `continueWith` 的 return 值
，所以假設把 `continueWith` 調整一下
```swift
loadUserID().continueWith { task -> String in
  print("continueWith :\(task)")
  return task.result!
}.continueOnSuccessWith { result in
  // handle success
  print(result)
}.continueOnErrorWith { error in
  // handle error...
  print(error.localizedDescription)
}
// 執行結果：
// continueWith :Task: success("A001")
// A001
```
所以從這個範例可以得知 `loadUserID()` 這個 task，經過了 `continueWith` 後，表示該 task 已經被處理了，後續的 `onSuccess, onError` 是針對 `continueWith` 的結果處理。

再把這個例子調整一下，會更好理解。
先把 `loadUserID()` 改成 `taskSource.set(error: SomeError())`。
```swift
loadUserID().continueWith { task -> String in
  print("continueWith :\(task)")
  return task.result ?? "unknown"
}.continueOnSuccessWith { result in
  // handle success
  print(result)
}.continueOnErrorWith { error in
  // handle error...
  print(error.localizedDescription)
}
```
這時的結果是什麼勒？ `continueOnErrorWith` 會被執行嗎？
執行結果：
```swift
// continueWith :Task: error(testBolts_Swift.ViewController.SomeError())
// unknown
```
答案是不會執行 `onError` ，因為前面有執行 `continueWith`，後續要跑到 `onError` 的話，要調整成
```swift
loadUserID().continueWith { task -> String in
  print("continueWith :\(task)")
  throw SomeError()
}.continueOnSuccessWith { result in
  // handle success
  print(result)
}.continueOnErrorWith { error in
  // handle error...
  print(error.localizedDescription)
}
// 執行結果：
// continueWith :Task: error(testBolts_Swift.ViewController.SomeError())
// The operation couldn’t be completed. (testBolts_Swift.ViewController.SomeError error 1.)
```
## 進階的使用

從上述大量的例子我們能得知 `continueWith` `continueOnSuccessWith` `continueOnErrorWith` 之間的使用，但常常我們有些需求是 call A API 後在用 A API 的結果去 call B API 。

那我們來看範例，首先跟前面例子相似，假設我們有兩隻 API `(loadUserID, loadOtherAPI)` ，我們需要先 call `loadUserID` 在拿他的結果去 call `loadOtherAPI`。
```swift
func loadUserID() -> Task<String> {
  let taskSource = TaskCompletionSource<String>()
  // send request...
  taskSource.set(result: "A001")
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}

func loadOtherAPI(param: String) -> Task<String> {
  let taskSource = TaskCompletionSource<String>()
  // send request...
  taskSource.set(result: "Other API")
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}
```
操作就變成
```swift
loadUserID().continueOnSuccessWithTask { userID -> Task<String> in
  print(userID)
  return self.loadOtherAPI(param: userID)
}.continueOnSuccessWith { otherAPIResult in
  print(otherAPIResult)
}.continueOnErrorWith { error in
  print(error.localizedDescription)
}
```
我們知道需求是 `loadUserID()` 成功後要 call 其他 API ，所以這裡要用 `continueOnSuccessWithTask`，那在這後續其實也可以用 `continueWith` ，來處理成功或失敗，
範例是寫 `onSuccess` `onError`，這裡要注意的是 `continueOnErrorWith` 也會接 `loadUserID` 的錯誤。

最後再來看一個常見的需求，所有 API 都結束後要做一件事情，所以各 API 無需等待其他人。
例如現在要建立一篇貼文，貼文裡可以放多張照片，這時要先 call 上傳照片的 API，等全部照片上傳完，才能 call 建立貼文的 API。

```swift
func uploadImage() -> Task<String> {
  let taskSource = TaskCompletionSource<String>()
  // send request...
  taskSource.set(result: "img_01")
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}

func createPost(imageIDs: [String]) -> Task<Void> {
  let taskSource = TaskCompletionSource<Void>()
  // send request...
  taskSource.set(result: Void())
  // error:
  // taskSource.set(error: SomeError())
  return taskSource.task
}
```
使用上
```swift
let tasks: [Task<String>] = [
  uploadImage(),
  uploadImage(),
  uploadImage(),
]
Task.whenAllResult(tasks).continueWithTask { task -> Task<Void> in
  guard let imageIDs = task.result else {
    throw SomeError()
  }
  return self.createPost(imageIDs: imageIDs)
}.continueOnSuccessWith { _ in
  print("success")
}.continueOnErrorWith { error in
  print(error.localizedDescription)
}
```
假設在 `uploadImage()` 發生 error 就會在 `continueOnErrorWith` 上處理，因為不管怎樣都會觸發 `continueWithTask` 但在那的 `task.result` 會無法拿到，所以會 `throw` error 。

