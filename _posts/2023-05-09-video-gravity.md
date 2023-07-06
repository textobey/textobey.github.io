---
title: "videoGravity 프로퍼티에 대해 알아보자"
excerpt: "PlayerLayer의 영상 비율에는 어떤 종류가 있을까"

categories:
  - iOS
tags:
  - [iOS, AVFoundation]

permalink: /ios/video-gravity/

toc: true
toc_sticky: true

date: 2023-05-09
last_modified_at: 2023-05-09
---

## videoGravity

이번 포스팅에서는 AVPlayerLayer에 표시되는 시청각 콘텐츠를 표시할 때, 시청각 콘텐츠의 비율을 조절할 수 있는 videoGravity 프로퍼티에 대해 포스팅 해볼게요 :)

> videoGravity 공식문서
> [https://developer.apple.com/documentation/avfoundation/avplayerlayer/1388915-videogravity](https://developer.apple.com/documentation/avfoundation/avplayerlayer/1388915-videogravity)

### 종류

UIView의 프로퍼티인 contentMode와 그 종류는 접해본적이 있을텐데요!
- scaleToFill
- scaleAspectFit
- ScaleAspectFill

비슷하게 AVPlayerLayer에서도 직관적으로 아래의 종류가 존재해요!
- resizeAspect(Default)
- resizeAspectFill
- resize

![](https://velog.velcdn.com/images/textobey/post/fd7ba522-c997-444f-beba-d0aa9d140850/image.png)

### 효과
- resizeAspect: **종횡비**를 유지하고 **Layer bounds의 안에 들어맞도록** 설정해요.
resizeAspect 속성은 초기값으로 AVPlayerLayer의 videoGravity를 따로 지정하지 않았다면 해당 속성으로 적용이 돼요.
<span style="color: #808080">(테스트 해보니까 단말별 해상도에 따라 자연스럽게 채워지는 경우도 있고 아닌 경우도 있는거 같아요.)</span>

- resizeAspectFill: **종횡비**를 유지하고 **Layer bounds를 모두 채워**요.
그렇기 때문에, 이 설정은 수평/수직 비율에 따라 비디오 이미지가 잘려 표시될 가능성이 있어요.

- resize: 비디오가 Layer bounds를 채우기 위해 **늘어나**요.
Layer bounds에 따라 단순히 늘어나는 것이기 때문에 비율에 맞춰 늘어나는 것이 아니라 비디오가 표시되는게 부자연스러울 수도 있어요.


### 적용 결과

|                         resizeAspect                         |                       resizeAspectFill                       |                            resize                            |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![](https://velog.velcdn.com/images/textobey/post/b1246afb-8370-4a0d-aea4-a40b8874bf54/image.PNG) | ![](https://velog.velcdn.com/images/textobey/post/50de1d9f-c091-4e08-8b53-bae2709de821/image.PNG) | ![](https://velog.velcdn.com/images/textobey/post/0e13c9ab-d6cd-417a-8fea-92e5cb4f020a/image.PNG) |


### 마무리

직접 해보니까 송출받는 영상의 비율과 그 영상을 어떻게 표시하는 것이 좋을지 커뮤니케이션을 통해서 적절한 비율로 타협하여 확정하는게 중요해 보이네요 :)
