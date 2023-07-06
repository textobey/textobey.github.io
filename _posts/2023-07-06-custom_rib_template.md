---
title: "Xcode 커스텀 템플릿 만들기"
excerpt: "RIBs 템플릿 기반의 ReactorKit을 더한 템플릿!"

categories:
  - iOS
tags:
  - [iOS, Xcode, RIBs, Architecture]

permalink: /ios/custom_rib_template/

toc: true
toc_sticky: true

date: 2023-07-06
last_modified_at: 2023-07-06
---


안녕하세요 :)

이번 포스팅에서는 기존 RIBs에서 제공하는 Xcode File 템플릿을 커스텀해서 RIBs에 ReactorKit이 도입된 형태로 스캐폴딩된 템플릿을 만들게된 과정에 대해 간략하게 남겨볼게요.

## Xcode File Template

먼저 Template의 목적은 동일한 디자인 형태를 '재사용' 하는데에 있는데요.
똑같은 이유로 Xcode 또한 프로젝트를 만드는데 수고를 덜어주고 효율을 높이기 위해 기본적으로 제공하는 파일 템플릿이 존재하고 있어요.

![](https://velog.velcdn.com/images/textobey/post/88eee660-4ef3-496b-ad65-cf8334762024/image.png)

그리고 기본적으로 제공하는 Xcode 템플릿을 수정하거나, 새로운 템플릿을 추가할 수 있는데요.

이러한점을 활용해 UI 컴포넌트나 아키텍처 패턴의 구조에 대해 새로운 템플릿을 활용하는 경우를 어렵지 않게 찾아볼 수 있어요.

그 이유는 보일러 플레이트 코드 또는 협업간에 지켜져야 하는 코드 컨벤션이 존재하는 경우 템플릿을 사용하면 파일 생성과 셋업 단계에서 매우 편리하게 사용할 수 있기 때문이에요.


### Template 경로

Xcode 템플릿은 크게 두 가지로 나뉘어요.

`File Templates` : 파일을 추가할때 사용되는 템플릿
`Project Templates` : 프로젝트를 생성할때 사용되는 템플릿

경로 : `/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/Xcode` 

맥 파인더(Finder)에서 폴더로 이동 실행(단축키: Shift + Command + G)를 활용하여 이동하면 간편해요.

### Template 필수 구성 파일

템플릿 경로에 진입하게 되면, `.xctemplate` 확장자로 존재하고 있는 폴더를 확인할 수 있어요. 그리고, 그 폴더안에서 Template을 구성하는데 꼭 필요한 파일들의 종류를 확인해볼 수 있어요.

- TemplateIcon.png : 템플릿 선택 시 템플릿의 아이콘으로 표시되는 이미지
- TemplateInfo.plist : 템플릿이 생성되는데 필요한 중요한 정보를 설정할 수 있는 plist 파일
- `___FILEBAESNAME___.swift` : 최종적으로 생성되는 swift 파일의 내용을 정의하는 swift 파일

#### TemplateInfo.plist

![](https://velog.velcdn.com/images/textobey/post/b74de9a7-cf6b-4a6c-979f-975ec1b6e575/image.png)

- Kind: File/Project 템플릿 종류를 정의
- SortOrder: 템플릿이 보여지는 순서를 정의, 하나의 그룹에는 여러 템플릿을 만들 수 있어서, 템플릿간의 순서를 정의해줄 수 있음(단, Xcode에서 기본적으로 제공하는 템플릿들의 순서보다 위로 올릴수는 없음)
- Platforms: 어떤 OS(플랫폼)에서 사용되는 것인지 정의
- DefaultCompletionName: 파일명의 Default 값
- Options: 파일 생성시 여러 옵션을 정의


## Add Custom Template

저는 RIBs 아키텍처 패턴에 ReactorKit이 적용된 템플릿을 생성할것이기 때문에, File Template을 추가해야해요.

Github [RIB.xctemplate](https://github.com/uber/RIBs/tree/main/ios/tooling/RIB.xctemplate) 에서 제공된 기본 RIB 템플릿을 활용해서 제가 필요한 내용대로 커스터마이징 해볼게요.

### TemplateInfo.plist

![](https://velog.velcdn.com/images/textobey/post/25dc3c41-2cb5-4b51-9628-426d65770920/image.png)

먼저 TemplateInfo.plist는 크게 수정한 내용 없이, 표시되는 템플릿의 이름과 더 이상 필요하지 않은 옵션값 몇 개를 삭제했어요.

### `___FILEBASEINTERACTOR___`.swift

``` swift
...

final class ___VARIABLE_productName___Interactor:
    PresentableInteractor<___VARIABLE_productName___Presentable>,
    ___VARIABLE_productName___Interactable,
    ___VARIABLE_productName___PresentableListener,
    Reactor {
    
    // MARK: - Reactor
    
    typealias Action = ___VARIABLE_productName___PresentableAction
    typealias State = ___VARIABLE_productName___PresentableState
    
    enum Mutation {
        
    }
    
    // MARK: - Properties
    
    var disposeBag = DisposeBag()

    weak var router: ___VARIABLE_productName___Routing?
    weak var listener: ___VARIABLE_productName___Listener?
    
    var initialState: ___VARIABLE_productName___PresentableState
    
    // MARK: - Initialization & Deinitialization
    
    init(presenter: ___VARIABLE_productName___Presentable, initialState: ___VARIABLE_productName___PresentableState) {
        self.initialState = initialState
        super.init(presenter: presenter)
        presenter.listener = self
    }

    override func didBecomeActive() {
        super.didBecomeActive()
        // TODO: Implement business logic here.
    }

    override func willResignActive() {
        super.willResignActive()
        // TODO: Pause any business logic.
    }
    
    // MARK: - ___VARIABLE_productName___PresentableListener
    func sendAction(_ action: Action) {
        self.action.on(.next(action))
    }
}

// MARK: - Mutate

extension ___VARIABLE_productName___Interactor {
    func mutate(action: Action) -> Observable<Mutation> {
        return Observable.empty()
    }
}

// MARK: - Reduce

extension ___VARIABLE_productName___Interactor {
    func reduce(state: State, mutation: Mutation) -> State {
        var newState = state
        //switch mutation {
        //case .someMutation:
        //    newState.property = property
        //}
        return newState
    }
}
```

RIB에서 Interactor 모듈부분이 ReactorKit이 적용되며 Reactor를 채택하여 준수하도록 수정하였기 때문에, 다른 파일들보다 가장 많은 부분이 수정된 파일이에요.

`___VARIABLE_productName___` : VARIABLE 이라는 것은 말그대로 변수라는 의미로 받아들이시면 될거 같아요. 설정된 내용에 따라 가변적으로 설정되는 부분이에요.

또 productName은 TemplateInfo.plist에서 설정했던 Identifier 값으로 이렇게 productName을 사용하게 되면, 템플릿 파일 생성시 **파일 이름**으로 설정했던 텍스트로 `___VARIABLE_productName___` 자리에 들어가게 돼요.

예를 들어, `Textobey` 로 파일 이름을 설정했다면? `___VARIABLE_productName___` 자리에는 `Textobey` 로 모두 대체되게 되는 것이죠.



### Custom Template 저장 경로

이렇게 완성된 파일 템플릿을 `~/Application/Xcode.app/` 경로에 저장하게 되면 Xcode 업데이트 시에 사라지게 되니, 각자 본인의 디바이스에 적용하는게 좋아요.

아래 경로에 새로운 템플릿의 이름을 설정하여 폴더를 생성하여 넣어주면 돼요.
경로 : `~/Library/Developer/Xcode/Templates/File Templates`


### 느낀점

전에도 MVVM 아키텍처 패턴의 보일러 플레이트 코드와 규약을 지키기 위해서 템플릿을 사용해본적은 있었는데요!

이렇게 직접 시행착오를 겪어가며 템플릿을 생성해보고, 사용해보니 템플릿의 구조에 대해 더 잘 이해가 되네요.

이번 경험을 통해서 나중에 템플릿을 사용하고 있을때, 템플릿에 대한 수정 사항이 필요해져도 어렵지 않게 대처할 수 있을거 같구요!

흥미롭고 도움이 되는 재미난 경험이었습니다 :)

## References

- [https://github.com/uber/RIBs/tree/main/ios/tooling/RIB.xctemplate](https://github.com/uber/RIBs/tree/main/ios/tooling/RIB.xctemplate)
- [https://tech.lezhin.com/2019/12/16/ios-file-template]()
- [https://velog.io/@whitehyun/iOS-Xcode-Template%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-%ED%8C%8C%EC%9D%BC%EC%9D%84-%EC%89%BD%EA%B2%8C-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0]()

