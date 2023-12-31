---
title: "Relay Stream의 에러 이벤트를 처리하는 방법"
excerpt: "Error를 Relay에 바인딩하면 어떻게될까"

categories:
  - RxSwift
tags:
  - [RxSwift]

permalink: /rx-swift/binding_error_to_relay/

toc: true
toc_sticky: true

date: 2023-12-28
last_modified_at: 2023-12-28
--- 



안녕하세요~~.  
연말이다 뭐다해서..오랜만에 글을 작성하는거 같네요 ㅎㅎ..

그럼 거두절미하고~ 이번 포스팅 주제는  
**에러를 Relay(PublishRelay, BehaviorRelay)에 바인딩하면 무슨일이 벌어질까?** 입니다!
본 내용에 들어가기 전 한번씩 무슨일이 벌어질까 예상해보는 것도 재밌겠네요

## Relay?

먼저, RxSwift의 Relay의 특징이 무엇인지 간단하게 리마인드 해볼게요 :)

Relay는 Subject를 래핑한것으로 Subject의 그 기능을 그대로 가져오되  
onCompleted, onError 이벤트에 의해 스트림이 종료되지 않는 특징이 있죠!
(emit 시에도 onNext/onCompleted/onError가 아닌 accept() 단일인 이유)

이러한 특징으로인해 onCompleted, onError 관계없이 쭉 화면에 그려져야 하는 
UI에 활용할때 유용해요.

### Error를 Relay에게

그렇다면, accept()가 아닌 onNext, onCompleted, onError 이벤트가 발생하는  
Observable 혹은 Subject 등을 Relay에 바인딩하게 되면 어떻게 될까요?  

onNext, onCompleted 는 그렇다고 치고.. onError는 문제가 될것같지 않나요??
정확한 정답은 아래 bind 메서드에서 확인해봅시다

```swift
// Observable+Bind.swift

    /**
     Creates new subscription and sends elements to publish relay(s).
     In case error occurs in debug mode, `fatalError` will be raised.
     In case error occurs in release mode, `error` will be logged.
     - parameter relays: Target publish relays for sequence elements.
     - returns: Disposable object that can be used to unsubscribe the observer.
     */
    private func bind(to relays: [PublishRelay<Element>]) -> Disposable {
        subscribe { e in
            switch e {
            case let .next(element):
                relays.forEach {
                    $0.accept(element)
                }
            case let .error(error):
                rxFatalErrorInDebug("Binding error to publish relay: \(error)")
            case .completed:
                break
            }
        }
    }

```

주석중에 주목해야하는 부분은  
**In case error occurs in debug mode, **`fatalError` **will be raised.**  
디버그 모드라면 fatalError가 발생할 것이고

**In case error occurs in release mode,** `error` **will be logged.**
릴리즈 모드라면 error에 대한 로그가 남을것이라고 되어있네요.


### 샘플코드

```swift
func test_WhenErrorBindingAtRelayWithCatch() {
    let expectation = XCTestExpectation(description: "test_WhenErrorBindingAtRelayWithCatch")
    
    somePublishRelay
        .subscribe(onNext: { element in
            print(element)
        })
        .disposed(by: disposeBag)
    
    let observable = Observable<Int>.create { observer in
        observer.onNext(1)
        observer.onNext(2)
        observer.onNext(3)
        observer.onError(TestError.disconnected)
        return Disposables.create()
    }
    
    observable
        // 아래 catch 오퍼레이터를 주석처리하면 fatalError를 확인할 수 있음
        .catch { error in
            print("❎ \(#function) error: \(error)")
            XCTAssertEqual(error as? TestError, TestError.disconnected)
            expectation.fulfill()
            return Observable.empty()
        }
        .bind(to: somePublishRelay)
        .disposed(by: disposeBag)
    
    wait(for: [expectation], timeout: 3)
}
```

Github: [https://github.com/textobey/ios-architectures/blob/main/Example_UIKit/Example_UIKitTests/ErrorBindToRelayTests.swift](https://github.com/textobey/ios-architectures/blob/main/Example_UIKit/Example_UIKitTests/ErrorBindToRelayTests.swift)

### 정리

이 내용을 정리하면 어떤 결과로 이어질 수 있을까요?  
bind 메서드 내부적으로는 실제 릴리즈 환경에서는 Relay에 Error가  
흘러가지 않도록 구현이 되어있지만  

onCompleted, onError 등 스트림을 종료시킬 수 있는 Observable을  
Relay에 바인딩하는 것은 Relay의 성격과는 맞지 않다고 생각합니다.  

또 한편으론 bind 메서드는 반응형 프로그래밍에 아주 강력한 기능이고,
에러 핸들링이 가능한 다양한 오퍼레이터 또한 존재하기 때문에  
무작정 완전히 사용을 피해야 하는건가? 라고 하면 또 잘 모르겠네요..  
