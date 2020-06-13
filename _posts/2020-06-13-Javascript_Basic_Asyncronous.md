---
layout: post
title: "Javascript 기초 - 비동기 통신"
subtitle: "Javascript 기초 - 비동기 통신"
categories: development
tags: web
comments: true
---

- Javascript에서 비동기 통신하는 방법의 초기 부터 어떤 식으로 발전했는지 살펴봅니다.
  - XMLHttpRequest(XHR), Ajax, Promise, Async-await

# 비동기 통신이 필요한 이유

Synchronous(동기식)에서 `반대의-` 라는 의미를 가진 A- 를 붙임으로써 Asynchronous(비동기식)이 나오게 된다.

초기의 HTML은 Static한 성질을 가지고 있었고, 각 페이지의 이동은 Anchor tag의 href 속성을 통해 서로 다른 화면으로 이동이 가능했다.

하지만, 이러한 방식대로라면 모든 페이지를 이동할때 화면의 깜빡임이 발생하게 되고 이는 좋지 않은 유저경험까지 이어지게 될 뿐더러 하나의 Static한 html 파일에 용량이 큰 Assets나 Library를 사용하게되면 화면이 완전히 뜰때까지의 시간이 길어지게 된다. 또한 전체 페이지가 리로드되어 중복되는 시멘틱 태그도 다시 보여주기 때문에 비효율적이다.

싱글스레드로 동작하는 Javascript Engine과 위의 문제점을 해결하기 위해 비동기 통신이 등장했다.

비동기 통신은 처음 화면에 꼭 보여주지 않아야 할 것들은 순차적으로 로딩하면서 첫 화면이 빠르게 보여지게 할 수 있고, 다른 화면으로의 이동을 자연스럽게 한다.

예를들어 Lazy Loading이나 Infinite Scroll이 비동기 통신을 활용한 예시이다.

Javascript의 내장 객체에서 지원 해 주는 `XMLHttpRequest` 객체가 있고, 이를 쉽게 사용 할 수 있도록 해주는 라이브러리가 `Jquery`의 `Ajax` 이고 이외에도 axios나 Promise 등이 있다.

아래에서는 이러한 여러 개념들을 구현 방식으로 분류해서 살펴보겠다.

# Javascript Engine의 setTimeout()

setTimeout()은 정확히는 JS Engine의 API가 아니라 브라우저에서 지원하는 Web API의 일종이다.

그리고 이것을 이해하기 위해선 JS의 싱글 스레드 동작 방식과 이벤트 루프에 대해서 이해하고 넘어가야 한다.

후술할 Promise 패턴이나 Async-Await를 제대로 이해하기 위해서는 이같은 개념이 필요하다.

JS 이벤트 루프에 대해서는 별도의 글을 작성하겠지만, 간단히 정리하자면 아래와 같다.

