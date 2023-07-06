---
title: "ReactorKit을 공부하며 생겼던 궁금점들"
excerpt: "ReactorKit을 공부하면서 궁금했던 내용을 정리해보자"

categories:
  - Architecture
tags:
  - [Architecture, ReactorKit]

permalink: /ios/reactor-kit-qa/

toc: true
toc_sticky: true

date: 2023-04-04
last_modified_at: 2023-04-04
---



ReactorKit을 공부하며 샘플 프로젝트를 작성하고 있을 때, 궁금해진 내용 2가지를 간단하게 기록 해놓으려고 해요 :)


### Q. 여러개의 Reactor의 결합 또는 상호작용이 가능할까?

Reactor는 서로의 존재 여부를 몰라야해요.
관련 링크는 아래 References 중 ReactorKit의 Github 링크의 View Communication 파트에서 관련한 내용을 확인할 수 있어요.




### Q. Action 없이 State 방출이 가능할까?

가능해요!

일반적인 상호작용에 의한 Action의 동작, State의 방출의 일반 시퀀스가 아닌
개발자의 의도에 의한 트리거로 Action을 거치지 않고 State를 방출하는 방법으로는 Reactor에 전역 State(PublishRelay, BehaviorRelay)를 생성하고 생성된 relay와 Reactor의 transform(:) 메서드를 사용하여 merge 등 오퍼레이터를 활용하여 Observable<State> 또는 Observable<Mutation>으로 리턴할 수 있어요.



---

# References

- [https://github.com/ReactorKit/ReactorKit](https://github.com/ReactorKit/ReactorKit)
- [https://eunjin3786.tistory.com/147](https://eunjin3786.tistory.com/147)
