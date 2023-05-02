---
title: "View Drawing Cycle"
excerpt: "뷰가 그려지고 화면에 나타나는 과정"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/view-drawing-cycle/

toc: true
toc_sticky: true

date: 2023-04-27
last_modified_at: 2023-05-02
---

이번 포스팅에서는 View Drawing Cycle이 무엇인지 복습하고 기록하기 위해 간략하게 작성해볼게요 :)

## View Drawing Cycle

언젠가 한번은 View들이 어떻게 화면에 나타나(그려지)는지 궁금한적이 있으시지 않나요? 저도 그런 궁금증에 View Drawing Cycle에 대해 찾아보았던 기억이 나네요

**View Drawing Cycle**은 뷰가 처음 로드 되었을때나 무언가 변경 사항이 있을때 그것을 화면에 적용해(그려)주는 과정으로 보면 될거 같아요.

### 순서
1. View가 화면에 로드되면, 시스템이 UIView에게 draw(_:) 메서드를 통해 드로잉을 요청해요.
2. 시스템은 이 컨텐츠의 스냅샷을 캡쳐해서 View의 시각적 표현으로 사용해요.
3. View의 컨텐츠가 변경되면 시스템에 업데이트를 요청해요.(setNeedsDisplay, setNeedsLayout, displayIfNeeded, layoutIfNeeded 메서드를 호출하여 요청함)
4. 다음 Drawing Cycle이 되면 업데이트 요청을 받았던 View를 업데이트해요. 위의 2번에 해당하는 과정을 반복하는 것이죠!


> Q. 다음 Drawing Cycle은 언제 오는건가요?
> A. Main Run Loop의 마지막 단계에서 Update cycle이 수행돼요. 이 Update Cycle에서 시스템은 Layout(Size, Position), Display(Color, text, image 등), Constraints를 업데이트하는 작업을 수행해요.
> ![](https://velog.velcdn.com/images/textobey/post/01d1f6e5-30fa-46a5-b997-a629285efef6/image.png)
> Q. 왜 그때 그때 업데이트 하지 않고, Update Cycle을 기다리나요?
> A. Update Cycle에서 호출되는 메서드는(updateConstraints(), layoutSubviews(), draw()) 고비용의 작업이기 때문에, 매번 그때 그때 바로 업데이트 하는것이 효율적이지 못하기 때문인것으로 보여요. Apple에서는 이런 고비용 메서드를 한번에 UpdateCycle에서 처리되도록 설계했어요.


만약, Run Loop에 대해 알고 싶다면 [Run Loop](https://textobey.github.io/ios/run-loop/) 포스팅을 확인해주세요 :)


### Update를 요청하는 방법

아래 3가지 업데이트 요청 메서드들을 수행하면 명시적으로 시스템에 View 업데이트를 요청할 수 있어요. 

아래 메서드를 요청하면 업데이트를 요청하는 예약이 되고, 시스템은 이를 체크해두었다가 다음 Update Cycle에서 updateConstraints(), layoutSubviews(), draw() 메서드를 수행해요.

- setNeedsUpdateConstraints()
	: 다음 Update Cycle에서 Constraints에 대한 갱신이 일어나도록 예약하는 메서드

- setNeedsLayout()
	: View Layout에 대한 갱신이 일어나도록 예약하는 메서드

- setNeedsDisplay()
	: View 요소들을 다시 그려야 한다는 (draw(_:)) 예약을 메서드
  
> 굳이 요청하지 않아도 Update 요청이 되는 상황으로는 아래 같은 상황이 있어요!
- View의 크기 변경, 이동 또는 제거
- Subview 추가 또는 isHidden 프로퍼티를 true -> false
- ScrollView에서 View를 화면밖으로 스크롤하고 다시 View로 이동
- View Constraints 변경
- Device 가로 또는 세로 회전


추가로, 예약 요청을 하는법 말고 즉시 업데이트 할 수 있는 방법도 있어요.

- updateConstraintsIfNeeded()
- layoutIfNeeded()
- displayIfNeeded()


위의 메서드를 사용하면 UpdateCycle에서 수행되는 메서드들을 직시 호출하여 View를 갱신할 수 있어요.


### 마무리

UpdateCycle에서 수행되는 메서드들을 직접 호출하는 것은 피하고, View 업데이트 예약 요청 또는 즉시 요청 메서드들을 상황에 따라 잘 나누어 사용할 줄 알아야 겠네요 :)...


### References
 - [https://developer.apple.com/library/archive/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html](https://developer.apple.com/library/archive/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html)
- [https://velog.io/@mmim/iOS-View-Drawing-Cycle](https://velog.io/@mmim/iOS-View-Drawing-Cycle  )
- [https://green1229.tistory.com/67](https://green1229.tistory.com/67  )
- [https://zeddios.tistory.com/359](https://zeddios.tistory.com/359)
- [https://inuplace.tistory.com/1137](https://inuplace.tistory.com/1137)
