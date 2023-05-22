---
title: "Sync/Async, Serial/Concurrent"
excerpt: "Sync/Async와 Serial/Conccurent 이해"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/sync_async_serial_concurrent/

toc: true
toc_sticky: true

date: 2023-05-19
last_modified_at: 2023-05-21
---

앞으로 GCD와 RxSwift 관련 포스팅을 하기 전에 먼저 알아야 할
Sync(동기)/Async(비동기)와 Serial(직렬)/Concurrent(동시)에 대해 다뤄볼게요 :)

이 포스팅 내용은 앨런님이 올려주신 인프런 강의 [[그림으로이해하는] 동기(sync) 비동기(async)의 개념에 대한 가장 직관적인 이해](https://www.inflearn.com/course/sync-async-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4)를 보고 정리한 내용이에요.



## 스레드

들어가기에 앞서 스레드의 개념에 대해 간략하게 짚고 넘어가려고 해요.

프로세스와 스레드 모두 작업의 흐름을 의미하는데,
실행중인 프로그램을 프로세스라고 하고, 프로세스 안에 포함되는 더 작은 단위의 작업 흐름을 스레드라고 설명하죠.

더욱 자세한 설명과 리마인드를 원하신다면 미흡하지만 [스레드](https://textobey.github.io/ios/thread_of_hardware_software/) 포스팅을 확인해주세요 :)
도움이 되었으면 좋겠네요!


## Sync/Async

스레드에 올라온 작업들이 많아도 그 작업에 소요되는 시간이 길지 않으면 한 개의 스레드에서만 일을 시켜도 크게 문제가 없을거예요.
![](https://velog.velcdn.com/images/textobey/post/4d2086ce-2523-4273-bb61-d2128c76b997/image.png)

하지만 스레드에 올라온 작업들도 많은데 그 작업에 소요되는 시간도 길어진다면?
일을 다른 스레드로 분산시켜서 주어진 스레드 자원을 효율적으로  사용하는게 좋을거예요.

![](https://velog.velcdn.com/images/textobey/post/6d384f43-42c6-4c3a-8b3c-f1bbec8156ca/image.png)




### Sync(동기)

그럼 분산시켜서 효율적으로 스레드 자원을 사용하려고 Task 1을 Thread 2로 넘겼을때!
Task 1 작업이 끝날때까지 Thread 1에서 Task 2 작업(다음 작업)을 하지 않고 기다리는 게 **Sync.**

그렇기 때문에, Sync는 기다렸다가 작업이 종료돼야 다음 작업을 시작할 수 있어요 :)

![](https://velog.velcdn.com/images/textobey/post/f02fb9cd-2d8f-420e-a024-1d30a01849af/image.png)



### Async(비동기)

작업을 분산처리 시켰을때, Thread 2에 넘긴 Task1 작업이 끝날때까지 안기다리고 다음 Task 2 작업을 하는것이 **Async.**
그렇기 때문에, 넘겨진 작업이 끝나거나 말거나 Thread 1은 다음 Task 2 작업을 시작할 수 있어요.

대표적으로 네트워크 통신에서 Async를 활용하고 있어요.(iOS 기준)
네트워크 통신이 시간이 얼마나 걸릴지 알고 다음 작업을 멈춰둘수는 없잖아요? :)

![](https://velog.velcdn.com/images/textobey/post/a77f6261-f4c5-4b8f-a218-b1cf1c2e1df7/image.png)


## Serial/Concurrent


### Serial(직렬)

분산처리를 할때 다른 **하나의 스레드**로만 작업을 분산하는 것이 Serial(직렬)이에요.
순서가 중요한 작업을 처리할때 사용해요.

![](https://velog.velcdn.com/images/textobey/post/df1de15a-17fe-423a-a674-b037472042f6/image.png)


### Concurrent(동시)

분산처리를 할때 **다른 여러개의 스레드**로 분산하는 것이 Concurrent(동시)예요.
각자 독립적이지만 유사한 여러개의 작업을 중요도나 유사한 성격을 가진 여러개의 작업을 할때 유용해요.

몇 개의 스레드로 분산할지는 시스템이 알아서 결정해요.

![](https://velog.velcdn.com/images/textobey/post/63702313-dc85-46b6-a791-86afca6a019d/image.png)



### ETC

그렇다면 Async(비동기)와 Concurrent(동시)는 같은 말일까요? 아니죠.
비동기는 다른 스레드에 작업을 넘겨 시키고, 그 작업이 끝나는것을 기다리지 않고 다음 작업을 하는것이 Async.
작업을 다른 여러개의 스레드로 분산하는것이 Concurrent.
이제 확실하게 리마인드가 되네요 :)


+) iOS 기준으로는 1번(메인) 스레드가 UI와 관련된 작업을 처리하고 있기 때문에 작업을 많이 시킬수록 좋지 않아요. 그렇기 때문에 네트워크 관련된 작업을 다른 스레드로 보내서 처리해야 해요.
