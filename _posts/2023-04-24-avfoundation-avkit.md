---
title: "AVFoundation과 AVKit"
excerpt: "AVFoundation과 AVKit으로 무엇을 할 수 있을까"

categories:
  - iOS
tags:
  - [iOS]

permalink: /ios/avfoundation-avkit/

toc: true
toc_sticky: true

date: 2023-04-24
last_modified_at: 2023-04-24
---

이번 포스팅에서는 Apple 플랫폼에서 미디어와 관련된 프레임워크인 AVFoundation, AVKit에 대해 다뤄보려고 해요 :)

나중에 샘플 프로젝트를 작성하고 작성한 프로젝트 코드를 가지고 추가 포스팅을 할 수도 있을거 같아요. (대충 이번에는 차근차근 개념부터 다룬다는 말)

## AVFoundation
![](https://velog.velcdn.com/images/textobey/post/ed2f77b7-6831-4a23-86e6-c357fb860d5a/image.png)
>AVFoundation은 Apple 플랫폼에서 time-based인 시청각 미디어 작업을 위한 완전한 기능을 갖춘 프레임워크 라고 하네요.
>AVFoundation을 사용하면 QuickTime 영화 및 MPEG-4 파일을 쉽게 재생, 생성 및 편집하고, HLS(HTTP Live Stream)를 재생하고, 앱에 강력한 미디어 기능을 구축할 수 있다고 소개하고 있어요.

그리고 [Document](https://developer.apple.com/documentation/avfoundation/)에서는 구체적으로 어떤 작업을 할 수 있는지에 대해 설명하고 있어요.
- 시청각 Assets 작업
- Device 카메라 제어
- 오디오 처리
- 시스템 오디오 상호작용

미디어, 오디오, 카메라 등 광범위한 작업을 포함하는 여러 주요 기술이 결합된 프레임워크라고 정리할 수 있겠네요.

### AVAsset
시청각 미디어를 모델링 하는 객체라고 [Document](https://developer.apple.com/documentation/avfoundation/avasset)에 설명이 되어 있는데요.
![](https://velog.velcdn.com/images/textobey/post/65655615-657a-456c-8084-8d8f6cd151a6/image.png)
AVAsset은 AVAssetTrack 타입의 다양한 Track이 모여 만들어지는 것을 볼 수 있어요.

AVAsset은 오디오, 비디오 트랙, 제목, 길이, 자연스러운 영상 사이즈 등 다양한 데이터의 집합체가 되기 때문에 AVFoundation에서 가장 중요한 타입으로 볼 수도 있겠네요!


## AVKit

AVKit은 Trasnport Controls, Chapter navigation, Picture-in-Picture(PIP 모드)와 자막 및 폐쇄 자막 표시를 갖춘 미디어 재생용 사용자 인터페이스를 생성한다고 해요.

AVKit 안에 있는 AVPlayerViewController 클래스는 AVFoundation에 직접적인 접근을 제공하기 때문에, 미디어 재생에 관련된 다양한 기능들을 구현할 수 있어요.

> 혹시 아이폰을 사용하면서 미디어를 재생했을 때, 이런 화면(미디어 플레이어)가 제공되는걸 보신적이 있을것 같아요.
> 이 화면 하나로 AVKit이 무엇을 제공해 주는지 한눈에 들어올것 같네요 :)
> <img width="500" alt="2023-04-20 3 46 55" src="https://velog.velcdn.com/images/textobey/post/10385ac2-0c6f-44fe-84c9-e895745ea56a/image.jpeg">

AVKit 프레임워크를 통해 따로 미디어 플레이어 UI를 커스텀하지 않고 주어지는 그대로 사용하여도 완성도 있는 미디어 플레이어를 사용자에게 제공할 수 있기 때문에 정말 강력하고도 좋은거 같아요.

## iOS AVFoundation Stack

![](https://velog.velcdn.com/images/textobey/post/59770492-095d-45e3-a84e-c48b0848787d/image.png)

마지막으로 다뤄볼것은 AVFoundation(+AVKit)에 대한 스택인데요!
위에 그림처럼 AVFoundation은 UIKit 또는 AVKit과 비교해 보았을때 Core 쪽으로부터 더 가까운 low-level 프레임워크예요.

그렇기 때문에, AVFoundation은 UIKit보다 low-level에 위치하고 있어서   표준화된 UI를 제공해주지 않아요.
AVFoundation을 활용하여 작업을 진행하려면 AVFoundation과 low-level에 대한 깊은 지식이 필요하겠네요.

> Apple에서는 일반적으로 원하는 작업을 수행할 수 있는 가장 high-level의 추상화를 사용해야 한다고 해요. 예를 들어,
1. 단순히 영화를 보는 목적이라면 AVKit 프레임워크를 사용
2. iOS에서 비디오 녹화를 할 때 오직 전체 구성에 대한 최소한의 컨트롤만이 필요하면 UIKit 프레임워크를 사용

그리고, AVFoundation에는 프레임워크에는 오디오에만 관련된 API 두 가지 측면이 있는데요. 
- 사운드 파일을 재생하려면 [AVAudioPlayer](https://developer.apple.com/documentation/avfaudio/avaudioplayer)
- 오디오를 녹음하려면 [AVAudioRecorder](https://developer.apple.com/documentation/avfaudio/avaudiorecorder)

를 사용하여 애플리케이션의 오디오 동작을 구상할 수도 있다고 하네요.



## References

- [https://developer.apple.com/av-foundation/](https://developer.apple.com/av-foundation/)
- [https://developer.apple.com/documentation/avkit/](https://developer.apple.com/documentation/avkit/)
- [https://developer.apple.com/documentation/avfoundation/avasset](https://developer.apple.com/documentation/avfoundation/avasset)
- [https://developer.apple.com/documentation/avfoundation/avplayer](https://developer.apple.com/documentation/avfoundation/avplayer)
- [https://ios-development.tistory.com/927](https://ios-development.tistory.com/927)
- [https://wnstkdyu.github.io/2018/05/03/avfoundationprogrammingguide/](https://wnstkdyu.github.io/2018/05/03/avfoundationprogrammingguide/)