![](https://www.dropbox.com/s/fq1rxbl9xvorcep/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-06-13%2012.43.59.png?dl=1)

- JS Engine에는 프로세스가 실행될 Memory Heap과 그 순서를 담은 Call Stack이 있다.
- Web API는 Browser에서 DOM을 조작하기 쉽도록 하는 여러 메소드를 제공한다.
- Event **Loop(중요!)는** TaskQueue(CallbackQueue)와 Call Stack을 지속적으로 관찰하며 CallStack이 비었을 때 Task Queue를 비워간다.

이러한 흐름을 JS EventLoop라고 한다.

setTimeout()을 적절히 활용하면 아래와 같이 비동기 통신을 구현 할 수 있다.

```js
function addTen(x) {
  return x + 10;
}

setTimeout(function () {
  num = 5;
  result = addTen(num);
  console.log(result); // 15
}, 1000);
```

1초 후에 callback함수를 통해 addTen()이라는 함수를 실행하는 예제이다.

위 예시에서는 add()라는 간단한 함수를 사용했지만 이를 HttpRequest로 생각 할 수도 있다.

단, 여기서 주의하여야 할 점은 TaskQueue 에 있는 callback들은 실행 될 때의 클로저로 변수를 파악하기 때문에 CallStack이 비워지고 나서 정해진 변수들을 사용하므로 이를 고려해 별도의 callback함수에 변수를 지정하는 방법을 사용할 수 있다.

# XMLHttpRequest

간단하게 XMLHttpRequest는 아래와 같은 구조로 동작한다.

```js
function XMLHttp() {
  let req = new XMLHttpRequest(); // XMLHttpRequest 객체
  req.onreadystatechange = function () {
    // 서버가 데이터를 반환했을때 발생하는 이벤트 핸들러
    if (this.status == 200) {
      // 요청한 데이터를 문자열로 반환
      document.getElementById("text").innerHTML = req.responseText;
    }
  };
  // GET 요청 작성
  req.open("GET", "/examples/media/xml_httpxmlrequest_data.txt", true);
  // 요청 전송
  req.send();
}
```

여기서 중요한 것은 `req.open()`의 3번째 인자로 `true` 를 전달 해 주었다는 점이다.

2번째 자리까지만 쓰면( default는 `false`), 해당 코드는 더이상 아래로 진행되지 않고 동기식으로 처리되어 요청이 완료될때까지 기다리게된다.

만약 여기서 `false` 옵션을 사용하게 되면 코드 수준의 동기가 되기 때문에 브라우저가 멈추므로 유의하여 사용하여야 한다.

예를들어, 무언가를 로딩하게 되면 브라우저의 모든 이벤트나 버튼들이 먹통이 되는 것이다.

이 방법은 EventListener와 http 요청을 위한 작업등을 하나씩 사용하고 실제 요청을 send()로 보내는 것등을 직접 해 주어야 한다는 것인데, 직관적이라는 장점이 있지만 코드의 Line 수가 늘어나 가독성을 해치게 된다.

위 객체를 이용하면 위에 사용한 JS setTimeout() 보다는 더 유려하게 코드를 작성 할 수 있다.

요새는 개선된 방법이 더 많기 때문에 상대적으로 Low Level인 위 방법은 잘 사용되지 않고 Library를 활용한 Request가 선호된다.

계속해서 아래로 갈수록 더욱 개선된 방법이 소개 된다.

# JQuery

Vanila JS의 DOM 조작이 가독성이나 코드 조작 면에서 불편하여 나오게 된 것이 JQuery 이다.

물론 JQuery가 사용되는 원리를 이해하지 못하고 짠 코드라면 getElementById같은 WebAPI도 10배 이상 느려지는 신세계를 경험 할 수 있고, 사용 자체가 쉽다보니 코드에 사족이 많아지는 단점이 있으므로 주의해야한다.

JQuery의 ajax를 사용하여 비동기 통신을 하는 기본적인 방법은 아래와 같다

```js
$.ajax({
  url: "https://samslow.github.io/example.txt",
  type: "GET",
  data: {
    // 보낼 데이터
  },
  done: function (response) {
    // 성공 시 동작
  },
  fail: function (error) {
    // 실패 시 동작
  },
});
```

위에 소개된 것과 상반되게 정말 쉽게 사용 할 수 있기 때문에 더 주의해야 하는 코드이다.

비슷하게 Axios나 request.js같은 라이브러리를 사용하는 방식은 사실 위의 코드와 많이 비슷하다.

# Promise

Javascript ECMA2015부터 소개된 Promise 객체는 MDN에서 아래와 같이 설명한다.

> **`Promise`** 객체는 비동기 작업이 맞이할 미래의 완료 또는 실패와 그 결과 값을 나타냅니다.

즉, 최종 결과를 동기식으로 멈추면서 제공하지 않고 Promise라는 객체를 반환하여 callback을 받을 준비만 하는 것을 의미한다.

[이전 포스트](https://samslow.github.io/development/2020/06/09/Javascript_Basic_Prototype-Chaining/)에서 Prototype을 배웠으니 Promise의 prototype을 살펴보면 아래와 같다.

![](https://www.dropbox.com/s/d9ee6cz03yayvbk/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-06-13%2013.31.26.png?dl=1)

callback을 준비하는 객체라고 한 것 처럼 then(), catch(), finally() 등의 메소드로 비교적 간단하게 구성되어있다.

이것을 조금 더 쉽게 이행하면 3가지 상태로 분류 할 수 있다.

- 대기(_pending)_: 이행하거나 거부되지 않은 초기 상태.
- 이행(_fulfilled)_: 연산이 성공적으로 완료됨.
- 거부(_rejected)_: 연산이 실패함.

pending은 resolve되거나 reject 되는것을 기다리는 상태이고, fulfiled는 이것이 결정 된 상태, 거부는 catch()로 잡히는 에러나 reject을 처리한다.

실제 동작하는 코드는 아래와 같다.

```js
$.get("https://samslow.github.io/logo.png")
  .then((result) => {
    document.getElementById("Avatar").src = result.data;
  })
  .catch((result) => {
    console.error("Avatar load Fail");
  });
```

어떤가. 위에 소개된 Ajax의 장점은 취하고 단점이었던 코드의 성능은 덤으로 향상 될 것이다.

하지만, Promise가 단순할때는 문제가 되지 않지만, 3~4중이상으로 callback function이 실행된다면 가독성이 심히 나빠진다.

이것을 `Callback Hell`이라고도 부르는데 이를 개선하기 위해 ECMA2017(ES8)에서 소개된 Async-Await를 사용 할 수 있다.

# Async-Await

Promise 패턴의 단점은 아직도 나아지지 않은 callback Hell과 이해해야 할 복잡한 개념이 많다는 것이다.

필자가 처음에 Promise를 접했을 떄도 이를 이해하기까지 여러 시행착오를 겪어야 했다.

Async-Await는 이런 문제점들을 잡아주고, 형재는 비동기 통신의 비교적 완성형으로 널리 사용되고있다.

하지만, 역시 Promise 객체를 기반으로 동작하기 때문에 이를 이해하는것은 조금 미루더라도, 그 개념은 꼭 짚고 넘어가야한다.

이 둘이 세트인 이유는 Async로 선언되어야만 Await로 동기와 비동기를 구분지을 수 있게 되기 때문에 바늘가는데 실가듯 항상 같이 따라오는 것이다.

위의 소개된 것들과 같이 역시나 이것도 단점은 존재하는데, JQuery처럼 잘못 사용하면 병목현상을 유발 할 수 있다는 점이다.

이것에 관해선 [더 잘 소개된 포스트](<[https://medium.com/@sijk0331/javascript%EC%97%90%EC%84%9C-async-await%EC%9D%98-%EB%B3%91%EB%AA%A9-%EB%AC%B8%EC%A0%9C-%EA%B0%9C%EC%84%A0%ED%95%98%EA%B8%B0-60d54795c4fa](https://medium.com/@sijk0331/javascript에서-async-await의-병목-문제-개선하기-60d54795c4fa)>)가 있으니 이것을 읽고 오길 바란다. 잘만 사용하면 획기적으로 비동기 통신 성능을 개선 할 수 있으니 가볍게 읽고 오면 좋다.

![](https://ww.namu.la/s/622f344027a76589bb7f5e7458392cb354f3db63738a7d2b060d04adf2162c306b971c84ffb061e281f0fc35196e95b112043578e5cf56e263e8a655a1ad3a1fb998a10470c9d821775f470e695d05dc6ba1696f20748cbeb24f8b414a0fb934)

제일 위에 소개된 XMLHttpRequest가 디지몬중에 아구몬이었다면 궁극체인 워그레이몬은 Async-Await는 아래와 같이 사용 할 수 있다.

```js
// inner async function

// Load Image from server
const url = 'https://samslow.github.io/logo.png';
const response = await $.get(url);
const applyLogo = document.getElementById('Avatar').src = response;

// Upload image
const uploadNewLogo = await function () => {
	const logoImgPath = document.getElementById('uploaded').path;
  const logoImg = base64(require(logoImgPath));
  const req = $.post(url, {encoded: logoImg})

  return req;
}
if(uploadNewLogo.status == 200){
  console.log("Good response")
}else{
  console.log("Something wrong")
}
```

여기서는 await를 사용함으로써 코드를 callbackHell로 빠지지 않게 한다는것만 알고 넘어가도 된다.

# Summary

- 비동기 통신은 JS EventLoop에 기초를 두고 JQuery, Promise 패턴이 동작한다.
- 비동기 통신의 궁극체인 Async-Await 를 잘 사용하면 좋지만, 상황에 맞게 Promise 패턴을 적절히 사용하면 유용 하다.
- 점점 비동기 통신은 개선되지만 그 개념은 변하지 않는다.

이상으로 Javascript 기초 - 비동기 통신 글을 마칩니다.

전체적인 코드는 완벽하지 않을 수 있으니 참고용으로만 봐주시고, 만약 부족하거나 잘못된 개념이 있다면 댓글로 알려주시면 수정 하겠습니다. 비난대신 비판으로 함께 해 주세요.

# Reference

- [D7D Lab](https://m.blog.naver.com/dndlab/221783285664)
- [hudi.kr](<https://hudi.kr/%EB%B9%84%EB%8F%99%EA%B8%B0%EC%A0%81-javascript-%EC%8B%B1%EA%B8%80%EC%8A%A4%EB%A0%88%EB%93%9C-%EA%B8%B0%EB%B0%98-js%EC%9D%98-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC-%EB%B0%A9%EB%B2%95/](https://hudi.kr/비동기적-javascript-싱글스레드-기반-js의-비동기-처리-방법/)>)
