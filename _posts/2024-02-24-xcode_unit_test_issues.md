---
title: "Xcode Unit Test를 사용하며 접하는 문제들"
excerpt: "에러에 막혀 테스트 코드조차 못돌려보는건가..?"

categories:
  - Issue
tags:
  - [Issue, UnitTest]

permalink: /issue/xcode_unit_test_issues/

toc: true
toc_sticky: true

date: 2024-02-24
last_modified_at: 2024-02-24
--- 



오늘은 XCode Unit Test Bundle을 생성하여, 테스트 코드를 작성하여 테스트를 진행할때 인식하지 못하고 지나치거나 실수로 인하여 생기는 문제들과 해결법을 남겨볼까 합니다!
> (등잔 밑이 어둡다는 말처럼.. 오히려 이런것들이 더 원인을 못찾고 헤매게 되더라구요 🥲)

## 첫번째 이슈

테스트 코드 실행이 아닌, 앱 빌드(실행)시에 아래 에러 로그와 함께 빌드에 실패하는 문제

```swift
dyld: Library not loaded: @rpath/XCTest.framework/XCTest
```

![](https://velog.velcdn.com/images/textobey/post/9c42250f-ed81-4a07-8a86-8081ac57cee1/image.png)

### 해결 방법

Project -> Targets -> 앱 메인 타겟 선택 -> Build Phases -> Link Binary With Libraries에 테스팅에 관련된 모든 프레임워크와 XCTest.framework 제거

![](https://velog.velcdn.com/images/textobey/post/35fa0087-c758-4082-a216-5be400e41e51/image.png)


## 두번째 이슈

```swift
ld: warning: Could not find or use auto-linked library 'XCTestSwiftSupport'
ld: warning: Could not find or use auto-linked framework 'XCTest'
```

### 해결 방법

Project -> Build Settings -> Build Options에서 Enable Testing Search Paths Key 값을 Yes(true)로 변경


## 세번째 이슈

**UITest Bundle**에 **@testable import** 'Module'을 하게 되면 발생하는 문제

```swift
/// Error log lists
Undefined symbol: _$s12MyOceanTests04DeepB9ViewModelC14networkService05cloudH0AcA010AnyNetworkH0_p_AA0jH0_ptcfC
Undefined symbol: _$s12MyOceanTests04DeepB9ViewModelCMa
Undefined symbol: _$s12MyOceanTests12CloudServiceCAA03AnyE0AAWP
Undefined symbol: _$s12MyOceanTests12CloudServiceCACycfC
Undefined symbol: _$s12MyOceanTests12CloudServiceCMa
Undefined symbol: _$s12MyOceanTests14NetworkServiceCAA03AnydE0AAWP
Undefined symbol: _$s12MyOceanTests14NetworkServiceCACycfC
Undefined symbol: _$s12MyOceanTests14NetworkServiceCMa
Linker command failed with exit code 1 (use -v to see invocation)

```

![](https://velog.velcdn.com/images/textobey/post/2190b128-d3f0-4b8e-b4b9-37381c9cbe40/image.png)


### 해결방법

위의 원인에서도 알 수 있듯이 **UITest**에서는 **@testable import**가 작동하지 않기 때문에, UITest에서 사용하지 않으면 됩니다!

이것은 Apple의 설계 의도로 UITest는 별도의 프로세스로 앱 외부에서 실행되기 때문에,
UITest에서 앱 코드에 접근 할 수 없게 해두었다고 하네요.

저는 UITest에서 @testable import를 하려던건 아니고, [swift-snapshot-testing](https://github.com/pointfreeco/swift-snapshot-testing) 라이브러리를
한번 사용해보고 싶었던건데, Unit Test  번들을 생성했어야 하는데.. UITest 번들을 생성해버리는 바람에
'이런 이슈와 원인이 있구나~' 하고 우연하게 알고 갈 수 있었네요!

![](https://velog.velcdn.com/images/textobey/post/50d7d5d9-38c2-48df-aa04-c74c08bef58d/image.png)
