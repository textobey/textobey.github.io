---
title: "UIButton.isSelected/isEnabled issue"
excerpt: "isSelected, isEnabled를 같이 사용하면 안될까?"

categories:
  - Issue
tags:
  - [iOS, UIButton]

permalink: /ios/uibutton-enabled-selected-issue/

toc: true
toc_sticky: true

date: 2023-07-11
last_modified_at: 2023-07-11
---

이번 포스팅은 UIButton의 `isSelected`, `isEnabled` 를 같이 사용하며 있었던
이슈를 기록해놓기 위해 작성한 포스팅이에요 :)


## 구현

### 코드

```swift
lazy var editButton: UIButton = {
    button.isSelected = false
    button.setTitle("편집", for: .normal)
    button.setTitle("전체 읽음", for: .selected)
    button.setTitleColor(.black, for: .normal)
    button.setTitleColor(.black, for: .selected)
    button.setTitleColor(.gray, for: .disabled)
    return button
}()
```


### 코드의 의도

`editButton` 은 normal 상태와 selected 상태별로 다른 문구의 타이틀을 가지고,
상태값에 따라 설정된 타이틀을 표시하는 버튼을 만드려고 했어요.

그리고 추가로, 버튼이 `isEnabled` 상태가 되면 버튼 타이틀 색상을 Black에서 Gray로
변경 될 수 있도록 의도했어요.

실제로 의도와 예상되는 동작 결과에 맞게 버튼은 상태값에 따라 타이틀 문구가 변경되고
색상 또한 Black에서 Gray로 변경되는 것을 확인했어요.


### 문제 발생


```swift
// editButton이 Tap 되었을때
editButton.rx.tap
    .withUnretained(self)
    // editButton.isSelected == false일 경우에만
    .filter { !$0.0.editButton.isSelected }
    .subscribe(onNext: { owner, _ in
    	// editButton.isSelected = true 로 변경
        owner.editButton.isSelected = true
        // editButton.isEnabled를 isAllReadable 값으로 변경
        owner.editButton.isEnabled = owner.isAllReadable
    })
    .disposed(by: rx.disposeBag)
```
`isSelected` 가 false일 때만 filter에 의해 걸러지기 때문에,
버튼의 타이틀은 현재 "전체 읽음" 상태라는 것을 의미해요.

**문제 1.**

true로 변경해주기 때문에 "편집" -> "전체 읽음" 으로 변경 되어야 하는데
"편집" 상태에서 변경되지 않아요.

하지만, 브레이크 포인트를 통해 로그를 찍어보면
`isSelected` 값은 true로 정상적으로 출력되는 것을 볼 수 있었어요.

**문제2.**

isEnabled 값에 의해 버튼 타이틀의 색상이 Gray 또는 Black으로 변경되지 않았어요.

**문제3.**

`owner.editButton.isSelected = true` 또는

`owner.editButton.isEnabled = owner.isAllReadable`

`isSelected`와 `isEnabled` 프로퍼티를 변경하는 코드 둘 중 하나를
지워주면(또는 주석) 정상적으로 타이틀 문구와 색상이 변경이 돼요.(왜죠..?)


**문제4.**

`owner.editButton.isSelected`의 값을 true가 아닌 false로 설정하는 코드에서는
정상적으로 타이틀 문구와 색상이 변경돼요.


### 해결 방법

혹시나 공식 문서나 스택오버 플로우에서 `isSelected`, `isEnabled` 를 같이 사용하면
발생하는 이슈가 있는것은 아닌지 찾아보고, 혹시 isEnabled가 먼저 동작하면서
isSelected가 동작하는 것은 아닌지 찾아보는 등..
시행착오와 검색을 반복하다가 아래와 같은 방법으로 수정하기로 정했어요!

```swift
editButton.rx.tap
    .withUnretained(self)
    .filter { !$0.0.editButton.isSelected }
    .subscribe(onNext: { owner, _ in
        owner.editButton.isSelected = true
        owner.enableEditButton(isEnabled: owner.isEditable)
    })
    .disposed(by: rx.disposeBag)
```

```swift
private func enableEditButton(isEnabled: Bool) {
    let state: UIControl.State = editButton.isSelected ? .selected : .normal
    editButton.setTitleColor(isEnabled ? .black : .gray, for: state)
    editButton.isUserInteractionEnabled = isEnabled ? true : false
}
```

isEnabled 프로퍼티를 사용하지 않고, 버튼 상태값에 따라 setTitleColor(_:) 메서드를 호출하여
타이틀 컬러값을 변경해주고 isUserInteractionEnabled 프로퍼티 값을 변경하여
버튼의 사용자 상호작용을 비활성화 했어요!

의도했던 코드가 의도했던대로 동작하지 않을 때, 항상 그 문제로 deep dive 해볼 수 있는
시간이 있다면 좋겠지만 때로는 시간적 여유가 허락되지 않을때가 있을 수 있어요.

이럴땐, 자신이 의도했던 동작은 무엇인지, 문제가 되는 것은 무엇인지, 어떻게 해결할 수 있을지를
빠르게 파악하고 해결해나가는 능력 또한 중요하다는 것을 알 수 있어요 :)
