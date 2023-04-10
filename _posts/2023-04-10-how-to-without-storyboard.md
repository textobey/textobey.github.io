---
title: "프로젝트를 Storyboard 없이 작업하는 방법"
excerpt: "Storyboard 없이 Code Base로만 작업하는 방법을 알아보자"

categories:
  - Swift
tags:
  - [Swift, Xcode]

permalink: /ios/how-to-without-storyboard/

toc: true
toc_sticky: true

date: 2023-04-10
last_modified_at: 2023-04-10
---

프로젝트에 따라 코드베이스로 UI를 생성하고 NSLayoutContraint 클래스를 사용하거나(대표적으로 SnapKit 라이브러리를 사용해서) 스토리보드를 사용하지 않지 위해 스토리보드 사용에 관련된 설정값을 지워주는 경우가 있는데요 :)

프로젝트를 처음 생성하였을때를 기준으로 어떻게 설정하면 이것이 가능할지에 대해 포스팅 해볼게요.

⚠️**아래 포스팅 내용은 Xcode14 버전 이상일 경우 해당되는 내용이에요. Xcode13 버전 미만은 설정 방식이 달라요.**

### 1. Main.Storyboard 파일 제거
![](https://velog.velcdn.com/images/textobey/post/45ca1d46-6863-48a0-9e11-fc97b192badf/image.png)프로젝트내에 존재하는 **Main**이라는 이름을 가진 Storyboard를 제거해요.


### 2. Info.plist Key 제거
![](https://velog.velcdn.com/images/textobey/post/63a392b2-4f3e-409d-8928-ffa72f0c6203/image.png)Info.plist에 있는 **Storyboard Name** Key를 제거해요.



### 3. 프로젝트타겟 Key 삭제
![](https://velog.velcdn.com/images/textobey/post/10e994ff-67ae-4ff6-9e46-7a8fb3776feb/image.png)프로젝트 Targets에 Info로 이동하여 Custom iOS Target Properties 중 **Main storyboard file base name**을 제거해요.


### 4. SceneDelegate.swift 수정
```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(
        _ scene: UIScene,
        willConnectTo session: UISceneSession,
        options connectionOptions: UIScene.ConnectionOptions)
    {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = ViewController()
        window?.makeKeyAndVisible()
    }
    
    ...
```
위처럼 scene(:) 메서드까지 수정해주면 스토리보드 없이 코드만으로 프로젝트를 진행하기 위한 초기 설정을 끝마친거예요 :)
