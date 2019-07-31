---
layout: post
title:  "GraphQL이란 무엇인가 (feat. 무한한 공간 저 너머로)"
subtitle:   "GraphQL이란 무엇인가 (feat. 무한한 공간 저 너머로)"
categories: development
tags: web
comments: true
---

매주 사내 개발자들을 대상으로 하는 세미나에서 GraphQL을 발표한 내용입니다.
기본적인 개념과 어떤 것을 공부 하면 익힐 수 있는지 가벼운 코드와 라이브러리를 소개합니다.

## GraphQL

![image-20190724165449802](https://www.dropbox.com/s/wlmsfalcdcrc7tp/image-20190724165449802.png?dl=1)

Facebook이 만든 `어플리케이션 레이어 쿼리 언어`로 기존의 `REST API` 도 편하지만, 쿼리가 복잡해지면 URL이 길어지고 수많은 Endpoint가 생겨 관리가 어려운 단점이 있으므로, 해당 부분을 해결하기 위해 REST를 저격하고 나왔다.

2015년에 처음으로 공개적으로 릴리즈 되었고 2016년 10월에 Stable 버전이 처음으로 공개되었다.

Wiki의 정의를 보면

- [GraphQL](https://en.wikipedia.org/wiki/GraphQL) is a data query language developed by [Facebook](https://en.wikipedia.org/wiki/Facebook) as an alternate to [REST](https://en.wikipedia.org/wiki/REST) and ad-hoc [webservice](https://en.wikipedia.org/wiki/Webservice) architectures.

~~REST 와 ad-hoc webservice architectures를 대체할 수 있나보다~~
(뭔지 모르지만 뭔가 더 안좋은 거겠거니 하고 같으니 패스)

공식 홈페이지를 보면

### A query language for your API

GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools.



유레카! 

이제는 **우리가 원하는 방식대로 Query를 날릴 수 있다**

언제까지 백엔드가 만들어주는 것만 쓸것이냐

이젠 프론트가 직접 찾는다. 그것도 원래도 잘 쓰던 JSON을 활용해서!

이해를 돕기 위하여 REST API 와 GraphQL의 차이를 기준으로 설명 해 보겠다.



### REST API vs GraphQL

REST API는 서버 - 클라이언트 간의 약속
Representational State Transfer 표현에 의한 상태 전달

#### REST API의 특징

![RestAPI](https://miro.medium.com/max/294/1*zIHqyyQZ9R_SjoFbFUdu0A.jpeg)

- 직관적이고 구현하기 단순
- 브라우저단에서 캐싱이 되고 확장이 용이
- **SOAP(REST 이전의 XMl 기반 통신)** 에 비해 굉장히 가벼운게 장점!
  - 하지만 타입기반 데이터 교환은 Bye~(하지만 GraphQL 너라면..)
- Client에서 어떤 데이터가 필요한지 API 설계를 잘 해야한다.
  - 쉽게말해, Backend가 짜준대로만 써야한다.
- 메뉴얼이 필요하다.
  - 문서 업데이트하는 공수가 또 든다..
  - GraphQL은 DB Schema만 알면, 그게 바로 API문서가 된다.
  - scheme 구조만 알면 Query가 쨘! GraphiQL 활용
    - [GraphiQL 예시](https://lucasconstantino.github.io/graphiql-online/)
- 정적이다. Static
  - 상황에따라 API를 만들어 줘야 하는 경우가 있을때 상당히 비슷한 API임에도 동적 활용이 어렵다.
  - 원하는대로 Query만 바꿔주면 되므로 프론트엔드 개발이 빨라 질 수 있다.

#### GraphQL의 특징

![GraphQL](https://miro.medium.com/max/294/1*PHxksCXy0Wh5ryANEZkfDQ.jpeg)

- 원하는 데이터를 그대로 받아 올 수 있음

  - REST에서 처럼 성능상 문제로 JOIN을 많이하는 API와 덜 하는 API를 나누어 구현할 필요가 없어졌다!

  - Client가 원하는 Depth까지 잘 끊어서 처리 할 수 있게 되었으니까

  - 요청의 횟수는 줄이고 응답의 Size는 줄인다!

  - 예를 들어, 전화걸어주는 어플이 갖는 엔드포인트는

    - REST : 세탁소 빨래 가져가세요. 교환 요청받으세요. 경비아저씨 배송이 왔나요 3개의 요청을 따로따로 보내 확인해야 한다.

    - GraphQL: 3개의 요청을 GraphQL Query 하나로 처리 할 수 있다.

      ![gq3.png](https://images.velog.io/post-images/jakeseo_me/6a884bb0-6953-11e9-9a35-d1042a1399f2/gq3.png)

      ![gq4.png](https://images.velog.io/post-images/jakeseo_me/b8cfd0e0-6953-11e9-9ecb-354d9d20c9dc/gq4.png)

- 크로스플랫폼 개발에 용이하다.

- ![image-20190724155752870](https://www.dropbox.com/s/48nq1iy7o747m36/image-20190724155752870.png?dl=1)

  - 예를들어, `포스트`에 `저자` 와 `좋아요` 기능이 있을때, 웹에서는 모두 보여주지만 칸이 부족하여 모바일에서는 `좋아요`  를 안 보여준다고 가정하였을 때, GraphQL은 요청만 다르게 보내면 되지만 REST는 아예 새로운 코드를 작성하여야 한다.
  - ![gq2.png](https://images.velog.io/post-images/jakeseo_me/5a4a0a60-6951-11e9-9ecb-354d9d20c9dc/gq2.png)

- Foreign Key 를 이용한 검색 시, 리졸버를 사용하여 다른 데이터의 id만 가지고 바로 검색 가능 (REST도 설계에 따라 가능하지만 설계가 유연하진 않다, 때때로 어떤 요청은 다른 요청의 응답이 온 후에 보내야 하기도 한다.)

  - 아래와 같이 authorid, postid,comment 필드를 가진 Post타입이 있고 CountComment 필드가 없어도 있는 것처럼 흉내 낼 수 있다. → **API 스키마와 DB 스키마가 분리되어 처리된다!!** 장점이라면 장점

  - ```text
    Post: {
      author(post) {
        return Users.find({ id: post.authorId})
      },
      commentsCount(post) {
        return Comments.find({ postId: post.id}).count() 
      }
    }
    ```

    

- 단점으로는 캐싱과 예외처리등이 어렵다는 점

  - 하지만, 최근 Apollo의 등장으로  **Client, Server 단의 라이브러리, 캐싱 및 쿼리 분석도구**를 제공한다.
  - 기본적으로 형제인 React와 스핀오프가 좋다.

- 타입 기반 API 스키마 작성하여 클라이언트가 타입체크 가능

- 재미로 보는 아이콘 해설

  - ![img](https://t1.daumcdn.net/cfile/tistory/991CC53A5A54399B05)

    ![img](https://t1.daumcdn.net/cfile/tistory/996EF9345A5439A832)

    - 각 점들은 Node이자 Table 이고 이 Table들은 서로간을 Network로 연결한다.
    - 다른곳에서 호출 받는 것이 아니라 끼리끼리 호출한다는 의미



### 기존 `REST API` 와의 차이점으로 `Friend`  의 정보를 보자.

`Friend` 는 id, username, email 등의 기본 정보를 가지고 있다.



1. 1번 유저의 친구 목록을 호출할때 
   - REST API:  `GET /account/1`  
   - GraphQl

- ```json
  // GET /graphQL
  
  query {
  	account(id: "1"){
  		username
      email
      firstName
      lastName
    }
  }
  ```

2. 서비스의 요구사항이 바뀌어서 1번 유저의 친구들의 목록 까지도 봐야 할 때

   - REST API: `GET /account/1/?friends_detail=username, firstname`

   - GraphQL

     ```json
     // GET /graphQL
     
     query {
     	account(id: "1"){
     		username
         email
         firstName
         lastName
         friends{
           firstName
           userName
         }
       }
     }
       
     ```

3. 친구를 추가해야 할때!

   - REST API: `POST /account/1/edit_friend/?username="samslow"&email="naver··· `

   - GraphQL

     ```json
     // GET /graphQL
     
     mutaion {
       createAccount(
       	userName: "samslow"
         email: "naver.com"
         firstName: "Hyeonseok"
         lastName: "Seo"
         )
     }{
       username
       email
     }
     ```

     



## 외부 라이브러리 - Prisma

Prisma는 DB 프록시이다. GraphQL 스키마를 기반으로 DB를 자동으로 생성해준다.

GraphQL 스키마 자체가 엄격한 타입기반으로 구성되어 있고 DB를 구현하기에 충분한 데이터가 있기 때문이다. MySql, Postgresql등의 스키마 및 쿼리를 자동으로 생성해준다. 사용자는 GraphQL 스키마만 설계하면 되고 DB를 설계할 필요가 없다.

```text
type Character {
  name: String!
  appearsIn: [Episode]!
}type Query {
  hero(episode: Episode): Character
  droid(id: ID!): Droid
}
```

**→ 여기에선 자세히 다루지 않지만, `RubyOnRails` 의 ActiveRecord 처럼 간편하게 쓸 수 있는 인터페이스로 이해하고 넘어가면 된다. 나중에 자세히 다뤄보겠다. **



## 하지만 그렇다고 모두 대체되는 것일까?

정답은 NoNo, 한번 예측 해 보시라.아래의 경우를 보자.

- 외부로 오픈되는 API일 경우
- 자주 쓰이는 Request의 경우 REST로 만들어놔도 좋다.
- etc



## 그럼 여기서 해결하지 못한 질문들

*  ##### GraphQL은 라이브러리다?

누군가는 DB라고도하고, 누군가는 라이브러리라고도 하는데, 정확한 정의는 새로운 스펙이다.

마치 RESTful한 것이 라이브러리가 아닌 것처럼. 하지만 오해할 여지는 있는것이. 많은 언어에서 GraphQL 구현체가 이미 존재하기 때문에 해당 부분을 가져다 쓰다보니 생길 수 있는 질문.

*  ##### 기존 엔드포인트에 GraphQL을 적용 할 수 있다 ?

제가 바로 그렇게 해보려고 했습니다.. 스프링의 대한 이해부족과 시간제약때문에 실패하긴 했지만 불가능하지는 않다고 합니다. 하지만, GraphQL 은 단일 엔드포인트를 권장하기 때문에 REST가 URL과 Resource를 매칭시키는 개념적 모델을 사용했기 때문에 수많은 엔드포인트가 있었다면, GraphQL은 모든 리소스가 그래프처럼 서로 연결되어있기 때문에 URL을 분리 할 필요가 없다. 서버는 Query와 Mutaion(명령어)만 제공하면 된다.

 **리소스를 url이 아니라 Query로 표현하기 때문에 GraphQL은 창구개념의 하나의  엔드포인트 면 충분하다.**

* ##### REST를 잘 쓰고 있는데 GraphQL로 가야 할 이유가 있나?

  - 유연성의 증대와 시스템의 단순화가 목적인 GraphQL → 하나의 엔드포인드만 사용하여도 다양한 쿼리로 정보 획득 가능 → API 만드는 시간 감소 → 개발 시간의 증대
  - 타입체크를 클라이언트에서 하여 실수를 막아 줄 수도 있음
  - 전달될 데이터 양을 줄이고 받아올 데이터 양도 필요한 만큼만 받아온다 → 과유불급
  - 다양한 디자인패턴 적용하기가 수월하다.
    - 컴포짓 패턴으로 다양한 소스에서 데이터 한데 모아 볼때
      - ![image-20190725005546670](https://www.dropbox.com/s/gwr1dfs2v49g3sm/image-20190725005546670.png?dl=1)
    - 프록시 패턴으로 레거시 시스템에 인증을 추가할때(Resolver 사용하는 방법)
      - ![image-20190725005658089](https://www.dropbox.com/s/vv8r4yfev9pup3u/image-20190725005658089.png?dl=1)
- 이외 다양한 디자인패턴으로 사용 가능하다



## GraphQL을 공부 한 후기

처음에 아무것도 모르고 시작했는데 생각보다 자료가 많아서 어떤걸 배워야할지 또 개념은 뭔지에 대해 쉽게 접근 할 수 있었던 것같다.

처음에 왜 `무한한 공간 저 너머로` 라고 한 이유를 이제는 유추 할 수 있겠는가?
단 하나의 엔드포인트로 어디든지 갈 수 있는 그 장점이 내게는 버즈라이트이어의 한마디처럼 들린 것이다.

앞으로 모든 Router 들이 GraphQL 로 바뀌진 않겠지만, 서비스 규모가 커져 EndPoint가 많아지면 관리하는 문서가 많아지거나 정적인 여러 단점때문에 장점이 확실한 GraphQL로 바뀔 것 같다.

어떤 기술스택에도 붙을 수 있다는 장점 외에도 수많은 장점들이 있기 때문에 역으로 장점이 단점이 되는 기술 스펙이 될 수도 있다. 하지만 프론트엔드를 사랑하는 개발자로서 GraphQL은 개발에 날개를 달아줄 기술임은 분명하다.

기회가 된다면 본문에 소개한 `prisma`나 `Apollo`에 대해서도 포스팅 해보려고 한다. 

그리고, 이 글을 쓰고나서 다른 개발자분이 [GraphQL을 AWS AppSync에서 쓰는 법](https://dev.classmethod.jp/cloud/aws/aws-appsync-re-introduction-2019-korean-ver/?fbclid=IwAR2KRZ778M1UuWHe30FR3oliZpHIlQTIkvBJf7XkkJ5cRBapIXA_rVRA_tg)을 공유 해 주었는데 
`GraphQL 서버리스` 개념으로 사용 할 수 있는 방법 인듯 하여 한번 따라 해 보면 좋을 것 같다.



### 오늘 내용을 작성한 키노트 파일도 [SlideShare](https://www.slideshare.net/HyeonseokSeo1/graphql-overview-and-practice-159382659)에 깔끔하게 정리해서 (이쁘게) 올려 놓았다.

* 이것때문에 팀장님이 꾸미는건 좋은데 사내세미나니 안꾸며도 하셨지만 꾸미는게 좋은걸 어떻게해... 다음엔 꾸안꾸st로 해보겠읍니다..

![image-20190731110634070](https://www.dropbox.com/s/p1cndsgbo4keiwg/image-20190731110634070.png?dl=1)





------

### Reference

- [가장 현대적인 웹을 만들자 3편 (GraphQL))](https://medium.com/@kiyeopyang/가장-현대적인-웹을-만들자-3편-graphql-cb69ce1a64b5)
- [GraphQL 공식 홈페이지](graphql.org)
- [GraphQL을 오해하다](https://medium.com/@FourwingsY/graphql을-오해하다-3216f404134)
- [when-and-why-to-use-graphql](https://medium.com/@JeffLombardJr/when-and-why-to-use-graphql-24f6bce4839d)
- [GraphQL이 무엇인가](https://velopert.com/2318)

