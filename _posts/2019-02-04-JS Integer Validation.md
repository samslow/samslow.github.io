---
layout: post
title:  "JS Integer Validation"
subtitle:   "JS Integer Validation"
categories: development
tags: web
comments: true
---
회사에서 Javascript로 입력을 받는 알고리즘을 짠 경험중 정수검사( Integer Validation )을 한 경험을 나눕니다.

------

자바스크립트에서 정수 검사를 평소에 어떻게 하는가?

사용하는 각자의 언어에서 제공해주는 함수를 사용 하는 방법도 있고 직접 정규표현식을 돌려서 맞추는 방법도 있을 것이다.

오늘은 이런 전반적인 부분에 대해 얻은 지식과 삽질기를 공유하고자 글을 써 보았다.



총 3가지의 시도를 해서 문제를 해결했으며 시도별 과정을 아래에 기술한다.

#### 시도 1. DF에서 action에 value를 dafult로 설정해본다.

이미 앞의 글들을 몇개 읽고 온 독자들 이라면 현재 본인이 챗봇을 개발중인 플랫폼중 하나는 `Dialogflow`(이하 'DF') 라는 것을 알 것이다.

시스템의 리소스를 최대한 적게 사용하기 위해 DF에서 처리 가능하면 해당 방향으로 진행하기에 이번에도 이와같이 진행 해 보았다.

DF에는 각 `Intent`에는 `Action and parameters` 가 있는데

쉽게 설명하자면 프로그래밍에서 추상화 개념을 설명 할 때 `상자에 담아 보내준다` 라는 개념을 자주 사용하는 것 처럼(~~이해하는 사람만 이해하길 바란다~~)

해당 `intent` 가 발동될때마다 발견된 인자들을 각 엔티티에 맞게 예쁘게 포장 해 주는 역할을 하는 기능이다.

​<img src="https://www.dropbox.com/s/teennv2ybyaee4r/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202019-02-04%2002.05.36.png?raw=1" />

저 `action` 의 활용 방법은 정말 다양한데, 일단 여기서는 해당 `intent` 가 발동 될 때 마다 아래와 같이 값을 Default로 반환 해 주는 역할을 한다고 보면 된다.

<img src="https://www.dropbox.com/s/z50ag196321k7v3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202019-02-04%2002.08.18.png?raw=1" style="height:500px"/>

유저의 입력에대해서 숫자를 Catch해서 정수타입으로 바꿔주고 action은 내가 정한 String으로 잘 들어갔다.



이 방법은 해당 액션이 발동되면 정수가 들어오는 것이므로  `parseInt()` 로 바로 받아 줄 수 있겠지만 조금만 더 생각해보면 숫자가 없는 일반 String 타입을 처리 해 줄 수 없다는 한계를 바로 느낄 수 있다.



#### 시도 2. `typeof` 를 통해 `number`가 아닐때 `if-else` 사용

사실 이번 시도가 이 글을 쓴 이유기도 하다.

필자의 생각과 Javascript의 생각(?)이 달라서 안되는 이유를 찾아보았더니 의외였기 때문이다.

먼저 `시도1`이 DF에서의 검사였다면, 이번시도는 Webhook이 걸릴 서버에서의 검사이다.(정확히는 회사 사내 플랫폼 서버지만)

이 방법마저 안 된다면 내가 구동중인 서버까지 값을 저장해서 가져와야하기에 여기까지가 마지노선이라고 생각하고 고민해보았다.

이번 시도는 앞선 시도와 다르게 코딩을 할 수 있기때문에 `if-else` 를 사용하여 접근했다.

JS는 필요할때만 찾아서 쓰는 생존코딩으로 했기 때문에 어려웠던 부분이 아닐까 싶다.

문제의 핵심은 아래 코드와 같다.

```js
var _req = JSON.parse($request);
params = _req.queryResult.parameters.number;
goal = parseInt(params); // Google Assistant JSON으로부터 Input

if (typeof goal === 'number'){
    pof = "pass";
}else{
    pof = "fail";
}
console.log(pof) // "pass"
```

