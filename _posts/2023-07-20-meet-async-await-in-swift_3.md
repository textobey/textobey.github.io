---
title: "Meet async/await in Swift(3)"
excerpt: "Swift Concurrency로 내딛는 첫걸음"

categories:
  - iOS
tags:
  - [iOS, WWDC, Concurrency]

permalink: /ios/meet-async-await-in-swift_3/

toc: true
toc_sticky: true

date: 2023-07-20
last_modified_at: 2023-07-20
---

이제 썸네일을 형성하는 메서드를 살펴보는 단계에서 더욱 크게 시야를 넓혀
아래 애플리케이션 코드 자체를 살펴보는 것으로 확대해볼게요.

이전 포스팅은 [여기](https://textobey.github.io/ios/meet-async-await-in-swift_2/)서 확인해주세요 :)

# Bridging from sync to async

아래 Posts 화면은 SwiftUI 코드로 작성되어 있고,
List를 활용하여 각 Row에 ThumbnailView를 뿌려주고 있는 꽤 간단한 구조예요.

![](https://velog.velcdn.com/images/textobey/post/6c7365e0-7dc0-4f5c-b4b1-c4be879ed93f/image.png)

## Calling async functions

코드를 보면, Post 오브젝트를 활용하여 viewModel에 있는
비동기식 `fetchThumbnail(for:)` 메서드를 통해 썸네일을 만들고, image에 결과를 전달해주고 있어요.

```swift
struct ThumbnailView: View {
    @ObservedObject var viewModel: ViewModel
    var post: Post
    @State private var image: UIImage?

    var body: some View {
        Image(uiImage: self.image ?? placeholder)
            .onAppear {
                self.viewModel.fetchThumbnail(for: post.id) { result _ in
                    self.image = result
                }
            }
    }
}
```

우리는 앞선 포스팅에서 completionHandler를 호출하는 방법이 아닌
async/await을 활용하여 이것을 변환하는 방법을 이제 알았기 때문에,
위의 코드는 아래처럼 짧고, 이해가 쉽도록 변환할 수 있어요 !

![](https://velog.velcdn.com/images/textobey/post/a7650a49-8f4d-4c5b-8cc5-690ce26d6453/image.png)

하지만, 이 코드를 작성하려고 하면 Swift는 에러를 발생시켜요. 왜일까요?
에러 내용이 알려주듯 비동기가 아닌 context에서 비동기 함수를 호출할 수 없기 때문이에요.

onAppear 수식어는 non-async 클로저(일반 sync 클로저라는 뜻이죠?)예요.
그렇기에, 바로 여기서 sync 와 async 사이의 간격을 bridge 해줄 수 있는 방법이 필요해요.

해결책은 비동기 Task function을 사용하는것이에요.
비동기 Task는 클로저에서 작업을 패지키화 하고, 즉시 실행 가능한 다음 스레드로 보내요.
GCD의 global dispatch queue에서 실행되는 비동기 Task처럼요.

이렇게 Task 클로저로 얻을 수 있는 이점은 **sync context 내부에서 async 코드를 호출할 수 있다는 것**이에요.

![](https://velog.velcdn.com/images/textobey/post/dcd955c7-1ebe-4b6d-b9f3-49d4f9cf65a1/image.png)

위의 코드처럼 수정하면 이제 Swift 컴파일러는 조건이 만족되었기 때문에 에러를 표시하지 않을거예요.
비동기 Task는 이런식으로 자연스럽게 구조화된 스타일로 Swift에서 concurrency 코드를 작성하게 해주는 API의 일부이고, 예시 코드처럼 활용한다면 sync와 async의 간격을 bridge 해줄 수 있는 것이죠.

# Async alternatives

하나의 예로 `fetchThumbnail(for:)` 메서드를 마이그레이션 한 것처럼
우리의 앱은 async/await을 채택할 수 있는 기회가 더 많이 있어요.

이걸 빠르게 하고 싶다면 기존 API에 대한 async 대안으로 작게 시작하는 것이 좋다고해요.

SDK는 completionHandler를 호출하는 구조의 비동기 방식의 API를 수백 개를 제공하고 있는데요, 이것들을 앞서 소개했던 await 방식으로 바꿀 수 있다면 엄청나겠죠?

![](https://velog.velcdn.com/images/textobey/post/7ee8050a-bf5d-4aea-9e4b-8aa1a9e483ca/image.png)

## Async APIs in the SDK

Swift 5.5에서부터는 Swift 컴파일러는 Objective-C에서 가져온 completionHandler 코드를 자동으로 살펴보고 async 대안을 제공해요.

그리고, 여기서 멈추지 않고 많은 Delegate API에서도 completionHandler를 호출하는 구조(콜백 구조)의 메서드가 포함되어 있는데요,
이제 이 부분도 async/await을 사용하면 더 이상 그럴 필요가 없어요.
Delegate 메서드에는 대신 사용할 수 있는 async 대안이 있어요.

```swift
import ClockKit

// completionHandler 구조의 Delegate 메서드를 async/await으로 대체한 코드
extension ComplicationController: CLKComplicationDataSource {
    func currentTimelineEntry(for complication: CLKComplication) async -> CLKComplicationTimelineEntry? {
        let date = Date()
        let thumbnail = try? await self.viewModel.fetchThumbnail(for: post.id)
        guard let thumbnail = thumbnail else {
            return nil
        }

        let entry = self.createTimelineEntry(for: thumbnail, date: date)
        return entry
    }
}
```

> ! 비동기 함수는 호출 결과가 직접 반환되지 않을 때, 통신하는 "get"과 같은 선행 단어를 생략하는 것이 좋기 때문에, async 대체 메서드에서는 "get"을 선행으로 하는 네이밍은 하지 않는 것이 좋다고 해요. 해요.

# Async alternatives and continuations

async/await을 사용하는 방법으로 대체 가능한 것이 있지만,
필연적으로 스스로 비동기 대체 코드를 작성해야하는 경우도 있어요.
이런 경우에는 어떻게 해야하는지 예제를 통해서 살펴보자구요 !

## Example

앱에 Core Data 저장소에 보관된 모든 게시물을 검색하는 메서드가 있어요.
completionHandler를 호출하는 구조로 되어있고, 이것을 async로 대체하고 싶은 상황이에요.

```swift
// Existing function
func getPersistentPosts(completion: @escaping ([Post], Error?) -> Void) {       
    do {
        let req = Post.fetchRequest()
        req.sortDescriptors = [NSSortDescriptor(key: "date", ascending: true)]
        let asyncRequest = NSAsynchronousFetchRequest<Post>(fetchRequest: req) { result in
            completion(result.finalResult ?? [], nil)
        }
        try self.managedObjectContext.execute(asyncRequest)
    } catch {
        completion([], error)
    }
}
```

1. async 메서드를 만들고, 반환 값을 [Post]로 변환
2. 이 함수는 오류가 발생할 수도 있으므로 throws 키워드가 필요
3. completionHandler 호출 구조의 `getPersistentPosts` 메서드 호출

근데.. 여기서 막혔죠? `getPersistentPosts` 메서드의 결과를 반환할 수 있을까요? 에러는 어떻게 throw 할 수 있을까요..?

게다가 suspension point를 만났기 때문에, 해당 Task는 suspend 되어 있는 상태가 되어 있어서 올바른 시점에 resume을 해주는 것도 중요해요.

앞으로 만들어가려는 결과 코드가 이런 과정과 고려가 필요해요.
이런 패턴은 항상 일어나고, **continuation** 이라는 이름이 붙여져 있어요.

![](https://velog.velcdn.com/images/textobey/post/c684beeb-9875-4035-b483-4aa31ff5076b/image.png)

continuation을 가능하게 하기 위해서 Swift는 작업을 생성, 관리 및 resume 할 수 있는 높은 수준의 안전한 기능을 제공해요.

```swift
// Async alternative
func persistentPosts() async throws -> [Post] {       
    typealias PostContinuation = CheckedContinuation<[Post], Error>
    return try await withCheckedThrowingContinuation { (continuation: PostContinuation) in
        self.getPersistentPosts { posts, error in
            if let error = error { 
                continuation.resume(throwing: error) 
            } else {
                continuation.resume(returning: posts)
            }
        }
    }
}
```

`withCheckedThrowingContinuation` 메서드 :

1. 오류가 발생할 수 있는 completion blocks을 비동기 함수까지 lift 할 수 있어요.
2. 일시 중단된 비동기 함수를 다시 시작하는 데 사용할 수 있는 continuation에 대한 접근 권한을 얻는 방법이에요.
3. `getPersistentPosts` 호출의 결과를 기다릴 수 있도록 만들어줘서 bridge 구축의 첫번째가 돼요.
4. continuation 값은 completionHandler의 결과를 재개하는 함수 `continuation.resume(throwing:)`, `continuation.resume(returning:)`를 제공해요.
5. `getPersistentPosts` 의 결과를 기다리는 모든 호출의 일시 중단을 해제하는 데 필요한 missing link를 제공해요. (스레드의 제어권을 얻기 위한 일련의 과정을 의미하는 거 같은데.. 확실한지는 모르겠어요)

이 과정을 통해 completionHandler 구조에서 async 함수로의 bridge가 깔끔하게 완성돼요.
continuation은 스스로 비동기 대체 코드를 작성할 수 있는 강력한 기능인것이죠.

> 명심해야할 주의점!

1. resume은 모든 경로에서 정확히 한번만 호출해야 함
2. 누락하는 것은 Swift 컴파일러가 경고를 통해 알려주고, 여러번 호출하는 것은 프로그램 데이터가 손상될 수 있기에 치명적인 에러로 표시

## Other Example

Swift에는 많은 API가 이벤트(사용자 상호작용?)기반이고, 그 이벤트에 따라서 적절한 시점에 애플리케이션에 알려주는 구조로 되어있는데요,
이때 이 구조는 Delegate 콜백을 사용하여 제공하고 있죠.

이때 아래의 에제처럼 continuation을 변수로 두고,
콜백 구조에서 async 함수로의 bridge를 이룰 수 있어요.

```swift
class ViewController: UIViewController {
    private var activeContinuation: CheckedContinuation<[Post], Error>?
    func sharedPostsFromPeer() async throws -> [Post] {
        try await withCheckedThrowingContinuation { continuation in
            self.activeContinuation = continuation
            self.peerManager.syncSharedPosts()
        }
    }
}

extension ViewController: PeerSyncDelegate {
    func peerManager(_ manager: PeerManager, received posts: [Post]) {
        self.activeContinuation?.resume(returning: posts)
        self.activeContinuation = nil // guard against multiple calls to resume
    }

    func peerManager(_ manager: PeerManager, hadError error: Error) {
        self.activeContinuation?.resume(throwing: error)
        self.activeContinuation = nil // guard against multiple calls to resume
    }
}
```

---

# 마무리

이것으로 [Meet async/await in Swift](https://developer.apple.com/videos/play/wwdc2021/10132/) 영상 내용을 나름대로 요약하고, 정리해보았는데요.
WWDC21 내용이다보니, 한국어 번역이 없어서 의역이나.. 제 나름으로 이해한대로 내용을 적었기 때문에

제가 의역하고 정리한 내용이 잘못됐을 수 있기 때문에 이 내용을 무조건 절대적이다! 라고 생각하지 않고 어느정도만 감을 잡는 정도로만 보셨으면 좋겠어요 :)
잘못된 부분을 알게되면 그때마다 수정을 통해서 좀더 완성도 있는 포스팅으로 깎아?나가 볼테니 너그럽게 봐주시면 좋을거 같아요. 

관련된 WWDC 영상들이 정말 많아서, 언제 다 차근차근 이해하며 볼 수 있을지는 모르겠지만
또 잘 정리해서 공유하고 싶은 내용이 있다면 준비해오도록 할게요 ~

감사합니다.
