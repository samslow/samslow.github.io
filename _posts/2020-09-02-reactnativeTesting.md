---
layout: post
title: "React Native에서 TDD 하기"
subtitle: "React Native에서 TDD 하기"
categories: development
tags: reactnative
comments: true
---

- 이 글에서는 [React Native Testing Library](https://callstack.github.io/react-native-testing-library/docs/getting-started/) 를 가지고 React Native에서 컴포넌트를 테스팅 하는 방법을 다룹니다.

# React Native Testing

TDD가 한번 나오더니 우후죽순으로 테스트를 하려는 조직이 늘어났다. 기존에도 이미 Testing은 꾸준히 사용되어 왔지만, '지금 잘 되는데 왜 굳이?'라며 잘 시도하지 않았고 코드가 늘어날 뿐이라며 시도하지 않던 분위기와는 사뭇 다르다.

또 약간 계륵같은것이 그렇다고 안하자니 집에 에어컨을 틀고나온 것 같은 찝찝함에 '언젠간 할거야!'라는 불편함을 주는 부분이기도 하다.

하지만 점점 서비스들의 코드가 순식간에 불어나 예상치 못한 오류들을 잡기 위해 몇일 밤을 지새고, 별것 아닌 곳에서 에러를 발견했을때 비로소 '처음부터 테스트 코드 짤걸..' 하는 후회를 주변에서 종종 듣는다.

오늘은 미래에서 온 필자 자신 혹은 독자에게 이 '처음부터 테스트 코드 짤걸···'에서 '처음부터'의 시점에 왔다고 생각하고 글을 써보려고 한다.

이 글은 Jest기반으로 React의 React Testing Library에서 영감을 받은 React Native의 React Native Testing Library를 기본으로 사용해 react native testing tutorial방식으로 설명하려고 한다.

# 테스트를 계속 하는 사람은 있어도 한번만 한 사람은 없다

개발자 김두부는 react native로 할일 관리 앱을 6개월간 개발해서 런칭했다. 시장 반응이 좋아서 유저들은 계속해서 새로운 기능을 요구했고 할일 관리에 이미지 추가, 알람 설정 등의 다양한 기능들을 계속해서 추가하고 있다.

새로운 기능들이 추가될 때마다 모든 기능들이 잘 동작하는지 테스트하기 위해 테스팅 시나리오 테이블을 만들어 손수 앱을 다 테스트 하려고한다.

이번에는 기존에 이미지 업로드 기능이 갑자기 동작하지 않았는데, 알고보니 최근에 추가한 이미지 선택 관련 코드가 이미지 업로드 코드에 영향을 미쳐서 버그가 발생했고, 이를 테스트해보지 못하고 프로덕션 릴리즈 후에 이를 깨달은 개발자 김두부는 눈물을 머금고 남들 다 퇴근하고 있을 때 hotfix branch를 만들어 작업을 시작했다.

이렇게 가끔가다 시나리오를 빼먹을 때도 있고 안일하게 넘어가다가 이미지가 제대로 업로드 되지 않았다거나, 할일 수정이 되지 않는다거나 하는 문제를 일으키는 적이 한두번이 아니다.

테스트 자동화는 이런 과정의 시간을 모두 줄여주고 CI 단계에서 문제를 발견할 수 있도록 해 결국 코드 질의 향상으로 이어지도록 돕는다.

소제목 '테스트를 계속 하는 사람은 있어도 한번만 한 사람은 없다'는 과장이 좀 있긴 하지만 한번 테스트를 겪어 본 사람은 간단하게라도 테스트를 작성하는 재미와 효율성을 느낀 사람이기 때문에 계속 할 가능성이 높다는건 직접 해보니까 알 수 있던 부분이었다.

단순하게 화면이 잘 나오도록 하는 스냅샷 테스팅은 쉽게할 수 있기 때문이기도 하다.

# 테스트의 종류

테스트의 종류에는 여러가지가 있지만, 가장 간단하고 자주 사용되는 테스트는 아래 2가지가 있다.

1. 유닛 테스트: 가장 조그마한 단위로 테스트하여 하나의 컴포넌트씩 테스트하는 것
2. 통합 테스트: 다른 컴포넌트들끼리의 상관 관계까지 테스트하는 것

당장 이해가 가지 않는다고해서 걱정하지 말자. 컴포넌트 기반으로 테스트를 하다보면 나도 모르게 어느새 이런 테스트를 고려해서 짜고있는 모습을 볼 수 있다.

그래도 이해를 조금 더 돕기 위해 아래 유닛 테스트와 통합 테스트의 가벼운 짤방을 위 설명과 함께 다시 보자.

![img](https://cdn-images-1.medium.com/max/1600/1*KoTFh3xRPgkzD0FlzsYKjA.gif)

![img](https://cdn-images-1.medium.com/max/1600/1*4-T6VVnULaszi9ydHQ7Sfw.gif)

# 한번 뽑은 칼이라면 프로젝트에 설치는 해 봐야지

지금부터는 실제 코드를 기반으로 어떤 식으로 테스트 코드를 짤 수 있는지 A-Z 로 실습을 해 보자.

우리가 만들 앱은 단순하게 유저가 들어와서 버튼을 눌러 출퇴근을 찍는 서비스를 만들어본다.

```shell
$ npx react-native init testingBasic # 프로젝트 시작
$ yarn ios # ios 시뮬레이터 빌드
```

버전에 따라 다르겠지만 처음 설치를 하면 아래와 같은 의존성을 갖는다.

```json
"dependencies": {
  "react": "16.13.1",
  "react-native": "0.63.2"
},
"devDependencies": {
  "@babel/core": "^7.11.5",
  "@babel/runtime": "^7.11.2",
  "@react-native-community/eslint-config": "^2.0.0",
  "babel-jest": "^26.3.0",
  "eslint": "^7.8.1",
  "jest": "^26.4.2",
  "metro-react-native-babel-preset": "^0.63.0",
  "react-test-renderer": "16.13.1"
},
```

여기서 주의깊게 보아야 할 몇가지 library는 `jest` `react-test-renderer` 이다.

`react-test-renderer` 는 React 컴포넌트를 순수한 JS객체로렌더링하는데 사용 할 수 있는 React 렌더러이다.

기본으로 설치되기 때문에 폴더 구조에도 아래와 같이 테스트 폴더가 내장되어있다.

이때 사용되는 `__tests__` 폴더 내부에 있는 파일은 기본적으로 `jest`로 테스트시에 자동으로 테스팅 파일에 포함이 되는데,

그 이유는 `packages.js`에 있던 `jest`의 `preset`이 `react-native`로 되어있기 떄문에 별도의 복잡한 설정 없이 사용 할 수 있는 것이다.

이는 jest의 장점 중 하나인 `Zero config` 이기도 하다.

![내장된테스팅](https://www.dropbox.com/s/zyvgqjo2axd2zvc/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2018.30.20.png?dl=1)

우리는 기본 테스팅 라이브러리 대신 React Native Testing Library를 사용 할 것이고 autocomplete도 지원되길 바라기 때문에 아래 라이브러리들을 함께 설치한다.

단 모든 테스팅은 프로덕션 릴리즈에는 포함될 필요가 없기 떄문에 개발 의존성에만 추가 해준다.

```shell
$ yarn add -D @testing-library/react-native @types/jest
```

먼저 기본적인 디자인 작업을 해준다.

```js
// ./App.js

import React from "react";
import { StyleSheet, Text, View } from "react-native";

import Profile from "./src/Profile";

const App = () => {
  return (
    <View style={styles.container}>
      <Profile userName={"samslow"} name={"서현석"} />
    </View>
  );
};

export default App;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: "center",
    justifyContent: "center",
  },
});
```

기본 큰 틀은 `App.js`를 위 내용으로 덮어써서 구성하고

아래 `Profile.js` 와 `Greeting.js` 는 `src` 폴더를 만들어 각각 파일을 생성 해준다.

```js
// ./src/Profile.js

import React, { useState } from "react";
import { StyleSheet, Text, View } from "react-native";
import Greeting from "./Greeting";

const Profile = ({ userName, name }) => {
  const [msg, setMsg] = useState("Select your status");

  return (
    <View style={styles.container}>
      <Text style={styles.textBox}>
        {userName}({name})
      </Text>
      <Text style={styles.textBox}>{msg}</Text>
      <Greeting title="Bye!" onPress={() => setMsg("Seeya!")} />
      <Greeting title="Hello!" onPress={() => setMsg("Welcome!")} />
    </View>
  );
};

export default Profile;

const styles = StyleSheet.create({
  container: {
    alignItems: "center",
  },
  textBox: {
    marginBottom: 15,
  },
});
```

```js
// ./src/Greeting.js

import React from "react";
import { Button } from "react-native";

const Greeting = ({ title, onPress }) => {
  return <Button title={title} onPress={onPress} />;
};

export default Greeting;
```

![레이아웃 끝](https://www.dropbox.com/s/nj2bdshg687m46a/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2018.40.05.png?dl=1)

모든 디자인과 레이아웃 작업이 끝났다.

이제 현재사진을 찍어 Snapshot으로 저장해 매 테스트마다 처음 상태와 같은 사진이 나오는지 비교 해 보는 테스팅인 스냅샷 테스팅을 해보자

실제로 캡처를 뜬다거나 하지는 않으면 스냅샷 파일을 가지고 사진을 찍듯 비교를 하기때문에 직접 코드로 볼 수 있다.

```js
// ./__tests__/App-test.js

import React from "react";
import App from "../App";
import { render } from "@testing-library/react-native";

let props;
let component;

function getTempComponent(props) {
  return <App {...props} />;
}

describe("[App] render", () => {
  props = {}; // fill test props
  component = getTempComponent(props);
  test("renders without crashing", () => {
    const rendered = render(component);
    expect(rendered).toMatchSnapshot();
    expect(rendered).toBeTruthy();
  });
});
```

위 코드는 `App.js`컴포넌트를 불러와서 우리가 의도한 `props`를 주입하여 스냅샷을 비교하는 테스트이다.

jest 메서드인 `describe`는 각각의 `test` 들을 그룹핑 하도록 해주고 모든 테스트는 `test` 메서드로 할 수 있는데 `test` 는 alias로 `it` 이라는 메서드로도 사용할 수 있다. 차이점은 없고 `it` 을 사용하면 첫번째 매개변수와 함께 자연스러운 문장처럼 읽히게 할 수 있다.

`component` 를 `render` 하고 `expect` 를 통해 프로그램에게 테스트 대상이 어때야 한다는 내용을 전달 해주면 된다.

- 스냅샷이 기존 스냅샷과 일치하는지 → `expect(rendered).toMatchSnapshot();`

- 컴포넌트가 `null`, `undefined` 같이 `falsy` 한 값을 가지지는 않았는지 → `expect(rendered).toBeTruthy();`

프로젝트 터미널에 `jest` 혹은 `yarn test` 를 통해 테스트를 실행 해 보자.

처음에 이 테스트를 실행하면 `Snapshot missing` 경고가 나오는데 당연하게도 우리는 스냅샷을 찍은적이 없기 때문에 이런 경고가 나온다.

이럴때는 `yarn test -u` 로 최신 스냅샷을 다시 찍어 업데이트 한다는 옵션을 함께 주면 된다.

그럼 `__tests__`디렉토리에 `__snapshots__` 폴더가 생성되며 아래와 같은 내용을 볼 수 있다.

![스냅샷](https://www.dropbox.com/s/x7bzbzbsdj5ur6v/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2018.55.49.png?dl=1)

뭔가 많이 익숙하면서도 미묘하게 다른 코드를 볼 수 있는데 이게 바로 앞으로 테스팅의 기준일 될 스냅샷이다.

스냅샷 테스팅은 UI가 예측못하게 바뀌는 걸 방지하도록 할 때 사용되는 유용한 툴이다.

만약 화면에 보이는 요소가 하나라도 변경된다면 다시 스냅샷을 찍어야 한다는 점만 기억하자.

사실 여기까지 이해했다면 테스팅에 대한 모든것을 알았다고 해도 무방하다.

이후에 설명하는 내용들은 [react native testing library 공식문서](https://callstack.github.io/react-native-testing-library/)에서 설명하는 Queries에 대한 설명이 될것이며 위 내용과 비슷할 것이다.

이번에는 `Greeting` Button에 대해서 위에서 설명한 컴포넌트 하나하나를 테스트하는 `단위 테스트`를 해보자.

```js
// ./src/__tests__/Greeting-test.js

import React from "react";
import { render, fireEvent } from "@testing-library/react-native";
import Greeting from "../Greeting";

describe("[Greeting] Test", () => {
  const onPressMock = jest.fn();
  const props = {
    title: "Hello!",
    onPress: onPressMock,
  };

  test("버튼이 눌린다.", () => {
    const rendered = render(<Greeting {...props} />);

    for (let i = 0; i < 5; i++) {
      fireEvent(rendered.getByText("Hello!"), "onPress");
    }
    expect(onPressMock).toBeCalledTimes(5);
    expect(rendered.toJSON().children[0].children[0].children[0]).toEqual(
      "Hello!",
    );
  });
});
```

`CRA`에서 이미 `jest`에 대한 `preset`으로 `react-native` 가 있다고 위에서 설명 한 이유로 `__tests__` 폴더를 만들어 규칙에 맞게 테스트 파일을 만들어준다.

이번 테스트에서는 `jest.fn()` 이라는 새로운 `jest` 함수가 등장했다.

`jest.fn()` 은 mocking 하는 목적으로 사용되었는데, 우리가 실제로 함수를 사용할 수도 있지만 실제 함수를 호출하기 어려운 상황이나 꼭 그럴 필요가 없을때 '이 함수가 있겠다~' 하고 만들어 두는 가짜 함수라고 볼 수 있겠다.

이를 통해서 우리는 해당 함수가 얼마나 호출되었는지 어떤 매개변수와 실행되었는지 같은 내용을 추적 할 수 있게 되었다.

또 `fireEvent` 가 새로 보이는데 이 아이는 `onPress`, `onChange`,`scroll` 같이 어떤 이벤트로써 상호작용하는 컴포넌트들을 트리거하는데 사용한다.

위의 예시에서는 `Hello!` 라는 텍스트가 있는 컴포넌트를 5번 누르고 5번이 잘 눌렸는지와 눌려진 컴포넌트의 텍스트가 변경되지 않았는지 테스트한다.

마지막으로 `getByText` 는 매개변수로 전달된 텍스트를 가진 컴포넌트 노드가 존재하는지 테스트함과 동시에 그 값을 리턴하는 함수이다.

이런 메서드를 테스팅 라이브러리에서는 Queries로 통칭하는데 Variant로 알려진 접두사와 Queries로 알려진 접미사의 합성으로 구성된다.

- Variant
  - getBy\* : 조건에 일치하는 엘리먼트를 하나 선택한다. 없다면 테스트 실패로 처리한다.
  - getAllBy\* : 조건에 일치하는 엘리먼트를 여러개 선택한다. 없다면 테스트 실패로 처리한다.
  - queryBy\* : 조건에 일치하는 엘리먼트를 하나 선택한다. 존재하지 않아도 실패는 하지 않는다.
  - queryAllBy\* : 조건에 일치하는 엘리먼트를 여러개 선택한다. 존재하지 않아도 실패는 하지 않는다.
  - findBy\* : 조건에 일치하는 엘리먼트를 하나 선택한다. 단 Promise 객체를 리턴하며 조건에 만족하는 엘리먼트가 나타날 때 까지 기다리고 만약 4500ms 내에 발견되지 않으면 테스트는 실패한다.
  - findAllBy\* : 조건에 일치하는 엘리먼트를 여러개 선택한다. 단 Promise 객체를 리턴하며 조건에 만족하는 엘리먼트가 나타날 때 까지 기다리고 만약 4500ms 내에 발견되지 않으면 테스트는 실패한다.
- Queries( 주로 사용하는 것만 다룸 )
  - ByText : 엘리먼트가 가진 텍스트 값으로 선택
  - ByAltText : 엘리먼트가 가진 alt 속성으로 선택
  - ByTestId : 엘리먼트가 가진 TestId 속성으로 선택

누군가는 ByTestId만으로 테스트를 모두 할 수 있을 것 같다고 하지만, 테스트를 위해 코드에 testId를 넣는 것은 가독성을 해치므로 지양해야한다.

Greeting에 대한 단위테스트가 끝났다면 이제 한단계 위의 컴포넌트인 Profile로 이동해 함께 있어도 잘 동작하는지 통합 테스트를 진행하면 된다.

```js
// ./src/__tests__/Profile-test.js

import React from "react";
import { render, fireEvent } from "@testing-library/react-native";
import Profile from "../Profile";

describe("[Profile] Test", () => {
  const props = {
    userName: "samslow",
    name: "서현석",
  };
  test("유저 이름이 알맞게 나온다.", () => {
    const rendered = render(<Profile {...props} />);
    rendered.getByText(/samslow/);
  });
  test("버튼이 눌리면 메시지가 적절히 변경된다.", () => {
    const rendered = render(<Profile {...props} />);
    // rendered.debug();
    rendered.getByText("Select your status");
    const byeBtn = rendered.getByText("Bye!");
    const helloBtn = rendered.getByText("Hello!");
    fireEvent(byeBtn, "onPress");
    rendered.getByText("Seeya!");
  });
});
```

이번 통합 테스트에서는 기존에 나왔던 메서드들을 그대로 사용했기 때문에 새로운 내용은 없지만, 이 테스트의 목적이 무엇인지 상기하면서 작성해야한다.

통합테스트는 다른 요소들과 상호작용에서도 그 값이나 레이아웃이 깨지지 않는지를 보는 것이기 떄문에 화면에 렌더링된 요소들을 선택하고 또 그 요소로 인해 변경되는 다른 요소들의 값이 올바른지까지 체크해야한다.

이제 모든 테스트는 끝났다. 하지만 내 테스트가 올바로 모든 부분을 잘 처리 했는지 의문이 들지 않는가?

내가 작성한 코드가 모두 테스트 되었는지 알고싶다면 아래 명령어로 jest에서 제공하는 report를 확인하면 된다.

```js
$ yarn test --coverage
```

위 명령어를 실행하면 `coverage` 라는 폴더가 생성되며 `coverage report` 를 이용 할 수 있게 된다.

![coverage](https://www.dropbox.com/s/ekt8scfohtlfgt3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2019.36.42.png?dl=1)

현재 우리의 테스트코드의 전체 커버리지는 90%이다.

report를 보니 `Profile.js` 의 15번째 줄의 내용이 테스트 되지 않았다고 한다.

방금 생성된 `coverage` 폴더에서 index.html을 열어 바로 확인 해 보자

![커버리지 확인](https://www.dropbox.com/s/94wprtu27vzc6vk/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2019.41.06.png?dl=1)

![profile coverage](https://www.dropbox.com/s/33h5atmrt54vknh/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2019.41.29.png?dl=1)

리포트에서 확인 해 보니 누가봐도 잘못했다는 의미로 빨간색으로 잘못한 부분을 짚어준다.

Profile-test에서 Greeting 버튼 중 Bye 는 테스트 되었는데 Hello 는 테스트되지 않아서 그런 것 같다.

Profile-test에서 테스트를 조금 수정 해 보자

```js
import React from "react";
import { render, fireEvent } from "@testing-library/react-native";
import Profile from "../Profile";

describe("[Profile] Test", () => {
  const props = {
    userName: "samslow",
    name: "서현석",
  };
  test("유저 이름이 알맞게 나온다.", () => {
    const rendered = render(<Profile {...props} />);
    rendered.getByText(/samslow/);
  });
  test("버튼이 눌리면 메시지가 적절히 변경된다.", () => {
    const rendered = render(<Profile {...props} />);
    rendered.getByText("Select your status");
    const byeBtn = rendered.getByText("Bye!");
    const helloBtn = rendered.getByText("Hello!");
    fireEvent(byeBtn, "onPress");
    rendered.getByText("Seeya!");
    fireEvent(helloBtn, "onPress"); // 추가된 라인 1
    rendered.getByText("Welcome!"); // 추가된 라인 2
  });
});
```

이제 다시 `yarn test --coverage` 를 실행하면?

![coverage Complete!](https://www.dropbox.com/s/krs02qngnow3k9n/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202020-09-02%2019.43.59.png?dl=1)

축하한다. 이제 이 코드는 모든 경우의 수를 커버하는 코드이기 때문에 여러분은 자신을 더 믿고 다른 기능들을 추가 할 수 있는 무적의 인피니트 건틀렛을 얻었다! 세상을 반으로 쪼개보기도 하고(?) 또 붙여보기도 하자(?)

![타노스](https://m.maniahouse.co.kr/web/product/big/201804/4388_shop1_490593.jpg)

# 저기 근데.. Coverage Report는 어떻게 읽나요 ?

코드를 작성해서 테스트하긴 하는데 jest가 뭘 안다고 우리 코드의 커버리지를 스스로 측정할 수 있는지 궁금할것이다.

jest의 coverage는 총 4가지 종류를 기반으로 모든 코드를 검토한다.

1. Fucntion Coverage: 각 함수가 잘 호출되었는가?
2. Statement Coverage: 각 구문이 잘 실행되었는가?
3. Branch Coverage: 각 분기가 모두 테스트 되었는가(if-else statement), 모든 케이스가 실행 되었는가?
4. Line Coverage: 실행 가능한 라인이 잘 실행 되었는가?

이런 결과로써 퍼센트는 실행된 코드와 그렇지 않은 코드를 보여주는 것이다.

Coverage Report에서 각 심볼과 색상이 의미하는 것은 아래와 같다.

- `E`: 'else path not taken'으로 if/else에서 else가 테스트 되지 않은 경우
- `I`: 'if path not taken'으로 if/else에서 if가 테스트되지 않은 경우
- `xN`: 해당 행이 실행된 횟수 N
- `Red`: 실행되지 않은 줄이나 코드 조각
- `Pink`: statements 커버되지 않음
- `Orange`: functions 커버되지 않음
- `Yellow`: branches 커버되지 않음

위 내용만 알면 Coverage Report는 쉽게 읽을 수 있다.

구분하는게 다소 헷갈린다면 Report에서 Test failed가 떠있는 line을 친절히 보여주므로 해당 부분에 대한 테스트를 작성하기만 하면 된다.

# Jest 가는데에 Plugin 따라간다

VScode 터미널 근처에 보면 `output` 이라는 별도의 탭이 있는데, 이 탭을 jest 로 세팅 해 두면 저장할 때마다 자동으로 테스트를 돌려주는 멋진 기능을제공한다.

또 `VScode` 를 사용한다면 아래 Plugin들을 사용하는 것을 고려하자.

- [Output Colorizer](https://marketplace.visualstudio.com/items?itemName=IBM.output-colorizer)
  - 기본적으로 Output 탭은 다른 결과물도 함께 보여주기 때문에 터미널에서 처럼 컬러풀하지 않다. 색을 칠해주자
- [Jest](https://marketplace.visualstudio.com/items?itemName=Orta.vscode-jest)
  - 모든 테스트 옆에 이 테스트가 잘 통과했는지 여부를 표시해준다.
  - 스냅샷 갱신이 필요하다면 자동으로 버튼을 띄워준다.
  - 암튼 전체적으로 디버깅을 쉽게 해줌! 정말임!

# 마치며

여기서는 다루지 않은 `beforeEach`나 `afterEach`같은 유용한 API들도 있는데 한꺼번에 소개하긴 너무 많아 소개하지 않았다.

너무 많은 개념들이 나와 여러 개념들이 나와서 헷갈리는 부분이 있을 수 있다.

예를들어 jest는 뭐고 testing-library랑 어떤 차이가 있는지 같은 경우 jest는 테스팅 프레임워크로 우리가 따라야하는 규칙이며, testing-library는 말 그대로 라이브러로 우리가 컨트롤 할 수 있는 더 작은 개념이다.

하지만 결국 모든것은 테스팅의 일환이다. 이정도만 알아도 다른 부분들은 충분히 찾아서 해볼 수 있다. (Jest 공식 문서가 잘 되어있으니 참고하자)

이 글을 기반으로 이제 우리는 TDD를 시도 해 볼 수도 있게 되었고 복잡했던 테스트 파일을 쉽게 읽을 수 있게 되었다.

누군가는 테스트 할 시간에 한 줄이라도 더 작성해서 빨리 개발하는게 낫지 않냐고 할 수 있지만 적어도 이 글을 읽은 독자들 만큼은 테스트가 왜 중요하고 조직의 규모가 커질수록 어떤 의미가 있는지 이해할 수 있었으면 좋겠다.

# Reference

- https://velog.io/@velopert/react-testing
- https://medium.com/@krishankantsinghal/how-to-read-test-coverage-report-generated-using-jest-c2d1cb70da8b
