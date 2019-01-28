---
layout: post
title:  "Jekyll Blog, PCDATA invalid Char value Error 해결 방법"
subtitle:   "Jekyll Blog, PCDATA invalid Char value Error 해결 방법"
categories: etc
tags: bugFix
comments: true
---
Jekyll 블로그 포스팅을 하다가 평소에는 문제가 없지만 rss 문서 즉, feed.xml 문서로 들어가면 나오는 PCDATA error를 해결하는 방법에 대한 글입니다.

# Jekyll Blog, PCDATA invalid Char value Error 해결 방법

## 에러의 시작

일주일간의 노동을 마치고 주말에는 맘 편히 쉬면서 블로그를 할까 하여 글 소재를 찾고있었다.

그러다 우연히 [Slack Channel을 통한 개발자 블로그 구독 방법](https://www.slideshare.net/zzsza/intro-102870757)을 새로 알게 되었다.

<img src="/assets/post_img/image-20190128093037359.png" height="600px" />

사실, 초기에는 Rss의 필요성을 못 느껴서 블로그 사이드에 있는 링크를 아예 빼버렸었는데

지금은 제일 마지막 아이콘으로 들어가있다. 안에 들어가게되면 아래와 같이 일반 독자는 보기힘든 글이 나온다.

<img src="/assets/post_img/image-20190128093620578.png"/>

그러다 첫문단의 링크를 통해 rss 구독의 필요성을 느끼게 되었고 내 사이트도 미래의 누군가를 위해 붙여놓기로 했다.

그러나, 해당 아이콘에 링크를 달아 붙이는거까진 잘 됐으나 제일 처음 들어가자마자 아래와 같은 에러가 뜨게 되는데

<img src="/assets/post_img/s2019-01-28 01.16.05.png" />

처음보는 유형의 에러라서 일단 개발자의 본능으로 구글링을 해보았지만

아래와같이 다른 곳에도 나는 에러로 내것과는 해결방법이 근본부터 다른 것 같았다.

<img src="/assets/post_img/s2019-01-28 01.16.34.png"/>



## 원인 분석

몇번 삽질끝에 에러문장의 단어들을 자체분석 해 보기로 하였고 대분류는 `XML`과 관련된 에러라는걸 알게되고 

자세한 소분류로는 PCDATA 라는 것의 문제라는것을 알게되었다.

여기서 PCDATA란 Parsed Character DATA의 준말인데 자세한 설명은 [링크](http://tcpschool.com/xml/xml_dtd_component)의 아래 설명이 나와있다.

<img src="/assets/post_img/s2019-01-28 01.17.00.png"/>

물론, 이를 처음보는 나로썬 이것조차 무슨 말인지 알 수 없었지만 여기서 알 수 있었던것은 <u>`시작태그`와 `종료태그` 사이 위치한 `텍스트`데이터 라는 것이었다.</u>

여기서 이것은 보이지않는 `문장마감심볼`의 문제라는 느낌이 왔다. 뭐 CR, LF 같은 줄바꿈 심볼들말이다.

에러메시지를 자세히 보니 138번째 줄에 140번대 컬럼이 문제가 있는 것과 더 아래에 어떤 파일이 문제있는지 알게 되었고

해당 파일 부분은 아래와 같았다.

<img src="/assets/post_img/s2019-01-28 01.16.16.png" />

겉보기엔 아무 문제 없으나 132줄 부터 148줄을 지우고 `feed.xml` 을 빌드해보면 잘 나오는걸로 보아

원인을 찾는데엔 성공했다.



## 문제 해결

아마 본인의 에디터(Typora) 버그로 인해 들어가야할 심볼이 안들어간것 같은데 이것저것 지우고 돌려놓고 해봐도 정확히 문제가 되는 부분을 찾을 순 없었다.

그리하여, 저 부분만 다시 VSCODE로 타이핑 하는 방법을 사용했더니 정상적으로 나올 수 있게 되었다.

다행히 에러메시지에 정확하게 문제가 있는 부분이 표기가 되어서 XML 지식이 없어도 쉽게 문제를 해결 할 수 있었지만

관련된 게시글이 없어 다른 사람들의 삽질을 막기위해 글을 남기게 되었다.



