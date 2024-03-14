---
title: "SwiftUI는 ViewModel이 불필요하다."
excerpt: "MVVM 덕분에 MVVM이 되어서 다행이다."

categories:
  - Architecture
tags:
  - [Architecture, MVVM, SwiftUI]

permalink: /architecture/stop_using_swiftui_with_mvvm/

toc: true
toc_sticky: true

date: 2024-03-14
last_modified_at: 2024-03-14
--- 



안녕하세요 😊

날이 완전 풀린거 같아서 기분이 좋아지는 요즘입니다~~  
저는 깃허브에 iOS 아키텍처라는 책들을 꽂아놓는 도서관 느낌으로  
[레포지토리](https://github.com/textobey/ios-architectures)를 하나 운영하고 있는데요!

SwiftUI-MVVM 구조로도 작성을 해본 경험이 있는데,  
이전에 재밌게 보았던 [아티클](https://qiita.com/karamage/items/8a9c76caff187d3eb838#%E9%96%A2%E9%80%A3%E3%83%84%E3%82%A4%E3%83%BC%E3%83%88%E3%81%BE%E3%81%A8%E3%82%81)을 공유해드리고, 그에 대한 저의 생각도
함께 공유하고  나눠보고자 이번 포스팅을 작성해봅니다!

# SwiftUI는 ViewModel이 불필요하다.

MVVM은 UIKit 기반에서 많은 iOS 개발자들이 채택하는 아키텍처죠  
좋은 아키텍처의 기준을 `관심사의 분리`, `테스트 용이성`, `사용하는 것이 편한` 으로  
판단하자면 MVVM의 아키텍처는 분명 좋은 아키텍처로 점수를 받는다고 생각합니다. 🤔

그리고, 데이터와 화면(View)에 표시되는 정보를 일치화하는 연결 작업  
`데이터 바인딩` 은 개발자의 업무 효율성과 생산성을 증가 시켜주는 장점도 있죠.

그렇게, 선언형 UI인 SwiftUI로 넘어와서도 자연스럽게 MVVM을 채택하는 일이 확산 되면서  
이런 글을 작성하게 된 것이 아닐지 추측이 되네요.

그럼, 제 의견? 이전에 [아티클](https://qiita.com/karamage/items/8a9c76caff187d3eb838#%E9%96%A2%E9%80%A3%E3%83%84%E3%82%A4%E3%83%BC%E3%83%88%E3%81%BE%E3%81%A8%E3%82%81)에서 어떤 근거를 가지고 어떤 주장을 펼치고 있는지  
제가 좀 정리해봤습니다 😎

### MVVM 디자인 패턴은 SwiftUI의 특징과 맞지 않는다.

> ViewModel이라는 것은 상태를 View에 Binding해서 Reactive로 반영하는 것이 목적으로 도입되었지만, 선언적 UI에 그 기능이 내포되어버렸기에 ViewModel은 불필요하다.
SwiftUI 등의 선언적 UI를 사용하는 세계에서는 생각하는 법을 바꿀 필요가 있다.

SwiftUI.View에는 데이터 바인딩의 기능이 포함됨
( SwiftUI는 PropertyWrapper를 통해 자체적으로 데이터 바인딩을 이룰 수 있다. )
⇒ UIKit.UIView + MVVM 으로 했던 것이 SwiftUI.View 하나가 되었다.

### 디자인 패턴을 위한 디자인 패턴의 작성이 되어버린다.

이미 SwiftUI는 MVVM으로의 기능을 수행하기에
"SwiftUI에서 MVVM를 사용해버리는 것"은  바꿔말하면  
"SwiftUI + MVVM = MVVM on MVVM",  **똑같은걸 두 번 말하는 것이 되어버린다.**

**"MVVM 덕분에 MVVM이 되어서 다행이다" 라는 상태**가 되어있다.”

>![](https://velog.velcdn.com/images/textobey/post/72e34438-1a93-428e-afae-f9ef8ef87dad/image.png)
거참.. 이상하구만 난 이미 MVVM이 되었는데, MVVM이 될 상이라니!

정리하자면.. SwiftUI에서 ViewModel의 개념과 함께 ‘ViewModel’이라는 단어 자체도
선언적 UI. “SwiftUI 환경에서는 그 존재 의미를 잃었다” 라는 의견

---

# 긍정적인 의견

아래는 아티클에 대한 긍정적? 혹은 동감하는 저의 의견입니다!

MVVM 채용에 따른 보일러 플레이트 코드도 가지지 않으면서,   
간결하면서도 빠른 생산성을 가지고 효과적으로 서비스 개발을 할 수 있다.

그리고, 원문의 글을 보면서 오해할 수 있는점이 있다. (아 다르고 어 다른 느낌!)
MVVM 아키텍처를 SwiftUI에서 사용하지 말자 ❌
MVVM 아키텍처가 SwiftUI에 이미 적용 돼있으니 중복 사용하지 말자 ✅

SwiftUI의 선언적 UI 프로그래밍의 특성이 MVVM의 필요성을 감소시킨 것이 아니라,
더욱 쉽고 간결하게 MVVM의 강점을 살릴 수 있는 환경을 촉진하고 있다는 것.

그렇기 때문에, MVVM과 같은 아키텍처는 여전히 사용될 수는 있지만   
SwiftUI 자체의 기능들이 ViewModel 레이어 없이 이미 충분히 상태 관리와  
데이터 바인딩을 수행할 수 있기 때문에 ViewModel은 생성하지 않아도 된다.

# 궁금한 점(+부정적..?)

긍정적인 의견도 있는 한편으론, 부정적인 성격의 궁금한 점도 몇 개 떠오르더라구요. 💭

아키텍처의 종류에 따라 다른 개념/이름으로 불리겠지만,
아래 그림에서 ‘return’ 방향의 ViewModel(혹은 Presenter)은 도메인 영역으로부터  
얻어온 비즈니스 핵심 데이터를 현재 View 성격에 맞게 그려주기 위한 계층이잖아요?

![](https://velog.velcdn.com/images/textobey/post/57e6590f-0fe5-48ae-8485-5c2b5062fce3/image.png)

이런 ViewModel(Presenter) 계층을 명시적으로 생성 해둔다면
MVVM on MVVM이 된다하더라도, 또 보일러 플레이트 코드가 추가된다 하더라도

복잡한 비즈니스 요구사항이나 단위 테스트를 진행해야 하는 상황에서 장점이 있지 않을까?..하는

- UI와 비즈니스 로직 계층(책임)을 명확하게 나누는 관심사의 분리가 가능하다.
- I/O 상황에서 복잡한 데이터 가공이 요구되는 상황에서 이점이 있다.
- 테스트에 더 유연한 소프트웨어가 될 것이다.

# 마무리

하하.. 어떻게 정리하고 끝내면 좋을지 모르겠네요~

정말 좋아했던 재밌는 웹툰 혹은 영화가 열린 결말로 끝난 기분이에요.  
하지만, 한 가지 생각나는 단어는 “no silver bullet”

상황에 따라 혹은 협업하는 팀원들과의 의사 결정에 따라 더욱 좋은 가치를   
제공해줄 선택은 그때마다 항상 다를거예요.

그런 상황에 대비하여 이렇게 재밌는 아티클을 읽고 제 생각을 정리해둔 것이  
추후에 도움이 된다면 좋겠네요.

읽어주셔서 감사합니다 (_ _ )
