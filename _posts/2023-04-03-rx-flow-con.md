---
title: "RxFlow를 구체화 해보자"
excerpt: "추상화된 RxFlow를 구체화해 볼 수 있을까?"

categories:
  - RxSwift
tags:
  - [RxFlow, Coordinator]

permalink: /rx-swift/rxFlow-concreteization/

toc: true
toc_sticky: true

date: 2023-04-03
last_modified_at: 2023-04-03
---

# 소개글

이 포스팅에서는 RxFlow의 필요성, 장점, 내부 구조 같은 서론들은 뒤로하고서
높은 추상화 단계를 가지고 있기 때문에 잘 와닿지 않는 RxFlow를 실제로 존재하는것들에 비유하여 **구체화**해보는 과정을 진행해보도록 할게요 :)

# RxFlow의 주요 개념
<img src="/assets/images/posts_img/rx-flow-con/rx-flow-con-1.png" alt="con1" width="50%">

- **Step**
Step은 navigation을 lead 할 수 있는 'Key'예요.
이 Key(Step)에 따라 다르게 navigate되고 이를 적절하게 표현하기 위해 enum으로 정의하고 있죠.

- **Flow**
RxFlow의 핵심이에요. Coordinator의 역할을 하고 있어요.
때에 따라 새로운 Flow를 생성할 수도 화면을 전환할 수도 있죠.

- **Stepper**
RxFlow의 주요 키워드 중 가장 추상적인 개념이라고 생각돼요.
너무 복잡하게 생각하지 않고 아래 내용만 집고가요.

1.
  Stepper는 Flow내에 있는 Step(event)를 방출할 수 있는 모든것이에요.

2.
  ViewController, ViewModel, Custom Class/Struct 등
  Stepper 프로토콜을 채택하고 준수하면 무엇이든 Stepper가 될 수 있어요.


- **Presentable**
Root가 되는 UIViewController예요.
UIViewController를 상속 받는 다른 Controller도 모두 가능하죠.
참고로, Flow도 Presentable이에요!

- **Flowable**
Coordinator에게 구체적으로 Presentable과 Stepper가 어떻게 조합되고 역할을 하는지 설명을 해줘요.

- **Coordinator**
위의 조합들을 잘 섞어주는 조정자, 진행해주는 진행자 역할을 해요.

---

# 구체화

주요 개념을 리마인드 했으니 예시를 통한 구체화를 진행해볼게요.

<img src="/assets/images/posts_img/rx-flow-con/rx-flow-con-2.png" alt="con1" width="50%">

자 여기에 징징이 집이 있어요.
징징이 집이 엄청난 대저택은 아니지만 2층집에다가 다양한 목적의 공간이 많이 있죠.

### Step 
화장실, 욕실, 침실, 주방, 창고방, 드레스룸, 그림방, 클라리넷 연주방 등 등..
Step을 구체화 한다고 하면 저는 이런 방(들)을 Step(s)이라고 구체화 할거예요.

### Flow
그렇다면 Flow는 무엇일까요?
거실, 주방을 가기 위한 복도가 있을거고 또 욕실, 침실, 창고방, 드레스룸 등을 가기 위한 복도가 있을거예요.

이 **복도**를 Flow라고 구체화 할거예요. 복도와 그 복도를 통해 열거된 방들..

그림을 통해서 표현하자면 이렇게 그릴 수 있겠네요.
<img src="/assets/images/posts_img/rx-flow-con/rx-flow-con-3.png" alt="con1" width="50%">

### Stepper
`징징이집 복도를 이용해서 징징이집 클라리넷 연주방으로 보내줘` 이 선언을 수행하기 위해서 필요한 조건이 무엇일까요?

당연하게 여겨지며 눈치도 못 챌거 같아요. 하지만 당연하게도 복도와 방이 있으려면 집이 있어야겠죠? 징징이**집**을 Stepper라고 구체화 할거예요.

> Q. 그런데, 집이 있어야 복도와 방이 있을 수 있는건 알겠어...
> 근데 집이 어떤 이벤트를 방출시켜서 나를 클라리넷 방으로 옮겨주는건 아니잖아?
> 그런데도 집을 Stepper라고 할 수 있는거야?

맞아요, 뭔가 좀 부족하죠?

하지만 이것은 Stepper가 올마이티한 능력을 가지고 있다고 오해하면 생기는 문제인것 같아요.
Stepper는 그저 Flow를 통해 Step으로 갈 수 있게끔 **조건(프로토콜)**을 달성(준수)하는 책임을 가지고 있을뿐이고

그 조건을 달성 했을 때 클라리넷 방으로 옮겨주는 진행자 역할을 하는건 **Coordinator**이니까요.


**번외)**

> Stepper라는 집이 없으면 이런 느낌이지 않을까요?
>  집도 없이 어느 땅바닥인지도 모르는 곳에서 Coordinator가 클라리넷 방으로 옮겨줄 수 있을까요?
> <img src="/assets/images/posts_img/rx-flow-con/rx-flow-con-4.png" alt="con1" width="50%">

---

# 마무리
꽤 높은 레벨로 추상화된 Cooridnator 패턴을 실제 존재하는 것으로 비유해 보았는데요!

주관적인 입장으로 정리된 글이다보니 이해가 안가거나 잘못된 비유 방향 등 개선 해야할 부분이 꽤 있을거 같아요.

역시 스스로에게 잘 맞는 부분은 적용하고 그렇지 않으면 본인만의 방향으로 다시 세워보는걸 추천드려요 :)
