---
title: "Singleton과 DisposeBag"
excerpt: "Singleton에서 Disposable dispose 시키기"

categories:
  - RxSwift
tags:
  - [iOS, RxSwift]

permalink: /rx-swift/disposebag_on_singleton/

toc: true
toc_sticky: true

date: 2023-10-25
last_modified_at: 2023-10-25
---


안녕하세요!

여러분들은 프로젝트에서 RxSwift를 사용하고 계신가요?  
사용하고 계신다면 Singleton 패턴으로 생성된 객체 또는 앱의 생명주기와 같은 생명주기를 가지는 서비스(모듈)에서 DisposeBag을 어떻게 사용하고 계신가요?

오늘은 과거에 궁금해서 찾아보고, 정리해놓았던 내용을 블로그에 포스팅해볼게요 :)

> 아! 이번 포스팅에서는 저의 주관적인 생각과 고민과 해결방을 적어보았어요.  
 무조건 이것이 맞다 틀리다 보다는 이런건 어떨까요? 라는 느낌으로 재밌게 읽으셨으면 좋겠습니다.

## Disposable 및 DisposeBag

```swift
public func subscribe(
	onNext: ((Element) -> Void)? = nil,
	onError: ((Swift.Error) -> Void)? = nil,
	onCompleted: (() -> Void)? = nil,
	onDisposed: (() -> Void)? = nil
) -> Disposable {
```

`Observable` 객체를 `subscribe` 하게 되면, `Disposable` 이라는 객체를 반환하고,

```swift
/// Represents a disposable resource.
public protocol Disposable {
    /// Dispose resource.
    func dispose()
}
```

`Observable` 에 대한 구독을 더 이상 원하지 않을때(=구독 해제), 바로 이 `Disposable` 객체에 접근하여 dispose() 메서드를 통해 구독을 해제할 수 있어요.

그리고, Observable에 대한 구독 해제를 하나 하나 처리해주어야 하는 비효율성을 해소하기 위해, Disposable을 모아두었다가 자신이 deinit 될 때 일괄적으로 dispose 처리를 해주는 `DisposeBag` 이 있죠.

```swift
extension Disposable {
    /// Adds `self` to `bag`
    ///
    /// - parameter bag: `DisposeBag` to add `self` to.
    public func disposed(by bag: DisposeBag) {
        bag.insert(self)
    }
}
```

## 그렇다면.. Singleton에서는?

`DisposeBag` 은 Bag이라는 단어처럼 가방에 모두 담아두었다가 자신(Object)의 라이프 사이클이 종료될때 일괄적을 처리되는 편리한 구조이지만,

예를 들어, Singleton 객체처럼 메모리에 올려지고나면 앱의 라이프 사이클과 동일하게 가져가지는 경우에는 앱의 종료전까지는 Deinit이 안되기 때문에, `DisposeBag` 에 담아둔 `Disposable` 들은 dispose 처리되지 않아요.

그렇기에, Singleton 객체 또는 라이프 사이클이 앱의 라이프 사이클과 동일한 객체에서 일반적인 방법으로 똑같이 적용해도 되는것인가에 대한 의문이 있었어요.

```swift
class  SingletonClass {
     private let disposeBag = DisposeBag()
     private let dependentObject: DependentClass

    func  calledFrequently () {
        dependentObject.getObservable().subscribe(onNext : {
             // do someting.
        })
        .disposed(by :  self.disposeBag )
         // disposeBag 가 해제되지 않는다
    }
}
```

## 아이디어들

### 1.

이 문제를 해결하기 위해서는 Disposable을 직접 관리해주어야겠다고 생각했어요. 그래서, 아래처럼 `Disposable` 을 별도로 저장해둘 프로퍼티를 선언하고 메서드가 호출될때 우선적으로 dispose() 처리를 하는 방법을 가장 먼저 떠올렸어요.

하지만, 이 방법은 동일한 처리 방법의 메서드가 객체내에 여러개가 존재한다면 기존 구독을 해제하고 새로운 구독을 시작하기에 기존 구독의 대한 결과를 받지 못한채로 구독이 종료될 수 있다는 단점이 있을것으로 보았어요.

```swift
var disposable: Disposable?

func method1() {
    disposable?.dispose()
	disposable = dependentObject.getAsyncSubjectAsObservable().subscribe(onNext : {
		// do someting.
	})
}
```

### 2.

두번째로는 `CompositeDisposable` 을 활용하는것인데요,  
여러개의 `Disposable`을 가지는 컨테이너를 선언해두고, dispose 호출시 각각의 `Disposable` 을 호출하기 때문에 1번에 있었던 단점을 어느정도 보안할 수 있어요.

하지만 이 또한, 지나간 구독과 필요 없어진 구독에 대해 적절한 생명주기 관리가 필요한 방법이기 때문에 무언가 간단하면서도 시원한 해결 방법이라기엔 조금 약한거 같아요.

```swift
let  compositeDisposable = CompositeDisposable()
    
func useCompositeDisposable () {
    let  disposable  = dependentObject.getObservable().subscribe(onNext : {
        // do someting.
        if (conditionalExpression) {
            compositeDisposable.dispose()
        }
    })
    _ = compositeDisposable.insert(diposable)
}
```

```swift
final class CompositeDisposable: Disposable {
    private var isDisposed: Bool = false
    private var disposables: [Disposable] = []

    // dispose가 된 상태라면 disposable를 dispose시키고, 그렇지 않으면 Disposable를 추가.
    func add(disposable: Disposable) {
        guard !isDisposed else {
            disposable.dispose()
            return
        }
        disposables.append(disposable)
    }

    func dispose() {
        guard !isDisposed else { return }
        isDisposed = true
        disposables.forEach { $0.dispose() }
    }
}
```


### 3.

다음으로는 take() 오퍼레이터를 활용하여 Observable에 대한 이벤트를 딱 한번만 발생하도록하여 Observable에 대한 시퀀스의 종료가 확실하게 이뤄지도록 하는 방법인데요,

이렇게 처리하게되면, Disposable 반환값에 대한 생명주기 관리나 기타 처리에 대해 신경을 써주지 않아도 되기 때문에, 1회성 이벤트를 가지는 Observable에 적절히 적용해보는것은 어떨까요?

```swift
func useTake() {
    _ = dependentObject.getObservable().take( 1 ).subscribe(onNext : {
         // do someting.
    })
}

```

## Reference

- [https://blog.cybozu.io/entry/2018/08/15/080000](https://blog.cybozu.io/entry/2018/08/15/080000)