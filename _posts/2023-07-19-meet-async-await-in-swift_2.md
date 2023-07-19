---
title: "Meet async/await in Swift(2)"
excerpt: "Swift Concurrency로 내딛는 첫걸음"

categories:
  - iOS
tags:
  - [iOS, WWDC, Concurrency]

permalink: /ios/meet-async-await-in-swift_2/

toc: true
toc_sticky: true

date: 2023-07-19
last_modified_at: 2023-07-19
---

![](https://velog.velcdn.com/images/textobey/post/2d3a91c2-1a86-4094-a9e7-fcad75204f86/image.png)

저번 [Meet async/await in Swift(1)](https://textobey.github.io/ios/meet-async-await-in-swift_1/)편에서는 비동기 함수와 completionHandler의 문제점들을 살펴보았죠?

그리고 그 문제점들을 개선선하기 위한 기술과 노력으로는 무엇이 있었는지 알아보고
이 기술과 노력으로도 시원하게 긁어줄 수 없었던 아쉬운 부분을 시~원하게 긁어줄 수 있는 Async/await이 무엇인지 이번 포스팅에서 확인해보도록 해요 :)

# Async function

`fetchThumbnail` 메서드를 가지고 async/await을 사용하지 않는 경우와
사용하는 경우를 가지고 문법적으로 어떤 차이점을 가지고 있는지 확인해볼게요.

```swift
// non-async/await
func fetchThumbnail(for id: String, completion: @escaping (UIImage?, Error?) -> Void)

// async/await
func fetchThumbnail(for id: String) async -> UIImage
```

async/await에서는 함수 선언 시점에 completionHandler 대신에 `async` 이라는 키워드를 사용해요. 이 `async` 키워드를 통해 함수와 signature(문법?)이 simpler 해지는 장점이 있는 것을 확인할 수 있어요.

그리고, 비동기 함수를 활용하여 Data/Response만이 아니라 Error가 발생할 수 있는 상황도 있는데요. 이때는 `throws` 키워드를 추가해주어야 해요.

```swift
// async 키워드는 함수가 비동기 함수임을 나타냄
// throws 키워드는 Error가 throw 될 수 있음을 나타냄
func fetchThumbnail(for id: String) async throws -> UIImage 
```

## Migration

```swift
func fetchThumbnail(for id: String) async throws -> UIImage {
	// 1.
    let request = thumbnailURLRequest(for: id)  
    // 2, 3.
    let (data, response) = try await URLSession.shared.data(for: request)
    // 4.
    guard (response as? HTTPURLResponse)?.statusCode == 200 else { throw FetchError.badID }
    // 5.
    let maybeImage = UIImage(data: data)
    // 6.
    guard let thumbnail = await maybeImage?.thumbnail else { throw FetchError.badImage }
   	// 7.
    return thumbnail
}
```

`fetchThumbnail` 메서드를 async/await으로 마이그레이션한
전체 코드를 한 줄씩 차근차근 어떻게 수행되는지 살펴볼게요 !

### 1.
`thumbnailURLRequest(for: id)` 동기 메서드 수행

### 2.
`URLSession.shared.data(for: request)` 를 호출하여 데이터 다운로드를 시작.

completionHandler 구조의 메서드에서는 오류가 발생하면 명시적으로
completionHandler를 호출하여 이를 알려야 했는데,
awaitable 메서드인 data(for: request) 메서드는 이를 `try` 키워드로 압축해버림

### 3.

`throws`로 표시된 메서드를 호출하려면 `try` 키워드가 가 필요한 것처럼
`async`로 표시된 함수를 호출하려면 `await` 키워드가 필요함
-> 합쳐서 **try await** 키워드로 표시됨

### 4.

에러가 발생한다면 throw 될것이고, 에러가 발생하지 않는다면 데이터 또는 응답값을 받을 수 있음

### 5.
`UIImage(data: data)` 동기 메서드 수행

### 6.

completionHandler 구조의 메서드와는 다르게 guard 문을 사용할때도,
silence하게 에러 없이 지나갈 수 없음. Error를 throw 해야함
> `maybeImage?.thumbnail`는 비동기 함수도 아니고 프로퍼티인데 `await` 키워드를 사용할 수 있는 이유는 뭘까요?
> UIImage Extension으로 추가한 비동기 프로퍼티 이기 때문에 !
> `async`는 함수에만 한정된 것이 아니에요.
> 단, 프로퍼티의 경우 읽기 전용 프로퍼티만 비동기일 수 있어요.
> ![](https://velog.velcdn.com/images/textobey/post/43942f83-fabb-4e9d-8bc5-3cc7fbf37d19/image.png)


### 7.

이 모든것이 정상적으로 진행하면 thumbnail을 일반 함수가 Finishing되는
구조로 똑같이 반환하는 것이 가능

> `dataTask` 메서드와 마찬가지로 이는 Foundation 프레임워크에서 제공되는 비동기식.
> dataTask와는 달리 이 메서드는 awaitable임
> 따라서 호출된 후 스레드를 차단 해제하면서 빠르게 suspend 됨.
> 그러면 스레드는 자유롭게 다른 작업을 수행하는 것이 가능해짐
> 왜 이렇게 해야하냐?에 대한 내용이 부연 설명 되어야할듯.


이게 async/await을 사용하여 작성해야하는 **필요한 코드의 전부**예요.
20줄의 코드에서 6줄의 코드로 줄어 들었고, straight-line code가 되었으며,
completionHandler가 하던 역할을 완벽히 대체할 수 있어요.

이로써, completionHandler를 호출하는 구조의 메서드가 가졌던 문제점들을 개선 완료 :)

1. ~~휴먼 에러에 의해 모든 상황을 caller에게 알리지 않아 버그를 야기 할 수 있음~~ **해결!**
2. ~~코드가 이해하기 쉬우며 우리의 의도를 불분명하게 만들음~~ **해결!**
3. ~~콜백 지옥~~ **해결!**

---

## awaitable

awaitable 키워드는 호출된 후 스레드를 차단 해제하면서 빠르게 suspend 돼요.
async는 Task가 수행되는 도중에 다른 작업을 수행하는 것이 가능하죠?

awaitable 키워드는 Task가 suspend(일시정지) 될 수 있음을 의미하는데
비동기 함수가 일시 중단된다는 것은 무슨 의미일까요?
이 부분은 `async` 키워드 함수를 호출할 때 무슨일이 일어나는지 살펴보면서 알아보도록 해요.

### case. non-async

![](https://velog.velcdn.com/images/textobey/post/0c2e7ec6-86b8-47b9-bd2f-49f0b573f9c9/image.png)

non-async의 경우에는 `thumbnailURLRequest` Task를 수행하는 동안
스레드의 사용을 점유하게 돼요.( 영상 내용대로 말하자면 스레드를 차단한다고 하더라구요. )
그렇게 되면, 스레드는 데이터 다운로드, 이미지 렌더링 등 비싼 비용이 들어가는 작업이 완료될때까지 다른 Task를 수행할 수 없어요.
**non-async의 경우**에는 이런 스레드의 완전 점유를 끝낼 수 있는 방법은 작업된 값이 반환되거나 오류를 발생시키는 작업의 **완료(Finishing) 단계가 되는것 외에는 없어**요.


### case. async(awaitable)

![](https://velog.velcdn.com/images/textobey/post/6cfbe545-85e1-4a51-97f6-3366bb5e2f1b/image.png)

async(awaitable)의 경우 일반 함수와는 다르게 **일시 중단(suspend)라는 개념을 통해**
완전히 다른 방식으로 **스레드의 점유를 넘겨 줄 수 있어**요(포기 할 수 있음).
일반 함수와 마찬가지로 비동기 함수를 호출하면 스레드에 대한 점유(제어권)이 부여되지만
await 키워드의 메서드를 만나게 되면 스레드에 대한 점유를 포기하고, 스레드에 대한 점유를 시스템에게 제공하게 돼요.
(이때, 그냥 넘겨버리는 것이 아니라 스레드에 대한 제어권을 넘겨준 await 메서드의 수행을 약속(예약하고) 넘겨줘요.)
> await 키워드를 만날 경우 무조건 suspend 된다고 여겨질 수 있을거 같은데요,
> await 키워드를 만날때가 아니라 suspension point를 만나게 되면 suspend 돼요.
> suspension point는 무엇이 있는지, 어떻게 만들 수 있는지 나중에 또 알아볼게요!
> (그렇지만, 그냥 넘겨버리는 것이 아니라 await 키워드 메서드를 수행해줄 것을 예약함)

스레드의 제어권을 시스템에게 넘겨주고 suspend 하는 이유는
시스템은 할 일이 많고, 현 시점에서 어떤 Task가 수행되는것이 가장 중요한지 결정하고 진행할 수 있기 때문이에요.

이렇게 함수가 suspend 되면 시스템은 자유롭게 스레드를 사용하여 다른 Task를 수행할 수 있겠죠?
그렇게 스레드에 대한 제어권을 받은 시스템이 어느 순간에는 suspend된 await 메서드가 수행되는 것이 가장 중요한 작업이라고 판단하면
그때, 비동기 함수는 시스템으로부터 스레드를 사용하여 Task를 수행할 수 있게 되고 suspend된 Task를 resume해요.

그리고, 시스템은 원한다면 다시 일시 정지 할 수 있고, 실제로 여러번 중지될 수 있어요.
단, 비동기 함수는 일시적으로 중단될 수 있지만 무조건 일시정지 되어야 하는 것은 아니에요.

이 과정으로 작업이 완료되면 값 또는 오류와 함께 스레드 제어권을 함수에 넘겨요.
이때, 이때 알아야 하는 것은 함수가 suspend 됐을 동안에 스레드는 다른 Task를 수행할 수 있기 때문에 그 동안 애플리케이션의 상태가 극적으로 바뀌어 있을 수 있다는것을 알아야 한다고 해요.

함수가 suspend 된 동안 다른 작업을 수행할 수 있다는 사실 때문에
Swift는 await 키워드로 비동기 호출임을 표시 해야(알려야) 한다고 해요.

시스템이 어느 순간에는 suspend된 await 메서드가 수행되는 것이 가장 중요한 작업이라고 판단하여 함수가 스레드를 사용하여 Task를 수행할 수 있도록 해주는 시점에(아래 코드의 //this 시점)

이 스레드는 suspend 되어 제어권을 넘긴 스레드가 아닌 완전히 다른 스레드에서 수행될 수 있음을 알아야 해요.
그리고, await 메서드 행 사이에서 suspend된 동안 다른 일이 발생할 수 있음

```swift
func fetchThumbnail(for id: String) async throws -> UIImage {
    let request = thumbnailURLRequest(for: id)  
    let (data, response) = try await URLSession.shared.data(for: request) // this
    guard (response as? HTTPURLResponse)?.statusCode == 200 else { throw FetchError.badID }
    let maybeImage = UIImage(data: data)
    guard let thumbnail = await maybeImage?.thumbnail else { throw FetchError.badImage } // this
    return thumbnail
}
```
---

### additional

1. await 키워드의 메서드는 일시 중지될 수 있어요. 그래서, 메서드가 자신을 suspend하면 caller도 suspend 돼요. 따라서, caller 또한 비동기여야 해요.
2. async 함수에서 한번 또는 여러 번 일시 중단될 수 있는 위치를 지정하기 위해 await 키워드가 사용돼요.
3. async 함수가 일시 중단되는 동안 스레드가 차단 되지 않아요. 따라서, 시스템은 다른 작업을 자유롭게 예약(수행) 할 수 있어요.
4. async 함수가 다시 시작되면 호출한 async 함수에서 반환된 결과가 원래 함수로 다시 흐르고, 중단된 지점부터 실행이 계속돼요.

## 마무리

이렇게 async/await이 무엇인지, 어떻게 작성하고, 어떻게 동작하게 되는지 과정을 살펴보았는데요 !
다음 포스팅에서는 마지막으로 우리의 애플리케이션 코드를 어떻게 async/await으로 마이그레이션하고, 사용할 수 있을지 작성해보도록 할게요 :)
