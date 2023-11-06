---
title: "MVVM-Delegates is not MVVM"
excerpt: "MVVM Delegate 패턴은 MVVM이 아니다?"

categories:
  - Architecture
tags:
  - [Architecture, iOS]

permalink: /architecture/mvvm_delegates_is_not_mvvm/

toc: true
toc_sticky: true

date: 2023-11-06
last_modified_at: 2023-11-06
---


MVVM 아키텍처 패턴은 아키텍처 패턴중 iOS에서 꽤 많은 채택을 받고 있어요.
그리고, 저는 MVVM 아키텍처 패턴에서는 View와 ViewModel간의 바인딩을 핵심 매커니즘이라고 생각하고 있어요 :)

MVVM에서 RxSwift를 이용한 데이터 바인딩 방식 이외에도
Delegate Pattern, Closure, KVO, NotificationCenter, Combine 등
다양한 방법이 존재하는데요.

오늘은 MVVM 데이터 바인딩을 Delegate Pattern으로 했을때,
MVVM의 조건을 충족할 수 없다는 흥미로운 글을 읽게 되어서
원문과 함께 글쓴이의 근거를 같이 한번 살펴볼게요!

## 원문

[mvvm-delegates is not MVVM, actually that is MVP](https://github.com/tailec/ios-architecture/issues/4)

Github에 iOS 아키텍처를 다루는 레포지토리에서 marty-suzuki 님께서 이슈를 등록하면서 시작되었는데요.
(아래 코드는 제가 원문 코드를 포스팅에 맞게 수정한 내용이에요!)


### 코드

```swift
protocol CounterViewDelegate {
    func didChangeCount(_ presenter: CounterPresenter, count: Int)
}

class CounterViewController: UIViewController, CounterViewDelegate {
    let viewModel: CounterViewModel
    let countLabel: UILabel

    override func viewDidLoad() {
        super.viewDidLoad()

        viewModel.delegate = self
    }

    func countUp() {
        viewModel.countUp()
    }
}

extension CounterViewController: CounterView {
    func didChangeCount(_ presenter: CounterPresenter, count: Int) {
        countLabel?.text = "\(count)"
    }
}

class CounterViewModel {

    weak var delegate: CounterViewDelegate?

    private(set) var count: Int = 0 {
        didSet {
             delegate?.didChangeCount(self, count: count)
        }
    }

    func countUp() {
        count += 1
    }
}
```

### 주장

![](https://velog.velcdn.com/images/textobey/post/c4fdf2c5-2a10-4579-8db5-444dfb368443/image.png)
[MVVM 다이어그램](https://wojciechkulik.pl/ios/mvvm-coordinators-rxswift-and-sample-ios-application-with-authenticatio)

내용은 이러해요, 해당 코드에서 View와 ViewModel은 Delegate Pattern에 의해 바인딩이 이루어진 것을 볼 수 있는데요.

이는 ViewModel의 규칙 중 **ViewModel은 View(또는 ViewController)에 의존하지 않아야 한다.** 는 규칙을 어긴것이라고 주장하고 있어요.

### 근거

어째서 위의 코드가 ViewModel이 View에 의존하는 코드가 되는것일까요?

바로, **MVVM Delegate 매커니즘은 ViewModel의 delegate를 View에서 위임**하고 있고, 이로 인해 **ViewModel은 View에 어떤 메서드가 있는지 알고 있음을 의미하기 때문**이라고 해요.

그리고, MVVM의 Notify 매커니즘은 알림을 받는 개체를 고려하지 않고 알림을 보내야 하지만
Delegate Pattern으로 인해 알림을 받는 개체를 고려하는 상태가 되는것이죠. (이는 View 추상화 한다고 해도 동일)

### 기타

그럼 나머지 바인딩 방식은 어떻게 되는 걸까요?

`Observables`, `NotificationCenter`, `Closures`, `KVO` 는 MVVM의 조건을 만족한다고 하네요.

### 정리

이번에 써드파티 없이 Swift API만을 사용하는 것을 조건으로 하여 프로젝트를 진행한 것이 있는데요, 그 과정에서 MVVM 아키텍처에서 RxSwift를 사용하지 못하는 상황이니, 데이터 바인딩 방식을 어떤 것을 채택할지 그 방식과 장단점에 대해 알아보다 찾게된 흥미로운 글이었네요 :)

그 중 저는 `Closures` 를 채택하여 진행하였는데요, 조만간 저의 [ios-architectures](https://github.com/textobey/ios-architectures) 레포지토리에 업로드 할 수 있도록 해볼게요~

감사합니다.