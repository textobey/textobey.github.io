---
title: "SceneDelegate window 프로퍼티 자동 초기화"
excerpt: "스토리보드 없이 작업할때 자동 초기화 하는 방법"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/scene_delegate_window_nil/

toc: true
toc_sticky: true

date: 2023-05-29
last_modified_at: 2023-05-29
---

과거에 스토리보드 없이 프로젝트를 진행할때, SceneDelegate에서 UIWindow 객체를 생성하여 window에 할당하는 코드가 없음에도 UIWindow는 nil이 아니고 인스턴스화 되어 정상적으로 동작하는것을 보고 어떻게 그것이 가능한것인지 궁금했던적이 있었어요.

눈치채지 못하고 지나가거나 대수롭지 않게 생각하고 넘어갔을수도 있지만 궁금증이 생겼고 그것을 해소하고 싶었기에 그 프로젝트와 검색을 통하여 알게된 내용을 포스팅 해볼게요 :)


### 1. SceneDelegate


```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let _ = (scene as? UIWindowScene) else { return }
        // window 객체를 생성하여 할당하지 않았음에도 window가 nil이 아님
        window?.backgroundColor = .systemBackground
        window?.rootViewController = ViewController()
        window?.makeKeyAndVisible()
    }
    
    ...
}
```
window의 backgroundColor와 rootViewController를 설정하고 keyWindow로 만들고 사용 가능한 상태로 만들어주는 메서드가 정상적으로 수행되기 위해서는 당연하지만 window가 nil이어서는 안돼요.

어떻게 이것이 가능하게 할 수 있을까요?

### 2. 방법
![](https://velog.velcdn.com/images/textobey/post/4069f58c-a142-4df1-837c-d6c15955a2fd/image.png)바로 Info.plist에서 Main으로 설정되어있던 Storyboard Name Key를 제거하지 않고 Main에서 LaunchScreen으로 변경하면 끝이에요!


### 3. 이유
처음으로 프로젝트를 생성하였을때 SceneDelegate에 func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) 메서드에 자동 생성된 주석을 확인 해보셨던적이 있으신가요?

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
        // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
        // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).
    }
    
    ...
}
```
바로 2번째줄에서 아래와 같이 이유를 알 수 있는 설명이 존재하고 있네요 :)
If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
