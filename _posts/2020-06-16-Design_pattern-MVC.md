---
layout: post
title: "디자인 패턴 - 당신은 MVC를 안다고 할 수 있는가"
subtitle: "디자인 패턴 - 당신은 MVC를 안다고 할 수 있는가"
categories: development
tags: web
comments: true
---

- MVC를 알고 사용할 순 있지만, 그것에 대해 설명하긴 어려울 때 도움이 되는 글
  - 왜 ES20

# 디자인 패턴의 개념과 필요한 이유

SW 디자인 패턴은 **공통으로 발생하는 문제에 대한 재사용 가능한 해결책**이다.([위키백과](<[https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EB%94%94%EC%9E%90%EC%9D%B8_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/소프트웨어_디자인_패턴)>))

즉 이런 방식으로 SW를 개발하면, 일반적인 문제에 대한 해결책이 패턴화 되어있기 때문에 개발 주기가 빨라 질 수 있다.

우리가 알고있는 프레임워크들과 라이브러리도 하나의 디자인 패턴으로 볼 수 있다.

이러한 디자인 패턴은 권장 사항이지 필수 사항은 아닌데 왜 다들 지키려 할까?

그 이유는 품질 좋은 클린 코드를 빠르고 높은 생산성으로 만들 수 있기 때문 일 것이다. ~~( 이게 실존하는지는 모르겠지만 )~~

아래의 `SW 개발 3대 원칙` 이론에서 그 근거를 아래와 같이 찾아 볼 수 있다.

- KISS - Keep It Simple Stupid
  - 코드를 되도록이면 간단하고 단순하게 짜고, 불필요하게 장황해 지는 것을 경계하라는 원칙
  - 말은 쉽지만 실제로 깔끔하게 짜는것은 마냥 쉽지는 않다.
- YAGNI - You Ain't Gonna Need It
  - 지나치게 먼 미래를 생각해 미리 작업하지 말고 현재에 집중하라는 원칙
  - 어차피 미래가 되면 그 코드를 다시 처음부터 이해해야 한다.
- DRY - Do not Repeat Yourself
  - 동일 코드를 반복하지 말라는 원칙
  - 같은 기능을 하는 코드가 여러 군데에 산재 해 있으면 모든 코드를 수정 해야하고 이 과정에서 실수가 있을 경우 버그로 쉽게 이어진다.
  - 개인적으로 가장 좋아하는 원칙이자 Ruby On Rails 의 철학 중 하나

결국 모두 개발 습관이고, 몇가지는 알게 모르게 지켜오는 것도 있고, 혹자는 이런 것을 굳이 정해놓고 하느냐고 할 수도 있다.

틀린 말도 아니고, 결국 원칙은 원칙일 뿐 본인에게 맞는 스타일로 현재에 집중해서 개발하면 된다.

# 디자인 패턴의 종류

디자인 패턴은 오래전부터 연구되었던 소프트웨어 공학 분야이기 때문에 종류도 그만큼 많다.

아마 당신이 개발을 어느정도 해 봤다면 여기에 있는 패턴을 모두 알지는 못하더라도 최소한 2~3개는 들어 본 적이 있을 것이다.

