---
title: "UILabel LineBreakMode 트러블 슈팅"
excerpt: "UILabel에 개행 문자가 포함 됐을때 생기는 문제"

categories:
  - Issue
tags:
  - [iOS, UILabel, Issue]

permalink: /ios/uilabel_linebreakmode_issue/

toc: true
toc_sticky: true

date: 2023-07-17
last_modified_at: 2023-07-17
---

## 문제


### 의도

UILabel에는 텍스트를 표시하는 최대 줄 수를 지정하는 방법으로
`numberOfLines` 프로퍼티를 제공하고 있어요.
![](https://velog.velcdn.com/images/textobey/post/03d8cca1-8fd5-4562-beae-ab729b760410/image.png)

그리고, 만약 `numberOfLines` 에 설정한 최대 줄 수를 넘거나,
UILabel이 표시되는 사각형을 넘는 텍스트에 대해
줄 바꿈하고, 말줄임 표시를 나타내주는 `lineBreakMode` 프로퍼티 또한 제공하고 있어요.

```swift
typedef NS_ENUM(NSInteger, NSLineBreakMode) {
    NSLineBreakByWordWrapping = 0,         // Wrap at word boundaries, default
    NSLineBreakByCharWrapping,        // Wrap at character boundaries
    NSLineBreakByClipping,        // Simply clip
    NSLineBreakByTruncatingHead,    // Truncate at head of line: "...wxyz"
    NSLineBreakByTruncatingTail,    // Truncate at tail of line: "abcd..."
    NSLineBreakByTruncatingMiddle    // Truncate middle of line:  "ab...yz"
} API_AVAILABLE(macos(10.0), ios(6.0), watchos(2.0), tvos(9.0));
```

이 2개의 프로퍼티를 활용하여 UILabel에 MultiLineText를 표시하고,
5줄까지만 표시된 이후 ...(ellipsis)을 나타내주는 UILabel을 생성하려고 했어요.


### 구현


```swift
let messageDescriptionLabel = UILabel().then {
    $0.font = .systemFont(ofSize: 14, weight: .regular)
    $0.textAlignment = .left
    $0.textColor = 0x484D55.color
    $0.numberOfLines = 5
    $0.lineBreakMode = .byTruncatingTail
}
```

`Then` 라이브러리를 사용한것 외에는 정말 특이할게 없는 코드지만,
어떤 문제가 발생하는 것을 발견했어요.

![](https://velog.velcdn.com/images/textobey/post/63af0d6f-e10b-4063-9e59-6b23aca2e2b3/image.png)

이런식으로 `numberOfLines`로 지정된 최대 줄 수 위치에
`\n` (new line) 텍스트가 위치할 경우 ...(ellipsis)가 나타나지 않는 문제가 보이시나요?

### 원인

해결을 위한 검색도 해보고, 시행착오를 겪다가
Swift UILabel newLine Issue라는 키워드를 통해 접한 Stack overflow에서
가장 도움이 되는 글을 찾게 됐어요.

> 1. 개행(\n) 문자가 포함된 텍스트와 관련된 이슈이다.
> 1. iOS 15.4 미만 버전에서는 발생 하지 않는다.**(OS이슈)**
링크: [Specifying NSLineBreakMode.byTruncatingTail causes issues when displaying NSAttributedString in UILabel](https://stackoverflow.com/questions/73401681/specifying-nslinebreakmode-bytruncatingtail-causes-issues-when-displaying-nsattr)

실제로 제가 보유중인 iOS 14.7.1 버전 단말과
Xcode iOS 시뮬레이터를 통해 확인했을때는 문제가 없었어요.

### 해결

이 이슈에 관련되어서 오픈된 내용이 없고,
Apple에서도 이를 공식적으로 해결 하기 위해 열린 포럼이 없기 때문에
현재 iOS 17을 앞두고 있는 상황임에도 이 이슈를 인지하고 있는 사람은 적은거 같아요..
앞으로.. iOS 17에서 Apple이 공식적으로 대응하여 해결해줄지 지켜봐야 할 것 같아요.

아직 해결을 위한 완성된 코드를 작성해본 것은 아니지만,
제가 생각하는 해결 방향으로는 이래요.

1. 직접 지정된 최대 줄 수에 개행(\n) 문자가 위치할때
개행 문자를 ...(ellipsis) 표시로 replacingOccurrences 메서드를 활용하여 바꿔주는 방법

2.  `lineBreakMode` 프로퍼티를 사용하지 않고, 직접 로직을 구현하여 ...(ellipsis)를 표시하는 방법

하지만, 아무래도 이 방법들도 직접 구현하며 어떤 예상하지 못한 이슈가 있을 수도 있고
말과는 다르게 로직을 직접 구현하는 것이 어려울수도 있어요.

그렇기 때문에, Apple이 이 이슈를 공식적으로 대응하여 해결해주는 것이
가장 깔끔하긴 할텐데요.. 과연 언제 진행될지는 미지수네요.

> 해결 방법은 아니지만 해당 이슈를 한 눈에 확인해 볼 수 있는
> 샘플 프로젝트를 작성하여 공유드려요 :)
> Github: [UILabelNewLineTestViewController](https://github.com/textobey/UI-Components/blob/main/UIKit-Components/MyFoundation/ViewControllers/UILabelNewLine/UILabelNewLineViewController.swift)![](https://velog.velcdn.com/images/textobey/post/c109d946-3c8d-472e-b1c3-203eb9eecbb5/image.png)
