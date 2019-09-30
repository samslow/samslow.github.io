---
layout: post
title:  "Rails 에서 JS와 CSS의 로딩은 어떻게 하나 "
subtitle:   "Rails 에서 JS와 CSS의 로딩은 어떻게 하나 "
categories: development
tags: rails
comments: true
---
Today I Learn의 Rails 시리즈로써 Asset pipeline에 대해 다룹니다.

## Asset pipeline

### Rails 에서 JS와 CSS의 로딩은 어떻게 하나 

```html
<head>
  <link rel="stylesheet" type="text/css" href="mycss.css">
</head>
```

assets(Javascript, css)는 기본적으로 위와같이 HTML 문서에서 `<Head>` 안에서 호출된다.

이 방법은 보기에 직관적이라 어디있는지 바로 알 수 있는 장점이 있지만,

조금만 프로젝트가 커져 여러 js, css를 가져와야할때 코드의 길이가 길어져 관리가 힘들어지는 단점이 있다.

~~한마디로 머리가 드럽게 큰 가분수형 인간이된다(?)~~

하지만 Rails의 경우 Assets 폴더를 별도로 관리하여 아래와 같이 단 두줄에 CSS와  JS를 가져온다.

```html
<!-- views/layouts/application.html.erb -->
<head>
    <title>my service</title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all'%>
    <%= javascript_include_tag 'application'%>
</head>
```

위 코드를 부연설명 하자면 `app/assets`에서 stylesheets 와 javscripts 폴더내의 `application`이라는 `manifest`파일을 호출하는것이다.

수십줄의 반복적인 CSS link 태그대신 잘 정돈된 `manifest` 파일만 호출한다면 깔끔하게 정리된 수납함 하나만 가져오면 된다.

<u>이와같이 DRY하게 최소화 하는 과정을 **asset pipeline** 이라 한다.</u>

레일즈에서는 기본적으로 asset pipeline으로 asset을 가져오며 이방법이 아닌 `<link>` 태그로 사용하고 싶다면

`config/application.rb` 에서 `config.assets.enabled`를  `false`로 추가해주면 된다.



### 그렇다면 manifest 파일은 무엇인가?

OS나 Android를 해 본 사람이면 쉽게 이해 할 수 있을텐데, 위에서도 한번 언급했지만 간단히말하면 `잘 정돈된 수납함` 으로 이해 할 수 있다.



양말을 정리하는 상황이라고 해보자.

수납함이 긴양말, 짧은 양말, 덧신 등으로 '잘' 나누어져 있으면 여름이던 겨울이던 바로 제 위치에서 찾아 갈 수 있다.

이렇게 잘 찾아가도록 한꺼번에 관리하며 모아놓은 파일이 `manifest`이다. 

다양한 분야에서 자주 등장하는 용어이니 이번 기회에 알아가도록 하자.



위와같이 HTML 파일이 선언되었다면 이제 `app/assets/stylesheets` 를 가보자

```ruby
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, or any plugin's
 * vendor/assets/stylesheets directory can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any other CSS/SCSS
 * files in this directory. Styles in this file should be added after the last require_* statement.
 * It is generally better to create a new file per style scope.
 *
 *= require_tree .
 *= require_self
 */
```

처음 위의 파일을 접했을때 모두 주석처리 되어있길래 별로 중요하지 않은가보다라고 생각했지만 일부는 맞고 일부는 아니다.

위의 텍스트들은 해당 파일에 대한 주석 설명이 맞지만

코드 하단에 위치한 `*= require_tree . ` 처럼 표기되는 부분들은 주석이 아니라 위의 예시에서의 `양말들` 이 되는 것이다.

양말을 하나 추가 할 때마다 `*= require socks.css` 로 추가해 주면 전체 manifest에 반영이 되는 것이다.

이는 코드를 간결하게 보여지게 할 뿐만 아니라, 실제로 미리 컴파일하고 압축하는 과정을 통해 request를 최소화 하게 되어 서버에 부담도 덜게 된다.



### Directives

그럼 manifest에서 파일을 가져오는 다양한 방법에 대해 더 알아보자

`*=` 뒤에 오는 명령어(?) 들을 `Directives` 라고 하는데 이는 코드를 더 짧고 간단하게 보이게 해 준다.

제일 많이 사용되는 것은 `require`와 `require_tree` 이다.

후자의 경우 경로를 설정 해 주면 해당 경로의 assets을 모두 가져오기 때문에 나열하지 않아도 되는 장점이 있지만 

반대로 모두 가져오기 때문에폴더구조가 복잡 해 질 수 있다.

사실 이 모든것들을 더욱 최소화하기위해(최소화의 끝은 어디인가) `.css` 대신 `.scss` 의 `@import  "00/0"` 를 사용한다고 한다.

다음에는 이에 대해서도 다뤄보겠다. 이번 포스트에서는 아래에 다른 Directives를 소개하며 마무리한다.

본인의 사용 용도에 맞게 다양한 directive를 사용하면 된다. 



**require** ***[path]*** 는 경로상에 있는 asset 소스파일의 내용을 삽입해 준다. 동일한 소스파일을 여러번 require해도 컴파일시 한번만 포함

**include** ***[path]*** 는 require와 같은 기능을 가지지만, 해당 소스파일이 이미 include 되었거나 require 되었더라도 소소파일의 내용을 삽입해 주게 된다.

**require_directory** ***[path]*** 는 경로로 지정된 디렉토리에 있는 동일한 포맷의 모든 소스파일을 require해 준다. 이 때 파일들은 알파벳순으로 require된다.

**require_tree** ***[path]*** 는 require_directory와 같은 기능을 가지지만, 경로로 지정된 디렉토리의 모든 하위디렉토리들에 있는 파일들까지 모두 require되도록 한다.

**require_self**는 모든 require 또는 include directives 전에서 사용하여 현재 소스파일의 내용을 삽입하도록 한다.

**depend_on** ***[path]*** 는 경로상의 소스파일의 내용을 포함하지 않은 채로 의존성만 선언한다. 다른 파일이 수정될 때 asset 캐시를 expire할 필요가 있을 때 유용하게 사용할 수 있다.

**stub** ***[path]*** 는 경로상의 소스파일을 제외하도록 해준다. 경로는 하나의 유효한 asset 이어야 하고 이미 포함되었거나 포함되지 않을 수 있다. 일단 해당 소스파일이 제외되면, 블랙리스트에 등록되어 추후 require 하더라도 불러올 수 없게 된다.



Directives와 Asset pipeline에 대한 더 자세한 정보는 https://rorlab.org/rblogs/152 를 참조하면 된다.