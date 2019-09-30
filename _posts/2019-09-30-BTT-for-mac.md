---
layout: post
title:  "맥북 사용자를 위한 터치바 커스터마이징 (BTT 완벽 가이드)"
subtitle:   "맥북 사용자를 위한 터치바 커스터마이징 (BTT 완벽 가이드)"
categories: etc
tags: utility
comments: true
---

* 이 글은 맥북을 사용하는 분들의 터치바 활용성을 증대시키고 맥북을 더 잘 쓰고싶은 분들을 위하여 기고되었습니다.

![touchbarEx](https://www.dropbox.com/s/m0dh5lyrwcgk8rl/touchbarEx.png?dl=1)

![touchbarEx2](https://www.dropbox.com/s/hhptr7fo3d0r0uj/touchbarEx2.png?dl=1)

* 위 사진 두장은 제가 현재 사용하고 있는 터치바 디자인 입니다. 여러분도 아래 과정을 따라하면 똑같이 나오게 됩니다.

📢 두번째 사진의 날씨는 1번 터치바 그림에서 날씨 아이콘을 꾹 누르면 펼쳐집니다. 이 외에도 cmd와 opt를 누르면 나오는 창은 캡쳐가 안되서 올리진 못했지만 직접 올려보시고 봐보시길 바랍니다!

📢 재생 버튼을 꾹 누르면 YoutubeMusic이 켜집니다.

# 커스터마이징 툴 BetterTouchTool(BTT) 설치

1. [BTT 공식 사이트](https://folivora.ai/)로 이동하여 `DOWNLOAD TRIAL (45DAYS)`를 다운받는다.
2. 다운 받는 중에 손쉬운 설정을 까지 마치면 설치가 완료된다.

45일동안 체험 해 볼 수 있는 버전으로 저는 현재 2년동안 업데이트를 받을 수 있는 라이센스를 구매하여 사용하고 있습니다.

45일이라 하여도 실제 해 본 결과 알게된 정보는

- 45일은 절대시간 기준이 아니라 실제로 맥북이 잠자기에서 깨어나 활성상태로 돌아가고 있는 시간을 기준으로 계산한다. ( 2달넘게 사용해도 체험판이 끝나지 않았음 )
- TRIAL이 끝나도 완전히 삭제 한 후 다시 설치하면 다시 처음으로 돌아가 45일동안 사용 가능 ( 대신 기존 설정이 날아감 )
  - 이를 막기 위해 현재 Preset을 저장하여 재설치 하는 방법도 있음 ( 시도 해 보지는 못함 )
- 2년동안 업데이트를 받을 수 있는 LICENSE는 약 8달러로 한화 약 1만원 정도로 저렴한 편
  - 단, LICENSE 파일을 메일로 보내주는데 이를 잊어버리면 골치아파지므로 주의 요망

# BTT가 제공하는 기능

- 키보드 단축키 커스터마이징
  - 한영변환키, 카카오톡, 캘린더, 슬랙 단축키를 등록할 수 있고 잠자기, 잠그기 등의 맥북 내장 API를 사용 할 수 있다.
- 터치바 커스터마이징
  - 글 상단에 있는 사진은 실제 제 맥북의 터치바를 캡처(ctrl + cmd + shift + 6) 한 화면입니다.
- 이외 트랙패드, 매직마우스 그리고 Siri Remote 등 커스터마이징 할 수 있는건 싹다 가능
- 보통 window를 이동하려면 상단바를 드래그하여 창을 움직여야 하지만, 단축키를 누른상태로 해당 윈도우에 마우스가 올라가 있기만하면 이동 간편
  - 이게 별거 아닌거 같아도 저는 처음에 이거하나만을 위해 LICENSE를 구입 했습니다.
- Snapping Area를 만들어 window를 해당 Area에 놓기만 하면 사전설정한 사이즈와 위치로 설정됨.
- 이외에도 뜬금없이 Webserver 기능을 제공하는 등 잡다한 기능이 종종 들어가 있음

# 사용법 & Preset 적용법

BTT는 현재 계속 개발되고있는 툴로써 UI가 계속 개편되고 있는 중인데, 최근 새로운 UI가 도입되면서 때아닌 과도기(!?)를 겪는 중입니다.

그래서 저는 충분히 안정화되고 유용한 이전 UI로 설명 하겠습니다.

1. 설치가 되면 Btt를 Spotlight로 실행 한 뒤 상단바에 있는 트랙패드 아이콘 클릭

   ![btt1](https://www.dropbox.com/s/g17m1d1w0lo1ztp/btt1.png?dl=1)

2. 이중 Old Configuration UI를 클릭하여 메뉴 설정 진입

   *  기능들을 직접 설명 하는 것 보다 아래 화면에서 본인이 커스터마이징 해 보는것이 빠름.

3. Advanced Settings > Moving & Resizing 에 진입하여 아래 Move windows 와 Resize windows를 어떤 단축키와 쓸 것인지 체크

   * 저는 Move windows는 ctrl + cmd와 함께, Resize windows는 Ctrl + opt 로 사용 합니다.
      ![btt2](https://www.dropbox.com/s/op2ur5blfq9mvow/btt2.png?dl=1)

4. Advanced Settings > Window Snapping 에서 Window Snapping Enabled 에 체크하고 맥북 상단 바에서 Snap Areas가 활성화 되면 입맛에 맞게 커스텀
   ![btt3](https://www.dropbox.com/s/9ftwvvmworrg3zl/btt3.png?dl=1)

5. 터치바의 완벽한 호환을 위해 `General Touch Bar Settings(첫번째 사진 오른쪽 위)` 을 클릭하여 `Default Settings`와 `Advanced`를 아래 사진처럼 세팅한다.

   ![bttset1](https://www.dropbox.com/s/66l6i18rf3k0akh/bttset1.png?dl=1)

   ![bttset1](https://www.dropbox.com/s/lsottv6oyfxvhbd/bttset2.png?dl=1)![bttset3](https://www.dropbox.com/s/1jadj5irt0giucd/bttset3.png?dl=1)



6. 마지막으로, 아래 첨부되어 있는 bttPreset 파일을 다운받아 왼쪽 아래 `Manage Presets` 에서 `import`로  적용하면 대부분의 세팅이 완료된다.
   * <a href="https://drive.google.com/open?id=1aiqX5M0XNByXBDmA4OnscENsVhRirfNw" target="_blank">Samslow-preset</a>

7. 이후에는 본인 입맛에 맞게 슥 샥 쇽 끼우면 나만의 맥북 터치바 완성!


이상으로 BTT 튜토리얼을 마칩니다.

**맥북 터치바가 구리다고 안 쓰시는 분들이 많은데 이 글을 읽고 많은 분들이 본인 입맛에 맞게 커스터마이징해서 사용하셨으면 좋겠습니다.**

