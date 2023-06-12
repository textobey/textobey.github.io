---
title: "CALayer"
excerpt: "Core Animation의 핵심 CALayer"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/core-animation-layer/

toc: true
toc_sticky: true

date: 2023-06-12
last_modified_at: 2023-06-12
---

이번 포스팅에서는 저번 Core Animation에 이어서
Core Animation 인프라의 중심인 **CALayer**에 대해 알아볼게요 :)

---

## Core Animation

CALayer에 대해 알아본다더니..왜 또 Core Animation인가요?
그것은.. Core Animation과 CALayer가 떼어놓고 말할 수 없는 존재이기 때문에..

혹시 Core Animation에 대해 알고 싶으시다면 [CoreAnimation]() 포스팅에서 확인할 수 있어요 :)

### Core Animation의 특징
- View 및 앱의 기타 시각적 요소에 애니메이션을 적용하기 위한 범용 시스템을 제공
- 앱의 View를 대체하지 않지만, View와 통합하여 콘텐츠 애니메이션에 대한 더 나은 성능과 지원을 제공하는 기술
- 그래픽 하드웨어에서 직접 조작할 수 있는 비트맵으로 뷰의 내용을 캐싱하여 Core Animation 동작 수행

---

## CALayer

![](https://velog.velcdn.com/images/textobey/post/b1146a5d-d389-4b85-bf50-3b3c5db437d4/image.png)

CALayer는 이미지 기반의 콘텐츠를 관리하고 콘텐츠에서 애니메이션을 수행할 수 있게 해주는 Object라고 설명하고 있는데요.

좀 추상적인 설명이기 때문에, CALayer의 주요 업무라고 할 수 있는 내용을 보고 이해 해볼까요?

### CALayer 주요 업무

- 제공하는 **시각적 콘텐츠를 관리**하는 것
- Layer Object 스스로에도 **배경색, 테두리, 그림자**와 같이 화면에 표시하는데 사용하는 콘텐츠도 있음
- 시각적 콘텐츠 외적으로도 콘텐츠를 화면에 표시하는데 사용되는 **지오메트리(위치 크기 및 변환)등에 대한 정보**도 **유지관리**
- **Layer의 속성을 수정**하는 것은 Layer의 콘텐츠 또는 지오메트리에서 **애니메이션을 시작하는 방법**임



## CALayer와 Core Animation의 관계

그래서 이제는 정확히 이 둘이 어떤 연관을 가지고 있는지 궁금할거 같아요.

**Drawing과 Animation의 기초를 제공**하는 Layer가 CALayer이고,
Layer Object는 Core Animation으로 수행하는 모든 작업의 핵심이에요.

Core Animation의 인프라의 중심이 CALayer라는 것을 알것 같네요.


### Layer-Based Drawing Model

![](https://velog.velcdn.com/images/textobey/post/193b0227-28e6-4ecc-81e5-1199665ce379/image.png)

Layer Object는 Core Animation으로 수행하는 모든 작업의 핵심이에요.

Layer Object가 Core Animation의 핵심이지만 대부분의 Layer는 앱에서 실제 그리그를 수행하지 않아요.

Layer는 앱이 제공하는 콘텐츠를 캡처하고 백업 저장소 라고도 하는 비트맵에 캐시해요.

그 이후에
1. 변경 사항이 애니메이션을 트리거(Layer의 속성을 수정하는 행위 등)  
2. Core Animation은 Layer의 비트맵 및 상태 정보를 위에 그림과 같이 새로운 정보를 만들어냄  
3. 만들어진 새로운 정보를 사용하여 비트맵 렌더링 작업 수행하는 CPU에 전달  
4. GPU에서 비트맵을 조작하면 App Window에 소프트웨어에서 수행할 수 있는것  보다 빠른 속도로 애니메이션이 생성됨  

이외에 Layer의 기반 애니메이션, 사용 좌표계, 기하학 조작 등 [Core Animation Basics](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/CoreAnimationBasics.html#//apple_ref/doc/uid/TP40004514-CH2-SW3) 문서에서 확인할 수 있어요.

---

## Layer와 View와의 관계

![](https://velog.velcdn.com/images/textobey/post/ee6b516b-89f4-434d-9f1f-66c54c7c6395/image.png)

iOS 프로젝트에서 UIView 쪽으로 Jump To Definition 기능을 통해 들어가보면 위에 그림처럼 `layer` 프로퍼티와 `CALayerDelegate`가 채택된것을 볼 수 있는데요.

View를 생성하면서 같이 만들어지고 `layer` 프로퍼티(Layer Object)가 뷰에 의해 생성 되었을때, 뷰는 자동으로 `CALayerDelegate` 의 대리자로 할당돼요.

> 이때 `layerClass` 프로퍼티를 활용하면  CALayer 타입이 아닌 다른 Layer 타입으로도 생성이 가능해요.

이렇게 맺어진 Layer와 View의 관계를 통해 얻을 수 있는 이점이 있는데요.

### Layer가 View에 주는 이점

- 높은 프레임으로 콘텐츠를 더 쉽고 효율적으로 애니메이션화 할 수 있는 인프라를 제공(Core Animation의 핵심 기능을 제공하는것과 같음)
-> iOS에서는 모든 View가 Layer 개체에 의해 지원됨

### View가 Layer에 주는 이점

- 스스로 시각적 인터페이스를 만들 수 Layer에 시각적 인터페이스 제공
- handle event, draw content, responder chain 관여 등 Layer가 수행하지 못하는 작업을 View는 수행할 수 있음

이런 이점들을 제공하며 앱에 필수적인 기능들이 갖춰지는 구조라고 볼 수 있을거 같아요.
그리고 이렇게 연결된(맺어진) 관계를 임의로 변경해서는 안된다고 해요.

또 짚고 넘어가야할 점은 모든 View는 Layer가 있어야 존재할 수 있지만,
모든 Layer는 View를 가져야 하는것은 아니에요.

이렇게 독립적으로 존재할 수 있는 Layer를 독립 실행형이라고 해요.
독립 실행형이기 때문에, Layer에는 다른 SubLayer들이 존재할 수 있어요.


## 마무리

이번에 Core Animation과 CALayer에 대해 차근 차근 깊게 알아가보며,
역시 low-level의 앱 인프라가 되는 개념이 쉽지 않고, 알아가야 할 것이 많다고 느껴졌어요.

또, 하나를 알면 다른 하나가 궁금해지는게 생기는 정말 고구마 줄기 같은..느낌을 받았네요 :)..
이 포스팅을 준비하면서 궁금해진 것들을 아래에 나열하고 하나씩 iOS 지식 퍼즐을 맞춰갈 수 있도록 노력해야겠어요.


### 추가로 궁금해진점

- Core Graphic과 CPU 기반 Drawing과 GPU 기반 Drawing의 차이
- iOS의 Render loop와 hitch 이슈
- iOS의 애니메이션 타입들의 종류
- UIView.animate 메서드와 Core Animation과의 관계

천천히..알아가보도록 하....


## Reference
- [https://developer.apple.com/documentation/quartzcore/calayer](https://developer.apple.com/documentation/quartzcore/calayer)
- [https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/CoreAnimationBasics.html#//apple_ref/doc/uid/TP40004514-CH2-SW3](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/CoreAnimationBasics.html#//apple_ref/doc/uid/TP40004514-CH2-SW3)
