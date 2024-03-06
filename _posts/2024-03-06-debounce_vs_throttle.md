---
title: "Debounce vs Throttle"
excerpt: "특징과 차이점을 알아보자(2)"

categories:
  - RxSwift
tags:
  - [RxSwift]

permalink: /rx_swift/debounce_vs_throttle/

toc: true
toc_sticky: true

date: 2024-03-06
last_modified_at: 2024-03-06
--- 



안녕하세요~~
이전 글인 [Signal vs Driver](https://textobey.github.io/rx_swift/driver_vs_signal/) 글을 포스팅 하면서  
'어? 활용도가 비슷한데, 이렇게 헷갈리는 것들을 시리즈처럼 정리하면 좋겠다' 라는 생각이 들어서!!  

바로 이렇게, 두번째 시리즈?인 `Debounce`와 `Throttle`에 대해  
어떤 활용 방법이 있으며, 서로 어떤 차이가 있는지 예제를 통해 한번 정리해볼게요!


## Debounce

![](https://velog.velcdn.com/images/textobey/post/6cbc383d-5456-40fe-bc06-3f2aaa830b9c/image.jpeg)

debounce 오퍼레이터는 위의 그림처럼,  
Observable 시퀀스에서 특정 시간안에 다수의 이벤트가 발생했을때,  
연이은 이벤트를 모두 처리하는것이 불필요하거나 피하고 싶은 경우에 활용할 수 있는데요.

특정 시간은 사용자(프로그래머)가 직접 설정할 수 있습니다!  
코드를 한번 볼까요?


### 코드

```swift
searchController.searchBar.rx.text
            .debounce(.milliseconds(300), scheduler: MainScheduler.asyncInstance)
            ...(중략)
```
위의 코드에서는 검색창에 입력받는 검색어가 입력될때마다, 짧은 시간동안 여러번의 API 호출을 피하고 싶은 경우를 예로 들었습니다.

검색어 Observable은 연이어 입력되어도,  
`Debounce` 에 의해 0.3초가 경과한 이후에만 Observable을 방출하게 되어,  
검색에는 0.3초에 쿨타임?이 생기게 되는거죠.

### 결과

![](https://velog.velcdn.com/images/textobey/post/93eaa92c-f685-40c8-bcc4-f3288297a146/image.gif)

## Throttle

![](https://velog.velcdn.com/images/textobey/post/1a8ecdf5-4a9f-42f6-8b91-2aa2c0e5f0d8/image.jpeg)

Observable 시퀀스에서 연이어 발생하는 2, 3, 4, 5 이벤트를  
throttle 오퍼레이터를 활용하면 **연이은 이벤트의 첫번째, 최신(마지막) 이벤트** 만을 최종적으로 방출하게 됩니다.

그런데, 중요한 개념이 한가지 더 있습니다 🔥🔥🔥

바로, 그림에는 없는 `latest` 라는 Bool 값인데요,
코드를 보면서 어떤 역할을 하는 값인지 확인하면 이해가 더 잘 될거예요!

### 코드

```swift
searchController.searchBar.rx.text
            .throttle(.milliseconds(300), latest: false, scheduler: MainScheduler.asyncInstance)
            ...(중략)
```


- `latest` true: **연이은 이벤트의 첫번째, 최신(마지막) 이벤트**를 최종적으로 방출
- `latest` false: **연이은 이벤트의 첫번째** 를 최종적으로 방출

그림을 보면서, 이야기 하자면 `latest` 를 어떻게 설정하느냐에 따라 최종적으로 방출되는 이벤트가 달라지게 됩니다.
- `latest` true: 2, 3, 4, 5 -> 2, 5
- `latest` false: 2, 3, 4, 5 -> 2


## 정리하며

`Debounce` 와 `Throttle` 모두 연이은 이벤트를 필터링하여,  
짧은 시간내 많은 이벤트가 발생하는 것을 방지할 수 있는 오퍼레이터 라는 점이 닮았고,  
연이은 이벤트 발생을 어떤 식으로 방지할 수 있는지 세부적인 방식에 차이가 있다는 것을 알았습니다! 

이번 포스팅을 통해 주어진 상황에서 어떤 필터링 오퍼레이터를  
활용하는게 더 적합할지 구분할 수 있도록 도움이 되면 좋겠습니다 😆
