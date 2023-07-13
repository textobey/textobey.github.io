---
title: "Meet async/await in Swift(1)"
excerpt: "Swift Concurrency로 내딛는 첫걸음"

categories:
  - iOS
tags:
  - [iOS, WWDC, Concurrency]

permalink: /ios/meet-async-await-in-swift_1/

toc: true
toc_sticky: true

date: 2023-07-13
last_modified_at: 2023-07-13
---

안녕하세요!

오늘은 WWDC21의 Meet async/await in Swift 영상 내용을 정리해볼게요.
영상 내용이 길지는 않지만, 새로운 기술을 접하는만큼 숨을 나눠가며
정리하는 것이 좋을것 같아서 제 나름의 기준으로 내용을 나누어 포스팅 해볼게요 :)

> Meet async/await in Swift(WWDC21) 링크 : 
[https://developer.apple.com/videos/play/wwdc2021/10132/](https://developer.apple.com/videos/play/wwdc2021/10132/)

---


# 비동기 프로그래밍

이제는 비동기 프로그래밍은 많은 사람들에게 일상적인 작업이에요.
하지만, 이에 따라 **장황하고, 복잡하며, 심지어는 부정확한 비동기 코드**를
**작성하게 되는 것이 너무 쉬워졌다**고 다들 인지하고 있어요.

## Example

영상에서 들고 있는 예를 통해 정리해볼게요.
UIKit 프레임워크에는 UIImage가 썸네일을 형성하는 기능을 제공하고 있어요.

```swift
// sync
func preparingThumbnail(of size: CGSize) -> UIImage?

// async
func prepareThumbnail(of size: CGSize, completionHandler: @escaping (UIImage?) -> Void)
```
> 혹시 sync/async에 대해 알고 계신가요? 부족하지만 아래 포스팅을 참고해주세요 :)
[https://textobey.github.io/ios/sync_async_serial_concurrent/](https://textobey.github.io/ios/sync_async_serial_concurrent/)



위 2개의 메서드에서 중점적으로 살펴볼 메서드는 async 메서드인 prepareThumbnail(_:) 이에요.
async 메서드는 호출되면 Task가 실행되는 동안 스레드가 자유롭게 다른 작업을 수행할 수 있어요.
그리고, Task가 완료되는 시점은 completionHandler를 호출하여 알려주고 있죠.

![](https://velog.velcdn.com/images/textobey/post/4123cfe6-a8ab-4ad7-8c76-3f0233d2af95/image.png)


### Fetching a thumbnail

위의 prepareThumbnail(_:) 메서드를 활용하여 아래의 화면을 구성한다고 해볼게요.

![](https://velog.velcdn.com/images/textobey/post/f6e18d52-ec01-460d-ba13-0275af866a06/image.png)

썸네일을 UITableViewCell에 표시해줄 준비가 되면 ViewModel에서는 아래의 작업이 수행돼요.
아래에서 수행되는 작업들은 모두 **이전에 수행된 작업의 결과에 따라 결과가 달라**지기 때문에,
**순서가 보장되어야** 해요.

![](https://velog.velcdn.com/images/textobey/post/f8aef9e4-84ca-46ee-8e16-bb0535bdac5f/image.png)

1. `thumbnailURLReqeust`, `UIImage(data:)` :
메서드는 값을 빠르게 반환하기에 **동기식 호출**이 되도록 하는것이 좋아요.
2. `dataTask(with:completion:)` :
메서드는 이미지를 구성하는 모든 데이터를 다운로드하는 데 시간이 걸리기 때문에,
**비동기식 호출**이 되도록 하는것이 좋아요.
3. `prepareThumbnail(of:completionHandler:)` :
메서드는 멋진 썸네일을 렌더링 하기 위해서는 비용이 많이 드는(비싼) 작업을 수행하는 장치가 필요해요.
그렇기 때문에, 이 메서드 또한 **비동기식 호출**이 되도록 하는것이 좋아요.


위의 작업을 비동기식 함수와 completionHandler를 통해 구현해볼게요.
아래 코드는 모든게 잘 이루어진다는 가정하에 충분히 썸네일 이미지를 준비해오기 위한
의도와 결과를 충족 시킬 수 있어요.

```swift
func fetchThumbnail(for id: String, completion: @escaping (UIImage?, Error?) -> Void) {
    let request = thumbnailURLRequest(for: id)
    let task = URLSession.shared.dataTask(with: request) { data, response, error in
        if let error = error {
            completion(nil, error)
        } else if (response as? HTTPURLResponse)?.statusCode != 200 {
            completion(nil, FetchError.badID)
        } else {
            guard let image = UIImage(data: data!) else {
                // completionHandler 누락
                return
            }
            image.prepareThumbnail(of: CGSize(width: 40, height: 40)) { thumbnail in
                guard let thumbnail = thumbnail else {
                    // completionHandler 누락
                    return
                }
                completion(thumbnail, nil)
            }
        }
    }
    task.resume()
}
```

### 문제점

그러나!!! 이것은 모든게 순조롭다는 가정하에 얘기하는 것이고,
또, 모든게 순조롭다고 하여 위의 코드에 문제가 없는 것은 아니에요.

위 코드의 문제점을 짚어볼게요.

`guard else return` 을 쓰는데에 너무 익숙해있어서 completionHandler를 호출하는 것을
**2번**씩이나 잊어버렸어요. 이는, fetchThumbnail 메서드를 호출하는 caller를 빠뜨려요.

따라서, 데이터에서 UIImage를 생성하거나 썸네일을 준비하는 데 실패하면
**누락된 completionHandler** 호출로 인해 caller에게 **알림이 전송되지 않고**
UITableViewCell은 업데이트 되지 않을거예요.

이 메서드의 작성자인 우리는 무슨일이 있어도 caller에게 알려야하는 것이
매우 중요하지만 이를 지키지 못한것이죠.

따라서, 이 문제를 개선하고, 충족시키기 위해서는 무엇이 되었든 모든 경로는 이를 알려야해요.
아래는 누락된 completionHandler 호출을 추가하여 이를 개선, 충족시킨 모습이에요.
![](https://velog.velcdn.com/images/textobey/post/51de240b-c33f-4cce-900a-32ec7a1e757a/image.png)

### completionHandler (비동기) 메서드의 문제점

이것이 Async/await 이전의 completionHandler 호출을 통한 비동기식 메서드를 작성하는 방법이었어요.
하지만, 작성된 코드는 동작을 하지만 completionHandler 호출을 사용하는 구조는

1. completionHandler 호출 시점에 **버그 발생을 야기**해요.
2. 코드를 제대로 **이해하기 어렵게** 만들고, 우리의 **의도를 불분명**하게 만들어요.
3. completionHandler를 호출하지 않는다고 해서 Swift는 에러를 발생시키 않아요.
(Swift 입장에서 completionHandler는 그저 클로저일 뿐이기 때문에)

그렇기 때문에, Swift는 우리가 하는 작업을 학인할 수 없다는 것을 의미하고
completionHandler를 항상 호출하도록 하고 싶어도, **Swift는 이를 강제할 방법이 없어**요.

사람들은 이것을 개선하기 위해서 사람들은 표준 라이브러리은 Result 타입을 사용하거나,
Future 같은 기술을 사용하여 다른 방식으로 비동기 코드를 개선했어요.

```swift
func fetchThumbnail(for id: String, completion: @escaping (Result<UIImage, Error>) -> Void) {
    let request = thumbnailURLRequest(for: id)
    let task = URLSession.shared.dataTask(with: request) { data, response, error in
        if let error = error {
            completion(.failure(error))
        } else if (response as? HTTPURLResponse)?.statusCode != 200 {
            completion(.failure(FetchError.badID))
        } else {
            guard let image = UIImage(data: data!) else {
                completion(.failure(FetchError.badImage))
                return
            }
            image.prepareThumbnail(of: CGSize(width: 40, height: 40)) { thumbnail in
                guard let thumbnail = thumbnail else {
                    completion(.failure(FetchError.badImage))
                    return
                }
                completion(.success(thumbnail))
            }
        }
    }
    task.resume()
}
```

이런 개선 방법은 조금 더 안전한 코드를 작성하게 해주지만
코드를 좀 더 못생기게 만들고, 더 길게 만드는 아쉬운 점이 있어요.

**그렇다면, 안전하고 간단하고 쉽게 코드를 작성하는 법은 없을까?**
이런 부분을 시원하게 긁어줄 수 있는 방법이 Swift Async/await 이라고 하네요.

---
