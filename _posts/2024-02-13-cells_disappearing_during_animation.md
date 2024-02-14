---
title: "UITableView 애니메이션 중 셀이 사라지는 문제 트러블슈팅"
excerpt: "Cell 너 왜 사라지는거야..?"

categories:
  - Issue
tags:
  - [Issue]

permalink: /issue/cells_disappearing_during_animation/

toc: true
toc_sticky: true

date: 2024-02-13
last_modified_at: 2024-02-13
--- 



안녕하세요 =)

오늘은 y축 좌표가 위 혹은 아래로 움직이는 애니메이션 효과를 가진 View와
그 View에 따라 높이가 달라지는 TableView가 배치 되어있을때
TableView(Cell)에 발생하는 어떤 문제를 다뤄보려구해요!


## 이슈

![](https://velog.velcdn.com/images/textobey/post/cc1cec07-dafe-4b96-89f6-77d98ebde674/image.gif)

SomeView(TabBar)라는 Label이 붙어있는 View가 y축 좌표로 올라오는 상황이에요.

무언가 이상한점을 보셨나요?
애니메이션이 진행되고 있는 도중에 Cell이 한개씩 사라지는 모습이요!

TableView의 bottom 제약조건은 SomeView의 top으로 설정했기 때문에,
예상 결과로는 TableView는 애니메이션 효과에 맞추어 줄어드는 것이라고
생각했지만, 예상 결과와는 다르게 이런 문제를 확인할 수 있었어요 😵


### 원인

그렇다면, 원인은 무엇일까요?

원인을 파악하기 위해 처음에는 clipsToBounds 또는 masksToBounds
프로퍼티의 설정이 true로 되어있어서 그런것은 아닌지 확인했지만
true로 설정된곳은 없기 때문에 원인이 아녔어요!

`disappearing during animation` 를 키워드로 구글에 검색해본 결과
놀랍게도 [Stackoverflow](https://stackoverflow.com/questions/41933077/cells-disappearing-during-animation-of-uitableview)에서 관련글을 찾을 수 있었어요.

![](https://velog.velcdn.com/images/textobey/post/ca50dd5b-8641-44cb-96d0-85ae1a4fc2bf/image.png)

요약하자면...이것은 Apple의 의도된 디자인이라고 하네요..?
그렇다면..Apple의 의도된 디자인이라고..협의를 하는..식으로 마무리를

할 수도 있겠지만!!
저는 근원적인 해결책이 아니더라도 어떤 아이디어를 통해서 이 문제를 해결해보고 싶었어요!
그래서, 아이디어를 냈답니다.

### 해결 아이디어

그것은 바로 상황에 맞추어 오토레이아웃 제약조건을 유기적으로 바꿔주는것!

정리하자면 이렇습니다.

1. 초기 상태: tableView.bottom은 somethingView.top으로 유지
2. show -> dismiss: tableView.bottom은 somethingView.top으로 유지
3. dismiss -> show: tableView.bottom을 superview.bottom으로 변경
4. animation complete: tableView.bottom은 somethingView.top으로 변경

위의 과정에서 3번과 4번이 핵심인데요!
쉽게 말해 **문제가 되는 순간을 피하기 위해, 문제가 없는 제약조건으로 변경해두었다가 다시 원복**하는 거죠!

![](https://velog.velcdn.com/images/textobey/post/260a821f-f403-422f-9ee8-ac33812f9cc1/image.png)

이렇게하면, SomeView(TabBar)가 Show 애니메이션이 수행되고 있는 순간에는
TableView가 safeAreaBottom에 붙어있기 때문에, TableView의 높이가 줄어들면서
Cell이 사라지는 문제를 피할 수 있게 되는거죠!

### 정리하며

이번 포스팅이 같은 혹은 비슷한 문제를 경험하고 계신분들께 조금이나마 도움이 되었으면 좋겠습니다!

이번 글에서 다루고 있는 트러블 슈팅 과정과 코드를 [Github](https://github.com/textobey/UI-Components/tree/main/UIKitComponents/MyFoundation/ViewControllers/DecreaseTableView)에 올려두었습니다!
다만.. 이 해결 아이디어가 근원적인 해결법과 거리가 멀? 수 있고, 만능 해결책도 아니기에
하나의 작은 의견? 아이디어로 생각하고 참고해주셨으면 좋겠습니다 :)
