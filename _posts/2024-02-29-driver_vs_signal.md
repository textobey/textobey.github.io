---
title: "Driver vs Signal"
excerpt: "Trait의 특징과 차이점을 알아보자"

categories:
  - RxSwift
tags:
  - [RxSwift, RxCocoa, Trait]

permalink: /rx_swift/driver_vs_signal/

toc: true
toc_sticky: true

date: 2024-02-29
last_modified_at: 2024-02-29
--- 



안녕하세요 👋

이번 포스팅에서는 RxCocoa의 Trait 중 `Driver` 와 `Signal` 은  
각자 어떤 특징을 가지고 있고, 또 어떤 차이점이 있는지에 대해서 개념과 실제 코드를 작성해서 알아볼게요 ~~

## Trait

Observable과 Trait은 유사하지만, Trait은 좀더 특수한 목적이나 케이스를 위해 만들어진 Observable 입니다.

## Driver

그렇다면, `Driver`는 어떤 목적을 위해 만들어진 Observable 일까요?

먼저, RxCocoa에 존재하는 Trait이기 때문에 UI를 위해 만들어진 혹은 UI에 특화된 Observable입니다.

왜냐면, `Driver.swift` 파일의 설명에도 나와있듯이 `Driver`는 아래의 특징을 가지고 있습니다.

- 절대 실패하지 않음
- 이벤트가 `MainScheduler.instance`에서 이뤄지도록 함
- `share(replay: 1, scope: .whileConnected)`를 통해 이벤트를 공유하고, 새로운 구독이 생기면 마지막 element를 1개만큼 공유하고 시작!

> UI는 계속 표시되어야하고(실패했다고 사라지거나 해서는 안되겠죠?)
메인스레드에서 업데이트 되어야하기 때문에 이를 고려해봤을때
왜 UI에 특화된 Observable인지 느낌이 확 오네요.

그리고, Observable event에 대한 구독은`subscribe` 가 아닌,
`drive(onNext: _)`를 통해 구현합니다.

```swift
driverInterval
    .drive(onNext: { count in
        print("Origin Driver Count is: \(count)")
    })
    .disposed(by: disposeBag)
```


## Signal

`Driver` 에 대해 이해가 잘 되었다면, `Signal` 의 이해도 어렵지 않습니다!!
왜냐면, 둘의 차이점은 한 가지 밖에 없기 때문인데요 ㅎㅎ

- 절대 실패하지 않음
- 이벤트가 `MainScheduler.instance`에서 이뤄지도록 함
- `share(scope: .whileConnected)`를 통해 이벤트를 공유

그리고, Observable event에 대한 구독은`subscribe` 가 아닌,
`emit(onNext: _)`를 통해 구현합니다.

```swift
signalInterval
    .emit(onNext: { count in
        print("Origin Signal Count is: \(count)")
    })
    .disposed(by: disposeBag)
```


## 차이점

혹시, 정리된 특징에서 차이점을 찾으셨나요?

이 두 개 Trait의 차이점은 바로!
마지막 element를 새로운 구독자에게 공유하는지 안하는지에 있습니다.

마지막 element를 공유하는가? Driver ✅
마지막 element를 공유하는가? Signal ❌


### 코드

초기화 시점에 3초마다 숫자가 증가하는 interval Observable을  
Driver와 Signal 모두 생성하고 구독까지 해놓은 상태입니다.

그렇다면, 콘솔창에는 Driver와 Signal 구독에 따른 로그가 찍히고 있겠죠?

Origin Driver Count is: 1 -> 2 -> 3 -> 4
Origin Signal Count is: 1 -> 2 -> 3 -> 4

이때, 4까지 카운트 된 상황에서 Driver와 Signal에 새로운 구독이 추가되면 어떻게 될까요?

Driver: 마지막 element인 4를 출력하고, 3초가 지나고 다음 element 5를 출력
Singal: 3초가 지나고, 다음 element 5를 출력

![](https://velog.velcdn.com/images/textobey/post/5f75054f-6ec5-4994-8b83-73574bbe388c/image.png)


## 정리하며

각 Trait의 특징과 코드를 통한 차이점 분석까지 해보았는데요!
그럼, 각 Trait은 어떤 상황에서 쓰는것이 더 적합할까요? 🤔

- Driver는 마지막 element를 받고서 시작하기 때문에,
지속적으로 변하는 상태를 반영하는데에 더 적합한 Trait.

- Signal은 한 번만 발생하는 이벤트를 처리하는데 더 적합한 Trait.

전체 코드는 제 [깃허브 레포지토리](https://github.com/textobey/UI-Components/blob/main/UIKit-Components/MyFoundation/ViewControllers/RxCocoaTraits/RxCocoaTraitsViewController.swift)에 있습니다.
읽어주셔서 감사합니다. 😊

