---
title: "Command ValidateEmbeddedBinary failed with a nonzero exit code"
excerpt: "1년간 괴롭혔던 빌드 에러 해결방법 대방출!"

categories:
  - Issue
tags:
  - [Xcode, Issue, iOS]

permalink: /issue/validate_embedded_binary/

toc: true
toc_sticky: true

date: 2023-11-10
last_modified_at: 2023-11-10
--- 



오늘은 저를 1년이 넘는 시간동안 아주 악독하게도 괴롭혔던 Xcode Build 에러 해결 방법을 공유해보겠습니다!!!

(진짜 구원 그 자체)

## 문제

Application Extension이 추가된 프로젝트에서 빌드 시도 시  
아래 에러 내용으로 빌드에 실패하는 문제..

또 빌드가 항상 실패하는것은 아니고, 가뭄에 콩나듯 한번씩 빌드가 또 성공하기도 해요. 

에러: `Command ValidateEmbeddedBinary failed with a nonzero exit code`

![](https://velog.velcdn.com/images/textobey/post/2442b382-5514-46c9-b82e-9464c9cce420/image.png)

<span style="color:gray">(이게 빌드 다 되고나서 마지막 부분에서 에러가 발생하는거라 시간이 오래 걸리는 프로젝트일수록 더 고통...여기에 클린빌드까지 한다면? 우웩 😵)</span>

## 원인

추측이지만 Xcode 14로 업데이트 되면서 올라온 릴리즈 노트에  
기존 ~App과 ~Extension으로 분리되어있던 대상을 하나의 대상으로 합치는 작업을 수행했는데 이게 빌드 과정에서 타겟 네임?을 변경해버리는데 이것을 원인으로 추측 중..

https://developer.apple.com/documentation/xcode-release-notes/xcode-14-release-notes

## 해결방법

1. Xcode 프로젝트에서 Project 또는 Target을 선택
2. Editor -> Validate Settings
![](https://velog.velcdn.com/images/textobey/post/b821219f-3dff-47a4-92db-ac8ee4f706db/image.png)
3.  Build Phases 체크박스 **체크해제**
![](https://velog.velcdn.com/images/textobey/post/cbb801ea-554a-4c13-9285-aaa7293e022a/image.png)

### 그 외 실패한 시도들..
- 클린빌드
- DerivedData 폴더 파일 제거
- CFBundleShortVersionString 변경
- Xcode 껐다 켜보기, Mac 껐다 켜보기 등...