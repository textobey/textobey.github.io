---
title: "KWDC23 후기"
excerpt: "한국 최대 규모의 iOS 행사 참여 후기"

categories:
  - ETC
tags:
  - [WWDC, KWDC, iOS]

permalink: /ios/kwdc_review/

toc: true
toc_sticky: true

date: 2023-07-18
last_modified_at: 2023-07-18
---

오늘은 [KWDC23](https://kwdc.dev/)에 참여하고 왔어요 :)
컨퍼런스에서 보고, 배우고, 느낀 점을 생생하게 남기고 싶어서
이 느낌 그대로 바로 포스팅 해볼게요 ~

# KWDC?

![](https://velog.velcdn.com/images/textobey/post/594506df-115f-4dea-bc28-87dbeef21c62/image.png)

아이폰이 한국에 처음 정식 발매된 이후로 14년이라는 시간이 흘렀다고 해요.
그동안에 한국은 Apple에서 간과 할 수 없는 시장 규모와
개발자(기획자, 디자이너 포함) 생태계를 갖추게 되었어요.

그리고 이를 반증할 첫 Apple Store인 가로수길을 시작으로 여의도, 강남, 명동, 그리고 곧 오픈을 앞둔 명동까지 총 5개의 Apple Store가 생겼고,  
단편적으로 이제는 WWDC 또한 한국어 번역 요약본 및
새로운 기술을 만나볼 수 있는 영상에서도 한국어 자막이 준비되어 있죠.

이렇게 성장한 한국 Apple 생태계와 이번 WWDC23의 발표 내용이
우리에게 어떤 변화를 가져다줄지에 대해 아래 세션을 통해
대화를 나누기 위한 엄청 큰? 모임이었던거 같아요.

여담으로, KWDC 오거나이저분은 한국 최대 iOS 커뮤니티 모임을 진행하면 몇 명 정도의 인원이 모일지 궁금하기도 하셨대요.

## Timetable

![](https://velog.velcdn.com/images/textobey/post/4cbb430b-a8ef-4045-8b81-264b1b709352/image.png)

## 내가 참석한 세션

1. 가로수길
dadahae님의 WWDC23 Recap

2. 명동
엽님의 10년된 서비스의 디자인 시스템 적용기

3. 명동
김윤재님의 Swift concurrency: Good to know

4. 명동
강성규님의 제조업에서 SwiftUI + TCA로 앱 개발하기
김용덕님의 현대차 그리고 셔클

### WWDC23 Recap

WWDC23에서 발표된 내용을 조금씩 요약하여 세션을 진행해주셨어요.
정말 세션 제목에 충실?했던 세션인거 같아요.

그 중에서 나도 꼭 살펴봐야지 했던 것은 UIKit에 새로 생긴 기능들과 그 중에서도 empty UI를 지원하는 API가 생긴 부분과 preview를 이제 실제 디바이스에서도 확인하는게 가능하다는것이었어요 !

또, 가장 신기하고 미지의 영역을 접한 느낌?이 들었던건
역시 VisionKit에 대한 내용이었어요.
언젠가는 한번 Apple에서 제공한 튜토리얼을 바탕으로
애플리케이션을 만들어보고 싶다는 생각이 들 정도였으니까요!

이렇게 WWDC23에서 발표된 내용을 요약해주신 덕분에
저처럼 아직 WWDC23의 내용을 하나하나 살펴보지 못한 분들에게는
요약해주신 내용을 통해 각자 자신이 가장 관심있는 분야로
자유롭게 다이브할 수 있도록 잘 유도해주신것 같아 좋았어요.

한편으론, 전문적인 내용으로 딥 다이브 하는 것을
원했던 분들도 있던거 같았는데 그 분들에겐 아쉬울 수 밖에
없었을거 같아요.

하지만, 저는 만족했습니다 :)

### 10년된 서비스의 디자인 시스템 적용기

이번 KWDC는 개발자만을 위한 커뮤니티가 아니라
기획자와 디자이너 분들을 포함하는 아주 큰 모임이었는데요.

이 세션은 디자이너 분들과 디자이너분들에게는
이 개선기를 통해서 간접적으로 경험을 하고
다른 시야를 가질 수 있겠다는 점이 좋을것 같았어요.

또, 디자이너와 자주 커뮤니케이션하고 협업하는 개발자들에게도
커뮤니케이션 하는 방법에 대해서 다시 한번 생각해보고
똑같이 다른 시야를 접해볼 수 있어 좋은 기회였다고 생각해요.

기억에 남는 것은 좋은 개선 결과를 가져오기 위해서는 모두가 합심해야하고 포기하지 않는 끈기와 노력이 있어야한다..
그리고나면 그에 합당한 꿀?이 떨어질것이다 라는 내용이 생각 나네요!

아! 추가로 Q&A 시간에 어떤분께서 엄청 날카로운 질문을 해주셨었는데
이 내용을 여기에 남겨두고 싶어서 추가해놓을게요 :)

![](https://velog.velcdn.com/images/textobey/post/49c2f1e1-8cf5-4785-843a-6287ad408d5a/image.png)



### Swift concurrency: Good to know

제가 요즘 가장 관심있게 공부하고 있는것 중 하나인
Swift concurrency를 주제로 한 세션이었는데요!

역시나..제가 요즘 가장 관심을 가지고 있는것 만큼
제일 재밌기도 했고, 연사분께서도 끊임 없는 자기 질문과
호기심을 가지고 실험하고, 답을 도출해내셨던 과정이
눈에 보이는것 같더라구요 !

연사님의 개인사정에 따라 세션의 내용이 유튜브에 업로드 되거나
되지 않을수도 있다는데 해당 내용은 꼭 업로드 됐으면 좋겠어요 !

Swift concurrency와 GCD를 같이 사용했을때의 퍼포먼스 비교와
concurrency의 Task 우선순위에 따른 스레드 점유에 대한 내용이 제일 기억에 남고 재밌었어요.

그래서, 만약 이 내용이 유튜브에 업로드 된다면
유튜브 내용과 [Swift Concurrency 성능 조사](https://engineering.linecorp.com/ko/blog/about-swift-concurrency-performance) 글을 참고하여
블로그에 정리해서 한번 포스팅할 생각이 있기 때문에,
그때 한번 자세한 내용을 적어볼 수 있도록 해볼게요 :)



### 현대 자동차 세션

현대 자동차 세션은 SwiftUI + TCA로 앱 개발하기, 김용덕님의 현대차 그리고 셔클을 묶어서 현대 자동차 세션이라고 했는데요.

TCA가 ReactorKit 구조와 크게 다르지 않은덕분에
세션을 이해하고 즐기기에는 무리가 없었던것 같아요.

아쉬운점은 기술에 대한 어떤 딥 다이브가 있었던것은 아니고,
SwiftUI + TCA 구조를 가져가게된 이유,
현대 자동차가 소프트웨어와 함께 미래를 그리고 있는것에
굉장히 능동적이고 적극적이라는 내용의 세션이었어요.

좋았던점은 SwiftUI + TCA를 진행하면서 겪었던
시행착오와 해결했던, 해결하지 못했던 이슈들을 적극적으로
공유해주셨던 점이 좋았어요 !

# KWDC23 느낀점

정말 소중하고 재밌는 시간을 보내서 좋았고,
이 수많은 개발자, 기획자, 디자이너 분들과 Apple 생태계를
더욱 키워나갈 수 있도록 노력하자는 생각도 들고
한편으론, 이 수많은 iOS 개발자분들 속에서 내가 가진
경쟁력은 무엇일까? 어떤 방향으로 더욱 성장하고, 배우고
노력해가야 할지 이번 KWDC23을 통해 새로 배우게된 것도 많고,
느끼게 된 점도 아주 많네요... :)

앞으로도 iOS 개발자로서 나아갈 길은 멀고 쉽지 않을 것은 확실하지만
고통스럽거나 피할것 같지는 않아요.
오히려, 재밌고 즐거울것 같다는 느낌?

아무튼..아주 재밌고 즐거운 경험을 하고와서
내년에도 진행하다면 또 다녀오고 싶어요 !
