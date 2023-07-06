---
title: "UITableViewAlertForLayoutOutsideViewHierarchy 경고"
excerpt: "경고가 발생하는 원인과 해결 방법은?"

categories:
  - Issue
tags:
  - [RxSwift, iOS]

permalink: /ios/rxswift-tableview-layout-issue/

toc: true
toc_sticky: true

date: 2023-06-27
last_modified_at: 2023-06-27
---

안녕하세요 :)

이번 포스팅에서는 RxSwift가 발생시키는 UITableViewAlertForLayoutOutsideViewHierarchy 경고에 대해 남겨보려고 해요.


## 문제

RxSwift를 활용하여 스트림을 tableView에 바인딩 하는 크게 특이할것 없는 코드를 작성하였어요.

그런데, Xcode 로그창에서는 아래 내용의 경고 로그가 빈번하게 올라와서 이를 해결하기 위해 경고 메시지의 가이드대로 **UITableViewAlertForLayoutOutsideViewHierarchy**에 대해 Symbolic breakpoint를 생성하여 디버깅을 시도해보았어요.

> Warning once only: UITableView was told to layout its visible cells and other contents without being in the view hierarchy (the table view or one of its superviews has not been added to a window). This may cause bugs by forcing views inside the table view to load and perform layout without accurate information (e.g. table view bounds, trait collection, layout margins, safe area insets, etc), and will also cause unnecessary performance overhead due to extra layout passes. Make a symbolic breakpoint at UITableViewAlertForLayoutOutsideViewHierarchy to catch this in the debugger and see what caused this to occur, so you can avoid this action altogether if possible, or defer it until the table view has been added to a window. Table view: ...


## 원인

생성한 Symbolic breakpoint는 디버깅 결과 RxCocoa Pod에 존재하는 DelegateProxyType.swift의 layoutIfNeeded() 메서드 라인에 걸리게 되었어요!
![](https://velog.velcdn.com/images/textobey/post/2544a976-5866-4ba7-8d8e-177d6c6bd1c4/image.png)

저는 아마 이것이 View Bounds가 설정되는 생명주기 시점인 viewWillLayoutSubviews를 지나기전에 layoutIfNeeded 메서드를 호출하였기 때문에 발생했다고 추측하고 있어요.

실제로 viewWillLayoutSubviews 시점 이후에 데이터 바인딩을 수행하였을때는 해당 이슈가 발생하지 않았어요!

## 해결

보다 근본적으로 이슈를 해결하고 싶었기에, RxSwift Github로 들어가 이슈를 검색해보니 이미 관련 내용으로 많은 이슈가 오픈되어 있었어요.

그리고, 아래 코드와 같은 방어로직이 추가되어 RxSwift 6.0.0 버전부터 머지되어 배포되었기 때문에, RxSwift 6.0.0 버전부터는 해결된 문제라고 하네요.

```swift
if object.window != nil {
	object.layoutIfNeeded()
}
```


## Reference
- [https://github.com/ReactiveX/RxSwift/pull/2076](https://github.com/ReactiveX/RxSwift/pull/2076)
- [https://qiita.com/shou8/items/1d3454d176865adb457f](https://qiita.com/shou8/items/1d3454d176865adb457f)
