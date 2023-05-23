---
title: "URLSession"
excerpt: "공식 문서와 함께하는 URLSession"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/url_session/

toc: true
toc_sticky: true

date: 2023-05-23
last_modified_at: 2023-05-23
---

이번 포스팅에서는 iOS의 HTTP 네트워킹 인기 라이브러리인 Alamofire(혹은 Moya)의 기반이 되는 iOS에서 제공하는 기본 HTTP 네트워킹 API인 [URLSession](https://developer.apple.com/documentation/foundation/urlsession)에 대해서 포스팅 해볼게요 :)

## URLSession
![](https://velog.velcdn.com/images/textobey/post/dfdc5f4d-52d3-42e6-afd0-ec54b5dfdb79/image.png)

**네트워크 데이터 전송 작업들과 관련된 그룹을 조정하는 개체** 라고 공식 문서에서는 설명하고 있는데요.

URLSession과 URLSession와 관련된 Class들은 EndPoint에서 데이터를 다운로드하거나 업로드 하기 위한 API를 제공해주기 때문에
앱과 서버간에 데이터를 주고 받기(통신) 위해 Apple에서 제공하는 API로 이해하고 넘어갈 수 있을거 같아요.

> EndPoint: API가 서버에서 리소스에 접근할 수 있도록 가능하게 하는 URL.

### URLSessionConfiguration

![](https://velog.velcdn.com/images/textobey/post/681091fd-377d-4312-b839-4fc36ab6940c/image.png)


URLSession을 초기화(생성)하기 위해서는 URLSessionConfiguration과 Optional 값인 URLSessionDelegate와 OperationQueue가 파라미터 값으로 필요해요.

![](https://velog.velcdn.com/images/textobey/post/c1da1fe6-7448-4bba-97b0-7c6a50241b70/image.png)

URLSessionConfiguration은 URLSession에 대한 동작 및 정책을 정의하는 Configuration Object라고 설명하고 있는데요.

URLSession에 적용될 다양한 동작 및 정책이 바로 이 URLSessionConfiguration을 통해 설정할 수 있는것이죠.

> 다양한 정책과 속성 설정 값들은 공식 문서 [URLSessionConfiguration](https://developer.apple.com/documentation/foundation/urlsessionconfiguration)
을 확인해주세요 :)

그렇기 때문에, URLSession API를 통해 데이터를 업로드하거나 다운로드 할때 **URLSessionConfiguration을 만드는 것이 항상 수행해야 하는 첫번째 단계**라고 하네요.

### URLSessionDelegate

하나의 세션안에 있는 Task들은 공통 Delegate를 공유해요.
- 인증실패
- 서버로부터 데이터가 도착 했을때
- 데이터가 캐시할 준비가 되었을 때 

등등 다양한 이벤트가 발생했을때 정보를 제공하고 얻기 위해 구현해요.
만약 Delegate에서 제공하는 기능이 필요없다면 세션 생성시 nil을 전달하여 API를 사용하면돼요.


## URLSession Type

이제 위에 과정을 통해 생성된 URLSession에는 어떤 타입이 있는지 알아볼게요.

### Shared (공유 세션)

위에 설명대로 URLSession을 초기화(생성)하기 위해서 URLSessionConfiguration도 생성해주고 필요에 따라 Delegate도 생성해주는 작업이 필요하지만!

![](https://velog.velcdn.com/images/textobey/post/60c1f814-cd80-467b-8367-f3e172582751/image.png)

Apple에서는 **shared** 라는 Singleton 인스턴스를 제공해주는데요.
shared로 생성된 URLSession Singleton 인스턴스는 URLSession Task 생성을 위한 **합리적인 기본 동작을 제공**해줘요. (이미 기본으로는 충분한 완성된 URLSession)

그렇기 때문에, URLSessionConfiguration을 통한 생성 없이 코드 몇 줄만으로 간단히 URLSession API를 사용할 수 있어요.

하지만, 그 만큼 내가 원하는 작업에 맞춤으로 만들어진 URLSession이 아니기 때문에 한계점이 있어요.
- 데이터가 서버에서 도착할 때 순차적으로 데이터를 얻을 수 없어요.
- 기본 연결 동작은 크게 사용자 지정할 수 없어요.
- 인증을 수행할 수 있는 기능이 제한되어 있어요.
- 앱이 실행되고 있지 않으면 백그라운드 다운로드 또는 업로드를 수행할 수 없어요.

### Default Session (기본 세션)

기본 세션은 캐시, 쿠키, 사용자 인증 정보 데이터를 디스크에 저장 방식을 사용하고 사용자의 키체인에 자격 증명을 저장 해요.


### Ephemeral Session (임시 세션)

임시 세션은 기본 세션과 유사하지만 캐시, 쿠키, 사용자 인증 정보 데이터를 디스크에 저장하지 않고 대신 RAM에 저장해요.

세션이 만료되면 데이터가 메모리에서 자동으로 제거돼요. 또한 iOS에서는 앱이 일시 중단될 때 메모리내 캐시가 자동으로 제거되지 않지만 앱이 종료되거나 시스템이 메모리가 부족하다 판단하면 데이터가 제거될 수 있어요.

### Background Session

앱 자체가 일시 중단되거나 종료된 경우에도 HTTP 및 HTTPS 업로드 또는 다운로드를 백그라운드에서 수행할 수 있어요.


## URLSession Task Type

이렇게 생성된 URLSession(API)은 4가지 유형의 Task를 제공해요.
세션내에서 데이터를 선택적으로 업로드하거나 서버로부터 데이터를 불러오는 Task를 생성해요.


### Data Task
NSData 개체를 사용하여 데이터를 보내고 받아요. 짧고 잦은 요청을 서버와 주고 받는 경우에 사용하도록 만들어졌어요.


### Upload Task
Data Task와 유사하지만 파일의 형태로(txt, json 포맷 같은걸 얘기하는걸까?) 데이터를 보낼 수 있어요.
앱이 실행중이지 않을때도 백그라운드에서 업로드를 지원해요.


### Download Task
파일의 형태로 데이터를 불러오고 앱이 실행중이지 않을때도 백그라운드에서 다운로드와 업로드를 지원해요.


### websocket Task
RFC6455에 정의된 웹소켓 프로토콜을 사용하여 TCP 및 TLS를 통해 메시지를 교환해요.


+) Task는 suspend(일시정지) 상태이기 때문에, `resume()` 메서드를 호출하여 Task를 시작해야해요.
