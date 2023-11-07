---
title: "RxSwift에서 메모리 누수를 추적하는 방법"
excerpt: "RxSwift에 특화된 메모리 누수 추적법?"

categories:
  - RxSwift
tags:
  - [RxSwift, iOS]

permalink: /rx-swift/tracking_memory_leak_on_rxswift/

toc: true
toc_sticky: true

date: 2023-11-07
last_modified_at: 2023-11-07
---


안녕하세요 :)

이번 포스팅에서는 RxSwift에 관련된 메모리 누수를 추적하는 방법과 해결 방법에 대해 작성해보려고 하는데요,  
순서가 조금 뒤바뀌긴 했지만.. 이번 포스팅과 저번 [Singleton과 DisposeBag](https://textobey.github.io/rx-swift/disposebag_on_singleton/) 포스팅을 함께 보신다면  
꽤 재밌는 내용이지 않을까 싶어요 :)

## 일반적인 추적 방법

메모리누수를 추적하는 방법으로 가장 쉽고 간단하게 확인해볼 수 있는것이 바로 **Debug Navigator(Command + 7)**이지 않을까 생각하는데요!

앱을 실행하고, 메모리 누수가 의심되는 부분 혹은 앱의 전체적 화면/기능을 한번씩 수행하였을때  
메모리 리소스 차지가 잘 해제 되는지 혹은 아래 그림처럼 메모리 사용량이 갈수록 쌓여가는지를 보고  
메모리 누수를 확인하는 방법이에요.

![](https://velog.velcdn.com/images/textobey/post/124719db-308f-48ec-95fb-7f7ca0ac5965/image.png)

물론, 추적 단계로 가려면 좀더 디테일하게 메모리 그래프까지 확인하는등의 방법이 있겠지만,  
쉽고 간단하게 확인해볼 수 있는 일반적인 방법으로 가장 먼저 소개해보았습니다.

## RxSwift에서는?

그렇다면, RxSwift에서의 메모리 누수가 발생하는지는 어떻게 알 수 있을까요?

### 리소스 추적 디버그 활성화

RxSwift에서는 앱 전체의 모든 구독에 대한 현재 리소스 수를 계산하는 자체 내부 매커니즘을 제공하고 있어요.
> 그 전에 [Enabling Debug Mode](https://github.com/ReactiveX/RxSwift/blob/c6c0c540109678b96639c25e9c0ebe4a6d7a69a9/Documentation/GettingStarted.md#enabling-debug-mode) 를 참고하여, 디버그 모드를 활성화 해주세요!
<span style="color:gray">+) Pod을 통한 활성화 방법 소개는 많은데, SPM은 어떻게 해야하는지 잘모르겠네요,,</span>

```swift
    /* add somewhere in
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey : Any]? = nil)
    */
	_ = Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
    	.subscribe(onNext: { _ in
        	print("Resource count \(RxSwift.Resources.total)")
    	})
```


앱의 각 기능과 화면을 사용하거나 진입하기 이전인 초기 상태(화면)의 **초기 리소스**와
 앱의 각 기능과 화면을 모두 사용하고, 진입 -> 이탈 이후인 **최종 리소스를** 비교하여
그 수의 차이가 있을 경우에는 어딘가에 메모리 누수가 있을 수 있다는 것을 의미해요.

### Instruments

Instruments를 활용하면 앱의 다양한 퍼포먼스 성능을 체크해볼 수 있습니다.
제가 Instruments를 자주 활용하거나 다루는 방법을 잘 아는것은 아니지만, Instruments를 활용하면 메모리 누수를 추적할 수 있어요!

먼저 **Debug Navigator(Command + 7)**에서 **Profile Instruments**를 눌러 Instruments를 실행해주세요.  

![](https://velog.velcdn.com/images/textobey/post/9c2f1e92-bbf5-4667-b6ac-9c0ddf7140ee/image.png)

그 이후에 아래와 같은 코드(혹은 RxSwift에서 권장하지 않는 안티패턴)가 실행되면  
Instruments에서 메모리 누수가 발생하여 `X` 표시로된 Check Point(?)가 발생한 것을  볼 수 있어요.

```swift
// Example
func memoryLeakDisposable() {
    Observable<String>.create({ observer in
        observer.onNext("method: memoryLeakDisposable")
        return Disposables.create()
    }).subscribe(
        onNext: { event in
            print(event)
        },
        onError: { print($0) },
        onCompleted: { print("Completed") },
        onDisposed: { print("Disposed") }
    )
}
```

![](https://velog.velcdn.com/images/textobey/post/7fa91443-bd1d-48b5-bf8a-8b0b22e1e16a/image.png)

예제로 작성된 코드같은(= bad code smell) RxSwift의 사용은  
정말 큰 문제라면 당장에 앱 충돌 문제를 야기하겠지만, 일반적으로는 이렇게 조용히 메모리 누수가 누적돼가면서 어느순간 OS가 앱을 종료시켜버리는 문제가 발생할것이고 그때서야 부랴부랴 원인을 추적해가고 해결하는것은 쉽지 않을것이기 때문에, RxSwift가 권장하는 올바른 코드 컨벤션으로 코드를 작성하고, 메모리 누수 점검을 해보는 습관이 필요하겠네요 :)

## Reference
- [Disposing RxSwift's Memory Leaks](https://medium.com/gett-engineering/disposing-rxswifts-memory-leaks-6ceb73162170)