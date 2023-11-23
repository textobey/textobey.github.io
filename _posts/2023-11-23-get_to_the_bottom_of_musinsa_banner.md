---
title: "무신사 배너 파헤쳐보기!"
excerpt: "어떤 아이디어가 녹아져있을까"

categories:
  - iOS
tags:
  - [iOS, UI]

permalink: /ios/get_to_the_bottom_of_musinsa_banner/

toc: true
toc_sticky: true

date: 2023-11-23
last_modified_at: 2023-11-23
--- 



이번 포스팅은 이전에 처음 봤을때부터 나중에 시간나면  
따라 만들어봐야지..봐야지..하고 생각해두었던 무신사 배너를 클론 코딩해보고  
또, 그 안에 어떤 비법?ㅎ 아이디어가 있는지 분석해보는 글입니다!

### 무슨 배너길래?

<img src="https://velog.velcdn.com/images/textobey/post/4a14f996-13b6-41af-a21b-11627ca72470/image.gif" width="443" height="960">

**무신사 배너**

UIScrollView 또는 UICollectionView를 활용하여 만들게 되는  
일반적인 페이징 UI와는 다르게 페이징시에 현재 페이지와 다음 페이지가  
공존?하며 이미 다음 페이지가 밑에 깔려있는듯한 독특한 느낌을 주고 있어요.

<span style="color:gray">이렇게 재밌고 신기한 UI를 만들면 따라 만들어보고 싶은 마음이..생겨버려..</span>


### 비법 혹은 아이디어

여러가지 삽질을 반복하며, 실패하기를 몇번인가..
다음에 해보려던 찰나에 [MUSINSA-banner-CloneCoding](https://github.com/oneoneoneoneoneoneone/MUSINSA-banner-CloneCoding)에서 아주 좋은 결과물을 얻어서, 그 안의 소스들을 하나하나 뜯어본 결과!

핵심적인 비법을 2개로 줄여보았습니다.

#### Part1. func scrollViewDidScroll(_ scrollView: UIScrollView)

아래 로직에서는 `scrollView.contentOffset.x - currentCell.frame.origin.x` 연산이 가장 이해가 되지 않았는데요,  

이 연산을 하는 이유는 **다음Cell의 imageView의 위치를 다음Cell의 superView의 originX(시작점)보다 더 앞으로 끌고오기 위해**서 입니다!

superView의 originX(시작점)보다 더 앞으로 끌고오게되면 현재Cell에서 다음Cell의 이미지를 보여주는게 가능해지기 때문이에요.

```swift
if collectionView.visibleCells.count > 0 {
    let currentCell = collectionView.visibleCells[0] as! HomeBannerCell
    currentCell.imageView.frame.origin.x = scrollView.contentOffset.x - currentCell.frame.origin.x
    
    if collectionView.visibleCells.count > 1 {
        let nextCell = collectionView.visibleCells[1] as! HomeBannerCell
        nextCell.imageView.frame.origin.x = scrollView.contentOffset.x - nextCell.frame.origin.x
    }
}

```

#### Part2. ClipsToBounds

Part1의 연산을 했다고해서, 실제로 무신사 배너와 같은 효과가 되도록 만들수는 없어요.

그렇다면, 어떤 비법이 또 있는걸까 분석해본결과 Cell의 ClipsToBounds 속성을 true로 변경해주는 것이었는데요.
ClipsToBounds 속성까지 true로 설정하면 똑같은 효과를 가지게 되더라구요.

사실 분석을 하면서 이 부분이 가장 이해가 안갔는데,  
조금 더 분석을 해보면서 혹시라도 더 알게되는게 있다면 추가로 정리해보도록 할게요!