여기서 필자가 겪은 문제가 무엇인지 바로 파악 할 수 있다면 아래는 더 안 읽어도 무관하다.

핵심은 조건문을 걸었지만 결과는 늘 pass가 나오는 문제다.

`parseInt()` 를 걸면 당연히 숫자가 있을 땐 타입이 `number`여아 하고 이외엔 `NaN`이라는 타입이니 

당연히 else로 가야하는게 정상이라고 생각했다.

NaN을 Js에선 Null쯤으로 취급한다고 생각해서 난 오류인데 

<img src="https://www.dropbox.com/s/zl279q9ry4lkzqc/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202019-02-04%2002.39.29.png?raw=1" />

검색을 해도 `잘못된 입력` 이라는 키워드만 봐도 Null과 굉장히 유사하다고 생각되고

약어를 살펴봐도 `Not A Number`이기때문에 왜 if문으로 계속 들어가는가에 대한 의문을 가지게 되었다.

하지만, 글머리에서 서술 한 것처럼 JS의 생각은 나와 달랐고, 이건 JS가 설치 해 둔 함정에 빠진 느낌까지 들었다..

<img src="https://i.ytimg.com/vi/lTlObkX0paI/hqdefault.jpg" />

오류의 요점은 NaN이 `number`로 인식되어서 실패한다는 것인데, NaN을 단순하게 `숫자가 아니다` 라고 인식했기 때문이다.

그리고 NaN에 대해서 별도로 구글링을 해보니 JS에서는 NaN조차도 number에 한 종류로 취급되고 있었다.

`NaN` 은 unordered data type이고 숫자는 또 아니어서 아래의 결과가 나타난다.

```js
NaN < 1;    // false
NaN > 1;    // false
NaN == NaN; // false
isNaN(NaN); // true
```

Js가 정말 멋진 언어라고 생각이 들다가도 이럴때 보면 사람을 당황시키는 언어인것 같기도 하다.

NaN에 대해서 더 알고 싶으신 분들은 [javascriptrefined 미디움](https://javascriptrefined.io/nan-and-typeof-36cd6e2a4e43)에서 확인해 보시길 바란다. 



#### 시도 3.  `isNaN(value)` 을 통해 검사하기

그럼, 이제 문제를 알았으니 최종장에 들어설 자격을 갖추었다.

이제는 number로 검사할 것이 아니라, parseInt의 반환형을 확인해 숫자가 들어올때와 아닐때를 구별 해 주면 됐다.

그 방법으로 `isNaN()` 함수를 사용 해 NaN일때는 fail로 가는것을 아래 코드에서 확인했다.

```javascript
var _req = JSON.parse($request);
params = _req.queryResult.parameters.number;
goal = parseInt(params);

var _check = isNaN(goal);
if (!_check){
    pof = "pass";
}else{
    pof = "fail";
}
```

다만, 여기서 추가적으로 알아가야할 것은 `isNaN()`과 `Number.isNaN()` 이 다르다는 것인데, [MDN문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/isNaN)에서는 후자가 ES5부터 사용되는 규격이며, 더 엄격하게 검사하니 이를 사용할 것을 권한다.

아래 코드에서 다른 점을 확인 해보자 

```js
isNaN('123') //false
isNaN('samslow') //true
Number.isNaN('samslow') //false
```

충분히 이상하고 혼란스러운 케이스를 발견했는가? `Number.isNaN()` 을 사용 해야 할 이유를 알겠다면 성공이다.



글을 다 쓰긴 했는데 기술적인 부분을 다루는 글이다보니 충분히 필자와 다른 의견도 있을 것이고 틀린 부분도 있을 텐데

댓글로 이런 부분에서자유롭게 의견을 주시고 가르쳐주시면 감사하겠습니다.

글이 도움이 되셨길 바라며 이만 줄이겠습니다.

