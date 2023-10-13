---
title: "OptionSet"
excerpt: "Enum인줄 알았던 그거 OptionSet이야"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/ㅐ/

toc: true
toc_sticky: true

date: 2023-10-13
last_modified_at: 2023-10-13
---

# OptionSet

![](https://velog.velcdn.com/images/textobey/post/f7cf56a9-d62e-4855-a288-b118e81c350c/image.png)

이번 포스팅에서는 OptionSet에 대한 내용을 다뤄보려고 해요!
OptionSet.. 어떻게 설명하면 좋을지 고민을 해봤는데요,
Option과 Set으로 나누고 각각의 단어가 무엇을 의미하는지 설명하면 간단할것 같은데? 라는 개인적인 생각이 들어서, 이렇게 한번 나눠볼게요 :)

- Option
  OptionSet은 비트마스크 방식으로 각 비트별로 하나의 옵션을 나타낼 수 있다는 특징이 있는데요, OptionSet의 Option은 말그대로 타입/선택지라고 받아들일 수 있을거 같아요.
  
  > 비트마스크: 이진수를 사용하는 컴퓨터의 연산 방식을 활용하여, 정수의 이진수 표현을 자료구조로 쓰는 기법

- Set
  OptionSet의 Set은 자료구조의 종류인 바로 그 Set이에요!
  그렇기 때문에, OptionSet은 Set 자료구형의 특성을 가지고 있어요.
  Apple 문서를 보아도 OptionSet은 Collections로 분류가 되어있어요.

이렇게 Option과 Set이 합쳐진게 `OptionSet` 이라는 **프로토콜**이에요.
이제, 코드를 통해서 이해를 좀더 높여보면 좋을거 같아요.

## Code

```swift
struct ShippingOptions: OptionSet {
    let rawValue: Int

    static let nextDay    = ShippingOptions(rawValue: 1 << 0)
    static let secondDay  = ShippingOptions(rawValue: 1 << 1)
    static let priority   = ShippingOptions(rawValue: 1 << 2)
    static let standard   = ShippingOptions(rawValue: 1 << 3)

    static let express: ShippingOptions = [.nextDay, .secondDay]
    static let all: ShippingOptions = [.express, .priority, .standard]
}
```

Apple에서 제공해준 샘플 코드를 확인해보면, ShippingOptions 구조체는 OptionSet 프로토콜을 채택하고 각 비트별로 옵션을 선언해두었어요.

- `nextDay` 1 << 0 : nextDay는 1을 0번 좌측 시프트 연산하기 때문에, 0001이고 2진법으로 연산을하면 1이라는 값을 가지는 옵션이 됨

- `secondDay` 1 << 1 : nextDay는 1을 1번 좌측 시프트 연산하기 때문에, 0010이고 2진법으로 연산을하면 2라는 값을 가지는 옵션이 됨

- `priority` 1 << 2 : nextDay는 1을 2번 좌측 시프트 연산하기 때문에, 0100이고 2진법으로 연산을하면 4라는 값을 가지는 옵션이 됨

- `standard` 1 << 3 : nextDay는 1을 3번 좌측 시프트 연산하기 때문에, 1000이고 2진법으로 연산을하면 8이라는 값을 가지는 옵션이 됨

그렇다면, `express`와 `all`은 어떻게 계산하면 되는걸까요?

`express`는 [0001, 0010] 값을 가지고 있고, 그렇기 때문에 0011(10진수 3)이라는 값을 가지는 옵션이 되고, `all`은 [0011, 0100, 1000] 값을 가지고 있고, 그렇기 때문에 1111(10진수 15)라는 값을 가지는 옵션이 되는거죠!

## 활용

```swift
// 1번째 예시
let singleOption: ShippingOptions = .priority
let multipleOptions: ShippingOptions = [.nextDay, .secondDay, .priority]
let noOptions: ShippingOptions = []

singleOption.rawValue      // 4
multipleOptions.rawValue   // 7
noOptions.rawValue         // 0

// 2번째 예시
let purchasePrice = 87.55

var freeOptions: ShippingOptions = []
if purchasePrice > 50 {
    freeOptions.insert(.priority)
}

if freeOptions.contains(.priority) {
    print("You've earned free priority shipping!")
} else {
    print("Add more to your cart for free priority shipping!")
}
```

위의 2가지 예시는 OptionSet의 Set 자료구조의 특성을 가지고 있다는 것을 확 체감할 수 있는데요,

첫번째 예시에서는 싱글옵션, 멀티옵션, 노옵션인 경우에도 타입에 T 또는 [T]의 형식으로 프로퍼티를 선언하지 않아도 된다는 장점을 볼 수 있어요.

두번째 예시에서는 Set 자료구조의 insert, contains와 같은 메서드(연산)를 수행할 수 있다는 장점을 볼 수 있어요. 물론, insert, contains 이외에도 OptionSet은 union, intersection 등 집합 연산자도 사용할 수 있어요.

> ! OptionSet은 아래 SetAlgebra 프로토콜을 채택하고 있기 때문에, 집합연산자를 사용하는것이 가능해져요. 
> ![](https://velog.velcdn.com/images/textobey/post/48481ae9-0d25-4a05-8fcd-96f1ec8fc855/image.png)

## Enum vs OptionSet

그렇다면, 여기서 궁금증!
`Enum`과 `OptionSet`은 각각 어느 상황에서 사용하면 좋을지, 상황별로 어떤 강점을 가지고 있을지가 궁금해졌어요.

저의 나름으로 생각해본 결론은 이래요 :)

**OptionSet과 Enum은 둘 다 제공되어있는 선택지를 선택(표현)하는데 사용**되는 타입이라는 것이 공통점이고,

아래의 열거형에서 사람의 상태는 자고있거나, 일어나있거나 하나의 상태만이 될 수 있어요.
동시에 자고있거나 일어나있는 상황은 될 수 없죠.

```swift
enum HumanState {
    case sleep
    case awake
}
```

반면에, OptionSet은 비트마스크 기반 Set 기반으로 여러 개의 옵션을 동시에 가질 수 있어요.

```swift
public struct CACornerMask : OptionSet, @unchecked Sendable {

    public init(rawValue: UInt)

    public static var layerMinXMinYCorner: CACornerMask { get }
    public static var layerMaxXMinYCorner: CACornerMask { get }
    public static var layerMinXMaxYCorner: CACornerMask { get }
    public static var layerMaxXMaxYCorner: CACornerMask { get }
}
```

요약하자면,
`Enum`은 다른 선택지를 제외하고 독점적인 선택지를 표현(구현)하고 싶을때!
`OptionSet`은 여러개의 선택지를 동시에 선택할 필요가 있고, 선택지의 중복을 두고 싶지 않을때!

## Reference

- [OptionSet - Apple Documentation](https://developer.apple.com/documentation/swift/optionset)
- [https://dongminyoon.tistory.com/51](https://dongminyoon.tistory.com/51)
