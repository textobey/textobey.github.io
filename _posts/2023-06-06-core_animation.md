---
title: "Core Animation"
excerpt: "Core Animation에 대해 알아보자"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/core_animation/

toc: true
toc_sticky: true

date: 2023-06-06
last_modified_at: 2023-06-06
---

이번 포스팅에서는 CALayer에 대해 다뤄보기전에 알아야 할 Core Animation에 대해 알아볼게요. <span style="color: #808080">(짐작 가시겠지만! CoreAnimation + Layer = CALayer)</span>

생각 이상으로 iOS View 계층에서 굉장히 중요한 역할을 하고 있고,
Core Animation과 CALayer에 대한 개념과 원리를 알아갈수록 iOS 애니메이션 시스템에 대한 깊은 이해를 할 수 있으니 [공식 문서](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/NaN)도 읽어주세요 :)

## CoreAnimation

Core Animation이란 뭘까요?

- 앱의 **View 및 기타 시각적 요소에** 애니메이션을 적용하는데 사용하는 **그래픽 렌더링 및 애니메이션 인프라.**
- **CPU에 부담을 주지 않으면서** 높은 프레임과 부드러운 애니메이션을 제공
- 애니메이션의 각 프레임을 그리는데 필요한 대부분의 **작업이 자동으로 수행**

하는것이 [Core Animation](https://developer.apple.com/documentation/quartzcore)이라고 설명하고 있어요.


### How?

그렇다면 Core Animation은 어떻게 저런 역할을 수행할 수 있는것인지 궁금해졌어요.

문서에서는 Core Animation이 실제 드로잉 작업을 온보드 그래픽 하드웨어로 넘겨 렌더링을 가속화 한다고 해요.

> 그래픽 하드웨어라고 하면 iOS는 단순히 칩셋의 GPU를 말하는것 인지 문득 궁금해서 찾아봤어요.
[그래픽 프로세서](https://developer.apple.com/library/archive/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/HardwareGPUInformation/HardwareGPUInformation.html#//apple_ref/doc/uid/TP40013599-CH106-SW1)를 보면 iPhone이 출시된 이후 Apple은 계속해서 새로운 iOS 기기의 GPU 기능을 개선해왔다고 하며,
A7, A8, A9, A10 및 A11 GPU는 함께 Metal 및 OpenGL ES 3.0을 모두 지원하는 차세대 그래픽 하드웨어를 생성한다고 설명되어 있어요. <span style="color: #808080">(근데 왜 그래픽 하드웨어를 '생성'한다고 되어있는걸까..)</span>


결과적으로 Core Animation의 자동 그래픽 가속은 CPU에 부담을 주지 않고 앱 속도를 저하시키지 않으면서 높은 프레임 속도와 부드러운 애니메이션을 제공할 수 있는것이죠. 

### Stack

![](https://velog.velcdn.com/images/textobey/post/5cc8eb99-1678-4bc8-af42-d87f9a1c78b5/image.png)

위 그림처럼 Core Animation은 UIKit 보다도 low-level에 위치하고 있어서
UIKit 프레임워크를 포함하고 있는 Cocoa Touch의 View WorkFlow와 긴밀하게 통합되어 있어요.

그렇기 때문에, UIKit은 Core Animation에 위에서 iOS 앱을 작성하다보면 알게 모르게 Core Animation을 사용하고 있는것인거죠.

Apple에서는 Core Animation을 직접 사용할 필요는 없을 수도 있지만, 직접 사용하게 된다면 앱 인프라의 일부로 포함된 Core Animation이 어떤 역할을 수행하는지 이해가 필요하다고 설명하고 있어요.

### 마무리

이렇게 Core Animation에 대해 알아봤는데요.

알아야할것은 Core Animation은 드로잉 시스템 자체가 아니에요. 하드웨어단에서 앱의 콘텐츠를 합성하고 조작하기 위한 인프라일 뿐이죠.

그리고 이 인프라의 중심에는 다음에 다뤄볼 CALayer가 있어요.
CALayer가 무엇이기에 인프라의 중심이고, 어떤 역할을 하는지 다음에 알아볼게요!

이 CALayer에 대한 내용을 다루고 나서야 진정한 Core Animation과 CALayer를 모두 이해할 수 있는 그림이 되겠네요 :)

### Reference

- [Core Animation](https://developer.apple.com/documentation/quartzcore)
- [About Core Animation](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40004514)
- [Core Animation Basics](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/CoreAnimationBasics.html#//apple_ref/doc/uid/TP40004514-CH2-SW3)
