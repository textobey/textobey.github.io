---
title: "UINavigationController에서 자식 RIB를 detach 하는 방법"
excerpt: "어떻게하면 자식 RIB을 정상적으로 detach 시킬 수 있을까"

categories:
  - Architecture
tags:
  - [Architecture, RIBs]

permalink: /ios/how-to-detach-child-when-popviewcontroller/

toc: true
toc_sticky: true

date: 2023-04-18
last_modified_at: 2023-04-18
---


RIBs 아키텍처를 활용한 샘플 프로젝트를 작성중에 LeakDetector 클래스를 통해 Child RIB의 ViewController가 정상적으로 인스턴스가 해제되지 않고 메모리 누수를 발생 시키는것을 알게 되었어요.

## 원인

여태까지 접해본 디자인 아키텍처에서는 **popViewController(_:)** 메서드가 실행되는 일반적인 뒤로가기 버튼 탭, 스와이프 백 동작시에 ViewController 인스턴스가 해제되며 Deinit 수순을 밟게 되었지만!

RIBs에서는 자녀 RIB를 부모 RIB에서 소유하고 있는 형태이기 때문에, 자녀 Interactor에서 listener 프로토콜을 통해 부모 Router에게 자신을 detach(전환) 시켜달라는 요청을 보내고, 부모 Router에서 비로소 detachChild() 메서드와 자녀 Router의 인스턴스를 nil로 만들고 **popViewController(_:)** 메서드 동작이 수행되어야 Deinit 수순을 밟을 수 있어요.

## 해결 방법

1. ViewController로부터 popViewController(_:) 시점에 Interactor에게 이벤트 전달
2. Interactor에서 부모 Router에게 detach 요청
3. 부모 Router에서 자식 router를 detachChild() 하고, router 프로퍼티에 nil 할당
4. viewControllable 프로토콜로 viewController에 접근하여 self.navigationController.popViewController(_:) 메서드 수행


## 추가 팁

ViewController에서 뒤로가기 버튼 탭 또는 스와이프 백 시점을 감지하는 방법
```swift
    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        guard isMovingFromParent || isBeingDismissed else { return }
        // Interactor에게 detach를 요청하는 Trigger 작성
    }
```
