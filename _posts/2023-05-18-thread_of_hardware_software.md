---
title: "하드웨어 스레드 vs 소프트웨어 스레드"
excerpt: "두 개의 스레드에는 어떤 차이가 있을까?"

categories:
  - CS
tags:
  - [CS]

permalink: /ios/thread_of_hardware_software/

toc: true
toc_sticky: true

date: 2023-05-18
last_modified_at: 2023-05-18
---

반갑습니다 :)
GCD에 대해 다시 리마인드를 하고 있는데 제가 여태 GCD의 스레드를 하드웨어 스레드의 개념으로 무의식적으로 잘못 이해하고 있더라구요 :(...

그래서 이번 포스팅에서는 하드웨어 스레드와 소프트웨어 스레드가 무엇인지 알아보고 어떤 차이가 있는지에 대해 알아볼게요!

### CPU, Core
**CPU(Center Processing Unit)**의 줄임말로 중앙 처리 장치라고 하죠?
<span style="color: #808080">(기존에는 프로세서라고 말했는데 점차 CPU라는 말로 대체 되어가고 있다고 해요.같은 의미인줄은 알고 있었는데 이런 히스토리는 처음 알았네요..?)</span>

컴퓨터의 핵심부품(CPU, 주기억장치, 보조기억장치, 입출력장치) 중 하나로 컴퓨터의 뇌와 같은 역할을 하는 하드웨어예요.

**Core**는 CPU의 핵심적인 역할을 수행하는 중심부 역할을 말하며, 시스템의 모든 연산을 처리해요.
Core는 CPU 내부적으로 반도체 부품을 통하여 실제로 존재하는 부분이에요.
![](https://velog.velcdn.com/images/textobey/post/e55023eb-b924-493d-be5f-5b4ea16ba9c3/image.png)

### Hardware Thread

하드웨어 측면에서의 스레드란 **Core가 할 수 있는 최소 단위의 일(혹은 도구)을 의미**해요. 

예전에는 Core에는 1개의 Thread만이 존재했지만 **하이퍼 스레딩** 기술이 도입 되면서 2개 이상의 스레드를 지원할 수 있도록 되었어요.

이렇듯 하드웨어 관점에서의 스레드는 CPU 제조사의 어떠한 기법?기술?인것이죠.

> **하이퍼 스레딩**
: Core는 자신을 n개의 스레드라는 개념으로 나누어서 마치 물리적인 Core의 개수가 n개가 있는것처럼 하는 기술


하나의 코어에서 하나의 일 혹은 도구 밖에 쓰지 못했는데, 하나의 코어에서 n개의 스레드 수 만큼 일 혹은 도구를 쓸 수 있도록 된것이죠!

> 여담으로 한때 유명했던 코어와 스레드의 재밌고 이해가도록 하던 비유
`4코어 8스레드`: 4명의 노예가 있는데, 노예가 팔이 8개 달려있다.
![](https://velog.velcdn.com/images/textobey/post/245061fc-2978-4aac-a2b3-dc515afc76af/image.png)


### Software Thread

프로세스란 프로그램을 실행시켜 동적상태로 변환, 작업중인 프로그램을 의미 하잖아요?(프로그램이 작동되는 과정)

소프트웨어 측면에서의 스레드란 이 **프로세스 안에 포함되는 더 작은 단위의 작업 흐름을 스레드**라고 해요.(한 프로세스 내에서 동작되는 여러 실행의 흐름)

소프트웨어 스레드는 시스템 한도내에서 계속 만들어 낼 수 있어요.(단, 물리적인 CPU 스레드를 초과하며 병렬적으로 제한 없이 실행 될 수 있지는 않음)

![](https://velog.velcdn.com/images/textobey/post/959954c7-682d-4c6a-97c5-a034e384b13a/image.png)

### 결론

![](https://velog.velcdn.com/images/textobey/post/84056b1d-bdbb-4ea2-8546-087556c543c0/image.png)

하드웨어 스레드와 소프트웨어 스레드는 전혀 다른 개념이고,
하나의 하드웨어 스레드는 수 많은 소프트웨어 스레드를 실행 시킬 수 있기 때문에, 굳이 따지자면 하드웨어 스레드의 개념이 더욱 크고, 서로 완전히 다른 개념임을 알아야 할 것 같아요.

이것이 충족돼야 다음에 포스팅할 GCD, Sync/Async, Serial/Concurrent에 대해서도 더 심층적으로 이해할 수 있을거 같네요 :)..



### Reference

- [https://www.youtube.com/watch?v=_dhLLWJNhwY&t=512s ](https://www.youtube.com/watch?v=_dhLLWJNhwY&t=512s )
- [https://juneyr.dev/thread](https://juneyr.dev/thread)
- [https://information-factory.tistory.com/38](https://information-factory.tistory.com/38)
- [https://babbab2.tistory.com/63 ](https://babbab2.tistory.com/63 )
- [https://80000coding.oopy.io/3117e1bd-e14c-4edc-89da-37804de074d6](https://80000coding.oopy.io/3117e1bd-e14c-4edc-89da-37804de074d6)
