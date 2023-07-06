---
title: "RIBs LaunchRouting을 구성하며 겪은 시행착오"
excerpt: "특이한게 궁금한 청개구리가 겪은 불필요한 시행착오"

categories:
  - Issue
tags:
  - [Architecture, RIBs]

permalink: /ios/ribs-launch-routing-issue/

toc: true
toc_sticky: true

date: 2023-04-10
last_modified_at: 2023-04-21
---

RIBs 튜토리얼을 마치고 샘플 프로젝트를 작성중에 발생했던 이슈와 시행착오에 대해 포스팅 해볼까해요 :)

### 1. 이슈

LeakDetector는 메모리 누수 없이 정상적으로 UIViewController가 해제 되었는지 체크해주는 클래스인데요. 저는 Detach 하는 과정이 아닌 Attach 해주는 과정에서 발생했던 이슈였기 때문에 무엇이 문제일지 아리송했어요..

일단 RIBs 아키텍처 환경이 생소하기 때문에 RIBs에 관련된 인터페이스(프로토콜) 구현에서 실수한것이 있는지 확인해 보았으나 아니였고.. 검색을 해보아도 위에 적은것처럼 Detach 하는 과정이 아닌 Attach 해주는 과정에서 발생하고 있어서 완전 미궁으로 빠지던 찰나였어요.

**내용**
Assertion failed: <<Book_RIBs.TabBarViewController: 0x13c82da00>: -1188917217535623679> has leaked. Objects are expected to be deallocated at this time: NSMapTable

```swift
RIBs/LeakDetector.swift:102: Assertion failed: <<Book_RIBs.TabBarViewController: 0x13c82da00>: -1188917217535623679> has leaked. Objects are expected to be deallocated at this time: NSMapTable {
[6] 3387942808378348322 -> <Book_RIBs.SearchBookViewController: 0x13d216240>
[8] -2632640745828397275 -> <Book_RIBs.RootViewController: 0x13c80ce00>
[13] -1188917217535623679 -> <Book_RIBs.TabBarViewController: 0x13c82da00>
[15] 2964597816792091991 -> <Book_RIBs.NewBookViewController: 0x13d215eb0>
}

2023-04-10 17:19:17.003360+0900 Book-RIBs[36276:59669714] RIBs/LeakDetector.swift:102: Assertion failed: <<Book_RIBs.TabBarViewController: 0x13c82da00>: -1188917217535623679> has leaked. Objects are expected to be deallocated at this time: NSMapTable {
[6] 3387942808378348322 -> <Book_RIBs.SearchBookViewController: 0x13d216240>
[8] -2632640745828397275 -> <Book_RIBs.RootViewController: 0x13c80ce00>
[13] -1188917217535623679 -> <Book_RIBs.TabBarViewController: 0x13c82da00>
[15] 2964597816792091991 -> <Book_RIBs.NewBookViewController: 0x13d215eb0>
}
```

### 2. 원인

SceneDelegate부터 천천히 코드를 둘러보던 찰나에 아래처럼 LaunchRouting 타입의 launchRouter 프로퍼티를 scene(:) 메서드 내부에 선언했던 것이 이유였어요.

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(
        _ scene: UIScene,
        willConnectTo session: UISceneSession,
        options connectionOptions: UIScene.ConnectionOptions)
    {
        guard let _ = (scene as? UIWindowScene) else { return }
        
        let launchRouter: LaunchRouting = RootBuilder(dependency: AppComponent()).build()
        launchRouter.launch(from: window!)
        window?.backgroundColor = .systemBackground
    }
	...
}
```

launchRouter 프로퍼티가 전역으로 존재하지 않을 경우에는 DelegatePattern과 ARC를 고려해보았을때, scene(:) 메서드에서 내부에서만 reference count가 증가하고 scene(:) 메서드가 모두 수행되었을때 reference count가 0이 되며 인스턴스가 해제 되기 때문에 발생하는 문제라고 보고 있어요.

실제로 Router.swift에서 deinit block에 브레이크 포인트를 걸어보면 scene(:) 메서드 동작이 모두 종료되고 Root Router부터 메모리에서 해제 되는것을 확인 해볼 수 있더라구요!

```swift
	Router.swift

    deinit {
        interactable.deactivate()

        if !children.isEmpty {
            detachAllChildren()
        }

        lifecycleSubject.onCompleted()

        deinitDisposable.dispose()

        LeakDetector.instance.expectDeallocate(object: interactable)
    }
}
```

### 3. 해결


```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?
    
    private var lauchRouter: LaunchRouting?

    func scene(
        _ scene: UIScene,
        willConnectTo session: UISceneSession,
        options connectionOptions: UIScene.ConnectionOptions)
    {
        guard let _ = (scene as? UIWindowScene) else { return }
        
        let appComponent = AppComponent()
        self.lauchRouter = RootBuilder(dependency: appComponent).build()
        self.lauchRouter?.launch(from: window!)
        window?.backgroundColor = .systemBackground
    }
    
    ...
}
```

launchRouter를 SceneDelegate 전역 변수로 두고 RootBuilder의 build() 메서드를 통해 반환된 router를 할당해주도록 코드를 수정했어요.

꼭 launchRouter가 SceneDelegate 전역 변수로 존재 해야한다는 내용에 대해 서술하고 있는 문서 내용이 있을지 모르겠네요.. 알고나면 정말 간단한 실수인데 익숙하지 않은 환경에서 코드를 작성해나가면서 겪는 시행착오는 항상 있는것 같아요.
