---
title: "Choosing Between Structures and Classes"
excerpt: "구조체와 클래스중에 어떤걸 쓸까?"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/between_struct_class/

toc: true
toc_sticky: true

date: 2023-10-16
last_modified_at: 2023-10-16
---

안녕하세요 :)

혹시, 여러분은 서버 통신으로부터 받은 데이터 혹은 DB 데이터에 대한 모델을 Struct로 생성중이신가요? 아니면 Class로 생성중이신가요?

이번 포스팅에서는 [Choosing Between Structres and Classes](https://developer.apple.com/documentation/swift/choosing-between-structures-and-classes) 아티클을 읽어보고, 왜 서버 데이터 모델을 Strcut로 생성하는지에 대해 정리해볼게요!

## Overview

구조체와 클래스는 앱의 데이터 저장 및 행동 모델링에 좋은 선택지이지만, 구조체와 클래스의 유사성으로 인해 어느 것을 선택해야 할지 결정하기 어려울 수 있기 때문에, Apple에서는 어떤 옵션을 선택하면 좋을지 가이드해주고 있어요.

- 기본(Default)으로 구조체를 사용한다.

- Objective-C 와의 호환성이 필요하다면 클래스를 사용한다.

- 데이터의 identity를 컨트롤하는 것이 필요하다면 클래스를 사용한다.

- 프로토콜과 함께 구조를 사용하여 구현을 공유함으로써 동작을 채택합니다.

## Use structures by default.

Apple에서는 Default로 구조체를 사용하기를 권장하고 있어요.
Swift의 구조체는 다른 언어에서는 Class에 제한된 많은 기능을 포함하고 있기에 저장 프로퍼티, 연산 프로퍼티 및 메서드가 포함될 수 있어요.
또, 프로토콜을 채택할 수 있기 때문에 구조체가 가져야하는 기본 동작을 구현(준수)하도록 할 수 있어요.

> 이를 뒷받침 하듯, Swift 표준 라이브러리 및 Foundation은 numbers, strings, arrays, dictionaries처럼 자주 사용하는 유형에서 구조체를 사용 중!
> ![](https://velog.velcdn.com/images/textobey/post/b6e8a9df-3843-43df-bb5f-38b395b8fac1/image.png)

### 구조체를 사용할 때 장점

그렇다면, Apple에서 아무런 이유 없이 클래스보다 구조체를 Default로 사용하기를 권장하지는 않았을거예요!

어떤 장점이 있기 때문인지 살펴볼게요.

1. 구조체를 사용하면 앱의 전체 상태를 고려할 필요 없이 코드의 일부를 더 쉽게 추론할 수 있다.

2. 클래스와 달리 구조체는 값 타입이므로 클래스에 대한 로컬 변경 사항은 의도적으로 흐름을 전달하지 않는 한 앱의 나머지 부분에 표시되지 않는다.

3. 결과적으로 복잡한 함수 호출에서 보이지 않게 변경되기보다 명시적으로 변경될 것임을 확신할 수 있다.

아래는 위의 장점에 대한 제 생각과 근거를 적어봤어요.

참조 타입인 클래스를 사용하면 같은 클래스 인스턴스를 여러 곳에 할당하고 값을 변경시키면, 여태 할당된 모든 변수에 영향을 주게되어 앱의 전체적인(혹은 넓은 영역)을 고려해야겠죠?

이런 이유로 예를 들어, 클래스의 값이 함수 혹은 의도되지 않은 사이드 이펙트에 의해 변경되었을 수도 있기 때문에, 명시적으로 변경된 곳을 확신할 수 없게 된다!

그렇기에, 참조 타입인 클래스보다 값 타입인 구조체를 Default로 권장하는것 같아요 :)

## Use classes when you need Objective-C interoperability.

Objective-C API나 기존 Objective-C 프레임워크에는 클래스로 정의된 것이 많다고해요, 그렇기에 이것들을 사용하여 데이터를 처리하기 위해서는 클래스를 사용하기를 추천하고 있어요.

## Use classes when you need to control the identity of the data you’re modeling.

Swift의 클래스는 참조 타입이기 때문에, ID 개념이 내장되어있어서 다른 두 클래스 인스턴스가 같은 값이 저장되어 있다고 하더라도 식별 연산자(===)에 의해 다른다고 간주돼요.

이는 또한 앱 전체에서 클래스 인스턴스를 공유할 때 해당 인스턴스에 대한 변경 사항이 해당 인스턴스에 대한 참조를 보유하는 모든 부분에 반영된다는 것을 의미해요.

그래서, 인스턴스 ID가 필요한 경우 클래스를 사용하는 것이 효율적인  file handles, network connections, and shared hardware 등 참조를 잃지 않아야 하는 곳에 주로 Class가 사용돼요.

## Use structures along with protocols to adopt behavior by sharing implementations.

'구조체는 상속이 불가능하다' 라는 것이 차이점이라고 했지만, 프로토콜을 함께 사용하면 상속과 비슷하게 모델링 하는것이 어느정도는 가능해요.

하지만, 클래스처럼 부모클래스를 상속하여 동작을 공유하여 사용하는것과는 확실히 차이가 있죠?

그렇기 때문에, Apple에서는 어떤 것을 모델링하더라도 상속 개념이 필요할때는 클래스, 구조체, 열거형 모두 채택 가능한 프로토콜을 기반으로 작성해 보는 것을 권장하고 있어요.

<span style="color:gray">(프로토콜을 기반으로한 코드 작성을 더 열심히 해야겠네요..!)</span>
