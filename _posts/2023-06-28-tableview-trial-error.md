---
title: "UITableView 시행착오"
excerpt: "UITableViewCell의 isSelected를 관리하는법"

categories:
  - RxSwift
tags:
  - [RxSwift, iOS]

permalink: /ios/tableview-trial-error/

toc: true
toc_sticky: true

date: 2023-06-28
last_modified_at: 2023-06-28
---

안녕하세요 :)

이번 포스팅에서는 아래 그림처럼 TableViewCell을 여러개 선택하는 것이 가능하고, 전체 선택 버튼을 통해 모든 Cell을 선택할 수 있는 기능을 가진 UITableView를 구현하며 겪은 시행착오를 적어볼게요!

![](https://velog.velcdn.com/images/textobey/post/f5d232c4-ce7f-4912-866e-4d06b7400ad6/image.png)


## 구현 과정

처음에는 allowsMultipleSelection을 활성화 하고,
UITableViewCell의 `var isSelected: Bool` 프로퍼티 또는 `setSelected(_:)` 메서드를 override 하여 상황에 맞춰 UI를 업데이트 하려고 했는데요.

### 문제 발생

문제는 전체 선택 기능을 구현하면서 발생했어요.

실제로 사용자가 Cell을 선택했을때는 `tableView(_:didDeselectRowAt:)` Delegate 메서드가 호출되고, 이에 따라 RxSwift의 `tableView.rx.itemSelected` 옵저버블 스트림에 이벤트가 발생하는 구조가 돼요.

> RxSwift의 **tableView.rx.itemSelected** 는 **tableView(_:didDeselectRowAt:)** 메서드가 호출되는 시점에
> DelegateProxy를 통해 생성(이벤트가 발생)되는 구조예요.


하지만, 실제로 사용자가 UITableViewCell을 선택하는 경우가 아닌
개발자가 작성한 코드( isSelected 프로퍼티 값 변경 또는 setSelected 메서드 호출)에 의해 cell이 선택되는 경우에는 `tableView(_:didDeselectRowAt:)` 메서드를 통하지 않기 때문에, `tableView.rx.itemSelected` 이벤트 구독을 통한 동작은 수행되지 않는 문제가 있었어요.

물론 직접 TableView의 Delegate에 접근하여 `tableView(didSelectRowAt:)` 메서드로 대체하고, 호출하는 방법을 통하면 문제는 해결되지만 Delegate 메서드를 직접 접근하여 호출하는 것은 일반적인 솔루션이 아니라고 생각했어요.

> 여담으로, 이것이 괜찮은 해결책일지 궁금해져서 챗GPT에 물어봤어요!
> 재미로만 봐주세요:)![](https://velog.velcdn.com/images/textobey/post/d189f0a2-ff68-43fb-a356-a080ae815260/image.png)


### 해결 과정

TableView의 allowsMultipleSelection을 비활성화로 변경하고,
`var isSelected: Bool` 프로퍼티,`setSelected(_:)` 메서드를 override 하는 방법에서 직접 UITableViewCell의 선택 및 해제를 괸리하는 프로퍼티와 메서드를 생성하였어요.

그리고, 이를 활용해서 `tableView(_:didDeselectRowAt:)` 메서드에서 직접 생성한 프로퍼티와 메서드를 활용하여 UI의 상태값을 정하고, 업데이트 하는 방식으로 리팩토링 해봤어요.

이렇게 수정했을때, Super class의 프로퍼티나 메서드에 의존된 구조를 탈피하고
코드의 흐름과 동작 파악이 쉽도록 바뀌었고, `tableView.rx.itemSelected` 구독을 통한 이벤트 처리 역시 정상적으로 수행되도록 수정되었어요.

물론, 이 방법도 최선의 방법이 아닐 수 있어요.
하지만 이런 고민과 개선 경험은 꾸준히 쌓아가는게 중요하다고 생각해요 :)