![types-of-designpattern](https://gmlwjd9405.github.io/images/design-pattern/types-of-designpattern.png)

[by @gmlwjd9405](https://gmlwjd9405.github.io/2018/07/06/design-pattern.html)

이러한 디자인 패턴을 모두 알면 좋겠지만, 그렇지 않다면 싱글턴, 커맨드, 팩토리, 빌더, 옵저버 패턴 정도 알면 도움이 될 것이다.

그리고, 오늘은 이러한 일반적인 GoF Design Pattern에서 굳이 분류하자면 **옵저버 패턴과 미디에이터 패턴**이 적절히 섞인듯한

MVC 패턴에 대해 알아보고 이 다음 포스팅으로 MVVM에 대해 알아볼 것이다.

# MVC Pattern

위에 설명 한 것 처럼 디자인 패턴이란 권장사항이지 에러를 일으키거나 고정된 사용방법이 명확한 것은 아니기 때문에 다양한 사용 방법이 존재한다.

MVC는 Model-View-Controller의 약자로 아래 두 패턴을 적절히 섞어놓은 패턴으로 알려져있다.

- Observer Pattern: 하나의 Model에 다양한 View를 두어 상태가 변할 때 그 모델을 구독하는 객체들이 자동으로 갱신 됨
- Mediator Pattern: Observer Pattern에서 M:N 관계가 많이 생성되면 전체가 복잡해지기 때문에, 중간에 중재자(Mediator)를 두어 이벤트가 발생하고 전달하는 것을 단순화 함

실제 사용에 성공하게 되면, UI로부터 Logic가 분리되어 App의 다양한 부분들을 더 간결하게 볼 수 있어 버그픽스에 유리하다.

아래 그림은 클라이언트와 DB를 고려한 일반적인 MVC 패턴의 대표적인 예시이다.

![img](https://upload.wikimedia.org/wikipedia/commons/5/53/Router-MVC-DB.svg)

[by MDN](<[https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC](https://ko.wikipedia.org/wiki/모델-뷰-컨트롤러)>)

- Controller: Model이나 View의 요소들을 유저의 요청에 맞게 "어떻게" 처리 할 것인지 정의
- Model: 데이터, 알고리즘, DB 등을 Model이라고 하는데, App이 "무엇"을 처리할 것인지 정의
- View: 사용자에게 보여 줄 정보들을 Controller나 Model에서 가져와 보여주고 입력을 받을 수 있도록 함

잘 이해가 가지 않는가 ? 위의 3가지를 다시 바꿔 말해보자

- Controller는 무엇을 처리할지나 보여줌에 있어서 신경쓰지 않는다.
- Model은 어떻게 보일지(View, Controller)에 대해 신경쓰지 않아도 된다.
- View는 데이터 저장이나 로직을 신경쓰지 않고 변경 처리 이벤트에만 관심있다.

결국은 이런 세가지 역할이 분업이 잘 될수록, 서로 역할이 완전히 나눠져 있을수록 디자인 패턴이 잘 적용되었다고 볼 수 있다.

예를들어 계산기를 만든다고 했을 때는 아래와 같이 구성 할 수 있다.

- Model
  - 데이터 베이스 및 데이터 표현
  - 유저가 입력하는 숫자를 저장, 이전 계산 기록을 저장
- View
  - 계산 결과를 보여줌
  - 현재 입력중인 숫자나 사칙연산이 무엇인지 보여줌
- Controller
  - 입력값이 초과하지 않았는지
  - 사용자가 View에서 입력한 숫자를 Model로 변경 해 줌
  - 사칙연산 계산

# 그럼 왜 MVVM이 나왔을까?

세상에 프로그래밍 언어가 C언어만 있는것은 아닌 것 처럼, MVC도 이론상은 그럴 듯 하지만, 시스템의 규모가 커질 수록 그 한계가 명확해져갔다.

- Model과 View가 M:N 관계로 매우 복잡 해 진다. 즉 이런 의존성이 문제를 일으킬 확률이 올라간다.
- 만약 MVC를 얕게 아는 상태에서 MVC를 적용하려 했다면, 높은 확률로 Controller에 모든 Logic이 들어갈 가능성이 크다. 하지만, 그럼 Controller의 할일이 불필요하게 많아지는 경우가 있어 필요에 따라선 Model에 로직과 메소드가 설정해 이를 분산 시켜야 한다.(getter, setter, ··· )

이런 문제는 MVVM에서는 어떻게 개선시킬 수 있었을까?

다음 포스트에서는 MVC의 구성을 알아본 것 처럼 MVVM도 그 유래와 뜻을 알아보도록 한다.

# 작성 후기

처음에는 MVC를 써보기만 했지 개념적으로 접하기 쉽지 않았는데, 제대로 각을 잡고 보니 잘못 사용하고 있던 부분도 있었고

과도하게 컨트롤러에 비중이 크거나 유지보수를 고려하지않은 Massive한 코드도 있었다.

이제 누군가 MVC에 대해서 아는대로 설명하라고 한다면, 위 글을 기반으로

M,V,C에 대해 설명하고 Client입장에서 이런 흐름이 어떻게 동작하는 지 설명 하면 되겠다.

마지막으로 당부하고 싶은 말은, 디자인 패턴이란 것이 정답이 없기 때문에 각자 큰 틀안에서 각자 조직에 맞도록 알맞게 변형하면 된다고 생각한다.

정답은 없기 때문에 이것을 정석으로 보기 보다는 이제까지 본인의 코드를 되돌아보면서 "우린 이렇게 썼었네", "이게 MVC로 구성된것이었구나" 라고 느끼고 더 잘 할 수 있는 방법에 대해서 고민하면 그게 진짜 정답이지 않을까

# Reference

- [테크 스케치-SW 개발 3대 원칙](https://blog.naver.com/PostView.nhn?blogId=complusblog&logNo=221163007357&redirect=Dlog&widgetTypeCall=true&directAccess=false)
- [MVC Orientation](https://stackoverflow.com/questions/9119657/how-do-gang-of-four-design-patterns-fit-into-the-mvc-paradigm)
