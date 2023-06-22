---
title: "Scale Factor"
excerpt: "px, pt 그리고 Apple의 Scale Factor"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/scale_factor/

toc: true
toc_sticky: true

date: 2023-06-22
last_modified_at: 2023-06-22
---

이번 포스팅에서는 [App Icon Generator](https://www.appicon.co/#image-sets) 웹사이트를 통해서 @2x, @3x 이미지를 생성하는 과정중에 Apple의 HIG 중 Scale factors에 관련된 내용과 발전 과정?을 포스팅 해보면 재밌을거 같아서 준비해보았어요 :)

## 배경

먼저 어떤 과정을 통해 Apple에서는 @1x, @2x, @3x로 Assets을 나누게 되었고, 또 각 배율은 어떤 의미를 가지고 있는지 짚고 넘어가야 할거 같아요.

### pixcel(px)
: 이미지를 이루는 가장 작은 단위의 사각형 모양의 점(화소 picture + element)

모니터의 광고나 스펙 같은걸 보면 몇 픽셀이고 해상도는 무엇이고 하는 이야기를 보거나 들으신적이 있으실거 같아요.

픽셀은 **디스플레이에서 가장 기본이 되는 단위**예요. 모니터나 아이폰 등 화면을 정말 가까이 들여다보면 수많은 사각형 모양의 점이 모여 이루어진걸 볼 수 있어요.

예를 들어 FHD(1920x1080) 해상도의 모니터가 있다면 이것은 픽셀의 개수가 1920 * 1080개가 존재한다는 의미예요.

이런식으로 요즘 디스플레이에는 FHD < QHD < UHD(4K)등 정말 많은 개수의 픽셀이 존재해요.

이 픽셀이 얼마나 오밀조밀하게 모여있는지 밀도를 나타내는 단위가 있는데요.

- **ppi(pixcel per inch)**: 1인치에 n개의 픽셀이 존재
    - (ex. 72 ppi): 1인치에 72개의 픽셀이 존재한다는 의미
- **dpi(dot per inch)**: 1인치당 점의 개수
    - 주로 인쇄물에서 사용되는 단위
    오늘날에 와서는 ppi와 서로 다른 개념이지만 혼용되어 차이없이 사용됨
    

### point(pt)

: 타이포그래피에서 사용하는 가장 작은 인쇄 단위.
pt는 해상도에 따라 더 선명하거나 흐려질 수 있지만, 크기가 달라지거나 변하지 않아요.

Apple에서는 과거에 이 인쇄에 쓰이던 pt를 모니터상에서도 똑같은 크기로 가져오기 위해서
1pt가 1/72inch(약 0.3527mm)인것에 착안하여 72ppi의 규격을 채용하여 OS에 적용하였어요.

이로 인해, **72ppi를 가진 디스플레이에서는 1pt=1px**가 되는것이죠.


### 발전

기술력이 발전하고 더 높은 해상도, 더 밀도 높은 PPI의 디스플레이가 출시됨에 따라 72ppi에서 220ppi, 270ppi, 300ppi로 계속 높아져갔어요.

그리고 여기서 **300ppi가 넘을 경우**에는 사람의 눈으로 보이지 않을 수준의 고밀도 디스플레이가 되고, **Apple**에서는 이것을 **Retina Display**라고 정의하여 마케팅 하였어요.

이렇게 점점 고해상도가 되어가는 Display를 Device에 탑재해가면서, 더 이상 1pt=1px의 환경만이 아닌 1pt=4px, 1pt=9px로 더 높은 밀도에 대응하기 위한 환경이 필요해졌어요.

결론적으로 절대적으로 px과 pt는 똑같은 사이즈가 아니고,
1인치당 픽셀이 얼마나 밀집되어 있는지에 따라(ppi) 달라질 수 있어요.

## Scale factors

Apple에서는 지원하는 모든 Device에서 artwork가 훌륭하게 보이도록 하려면 아래 2가지 방법에 대해서 알아야 한다고 설명하고 있어요.

- 시스템이 화면에 콘텐츠를 표시하는 방법
- 적절한 Scale factors로 art를 제공하는 방법

![](https://velog.velcdn.com/images/textobey/post/4055fe29-a32c-48ef-aa92-16d84d37ba33/image.png)

Image HIG에서는 iOS를 포함한 Apple의 모둔 플랫폼은 px에 매핑되는 pt 측정값을 기반으로 하는 좌표계를 사용해요.

표준 해상도 디스플레이는 1px=1pt 픽셀 밀도를 가지고 있어요.(= @1x)
@1x, @2x, @3x는 각각 픽셀의 배율을 의미로 아래와 같은 의미를 가져요.
 - @1x: 1pt에 1배의 픽셀이 담긴다.
 - @2x: 1pt에 2배의 픽셀이 담긴다.
 - @3x: 1pt에 3배의 픽셀이 담긴다.

### Scale factors를 통해 얻는 편의성

ppi에 따라 px과 pt의 크기가 달라질 수 있다고 정리했었는데요!

그렇기에 고해상도의 디스플레이는 픽셀의 밀도가 높기 때문에, 더 많은 픽셀이 포함된 이미지가 필요해요.

Asset에 @1x, @2x, @3x에 각각 해당하는 이미지를 넣어주면, Apple은 Scale factors 개념을 통해 Device별 ppi에 따라 적절한 art를 제공할 수 있도록 개발자에게 편의를 주고 있는것이죠!

Device의 pt에 맞춰 어떤 배율이 적용될지에 대해 정해져 있는데요.
이 배율을 통해 pt에서 px로 변환이 되기 때문에 개발자는 pt 단위에만 신경쓸 수 있는 환경이 이루어지죠.

이렇게 pt 단위를 이용하면 면적당 픽셀 수(ppi)에 관계 없이 같은 크기의 콘텐츠를 표시하는 화면을 제공할 수 있어요. <span style="color: #808080">(View나 Font크기 등을 다룰 때 pt를 단위로 사용하는 이유)</span>

+) 분리를 통해서 메모리/디스크 사용량을 줄여 성능을 향상 시킬 수 있어요.


> 아래 링크에서 Device별로 어떤 배율을 제공하는지 알 수 있어요!
[Device screen sizes and oriendtations](https://developer.apple.com/design/human-interface-guidelines/layout#Device-screen-sizes-and-orientations)

> 아래 링크에서 pt를 실제 Device에 적용시키기까지의 과정을 볼 수 있어요!
[Ultimate Guide to iPhone Resoulutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)


## References

- [https://developer.apple.com/design/human-interface-guidelines/images](https://developer.apple.com/design/human-interface-guidelines/images)
- [https://developer.apple.com/design/human-interface-guidelines/layout](https://developer.apple.com/design/human-interface-guidelines/layout)
- [https://ios-development.tistory.com/396](https://ios-development.tistory.com/396)
- [https://spoqa.github.io/2012/07/06/pixel-and-point.html](https://spoqa.github.io/2012/07/06/pixel-and-point.html)
