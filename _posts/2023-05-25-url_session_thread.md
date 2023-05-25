---
title: "URLSession Task Thread"
excerpt: "URLSession의 작업은 어느 스레드에서 수행될까?"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/url_session_thread/

toc: true
toc_sticky: true

date: 2023-05-25
last_modified_at: 2023-05-25
---

안녕하세요!
오늘은 찾아보며 은근히 재밌었던 내용을 공유해볼까 해요 :)

## 궁금증

저번 [Sync/Async, Serial/Concurrent](https://velog.io/@textobey/syncasyncserialconcurrent) 포스팅에서 마지막에 1번(메인)스레드는 UI와 관련된 작업을 처리하고 있기 때문에, 네트워크 관련된 작업을 다른 스레드로 보내서 처리해야 한다고 말하며 마무리 했었는데요!

URLSession을 활용하여 서버와 통신할때 따로 DispatchQueue를 네트워킹 작업이 메인 스레드가 아닌 다른 스레드에서 처리되도록 넘긴적이 없는데 따로 문제가 없는것을 보고

별다른 문제가 없는 건가? 그렇다면 이유가 뭘까 궁금해서
URLSession의 Task가 어떤 스레드에서 처리되는지 좀 찾아봤는데 잘 안나오더라구요...
그러다 문득 공식 문서를 둘러보는데 정답에 가까운 힌트를 얻을 수 있었어요!


## 힌트

[Fetching Website Data into Memory](https://developer.apple.com/documentation/foundation/urlsessiondatatask) 공식 문서로 이동하여 밑으로 내리다보면

```swift
func startLoad() {
    let url = URL(string: "https://www.example.com/")!
    let task = URLSession.shared.dataTask(with: url) { data, response, error in
        if let error = error {
            self.handleClientError(error)
            return
        }
        guard let httpResponse = response as? HTTPURLResponse,
            (200...299).contains(httpResponse.statusCode) else {
            self.handleServerError(response)
            return
        }
        if let mimeType = httpResponse.mimeType, mimeType == "text/html",
            let data = data,
            let string = String(data: data, encoding: .utf8) {
            DispatchQueue.main.async {
                self.webView.loadHTMLString(string, baseURL: url)
            }
        }
    }
    task.resume()
}

```

![](https://velog.velcdn.com/images/textobey/post/5963c190-7506-489d-b25d-f7affa8933c9/image.png)

이런 내용을 확인할 수 있는데요! <span style="color: #808080">(무려 Important..)</span>
내용을 좀 해석해보면 CompletionHandler는 Task를 생성한 것과 다른 GCD Queue에서 호출되기 때문에, UI를 사용하거나 업데이트하는 작업은 위 코드에서 명시된것처럼 main queue에 위치해야 한다는 것인데요.

바로 CompletionHandler는 Task를 생성한 것과 다른 GCD Queue에서 호출된다.
부분에서 정답과 다름 없는 힌트를 얻게 됐어요.

GCD Queue에는 Main, Global, Custom Queue가 있는데 위 코드를 보면 따로 Task가 Global이나 Custom에 위치하도록 명시하는 코드가 없으니 당연히 Main Queue라고 유추할 수 있는거죠!

## 해소


URLSession을 활용하여 서버와 통신할때 따로 DispatchQueue를 네트워킹 작업이 메인 스레드가 아닌 다른 스레드에서 처리되도록 넘긴적이 없는데 따로 문제가 없는 이유

-> URLSession의 Task 내부는 생성한 곳과 다른 DispatchQueue에서 호출되기 때문에 메인 스레드가 아닌 다른 스레드에서 처리됨


### 테스트

`Thread.isMainThread` 프로퍼티를 활용하여 Task의 CompletionHandler 내부와 외부 위치에서 현재 스레드가 메인 스레드인지 확인

![](https://velog.velcdn.com/images/textobey/post/71800aac-dbf9-4508-98ef-242011500026/image.png)

![](https://velog.velcdn.com/images/textobey/post/57002e0a-d88e-47ba-9601-0f9ac21477c3/image.png)
