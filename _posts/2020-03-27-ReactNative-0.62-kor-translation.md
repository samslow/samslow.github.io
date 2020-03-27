---
layout: post
title: "Flipper와 함께하는 React Native 0.62 릴리즈노트"
subtitle: "React Native 0.62 with Flipper"
categories: development
tags: reactnative
comments: true
---

- React Native 0.62 버전이 공식 릴리즈되었습니다. 어떤것이 변했는지, 공식 릴리즈 노트를 번역 한 내용입니다.

# Flipper와 함께하는 React Native 0.62 버전 안내

2020년 3월 26일, Facebook React Native core 팀의 [Rick Hanlon](https://twitter.com/rickhanlonii) 작성

오늘 Flipper를 기본으로 내장한 ReactNative 0.62가 릴리즈 되었습니다.

이번 발표는 Global pandemic 중에 버전을 출시 할 것 입니다. 이 출시를 가능하게 한 수백 명의 contributor들에게 존경을 표하고 개발되고 있는 버전이 릴리즈 버전과 너무 뒤떨어지는 것을 막기 위해서 입니다. issue 해결과 필요시 업그레이드 지연을 준비할 contributor가 다소 부족한 것을 양해 해 주세요.

## Flipper가 기본으로 들어갑니다.

[Flipper](https://fbflipper.com/)는 모바일 앱들을 디버깅하는 개발 툴 입니다. 이미 Android나 iOS 커뮤니티에서 유명하고 이번 버전에서 React Native App에서도 기본으로 사용할 수 있게 되었습니다.

![Flipper](https://reactnative.dev/blog/assets/0.62-flipper.png)

Flipper는 아래 기능들을 즉시 제공합니다.

- **Metro Actions:** 앱을 다시로드하고 툴바에서 바로 개발자 메뉴를 트리거합니다.
- **Crash Reporter:** Android 및 iOS 기기에서 충돌 보고서를 봅니다.
- **React DevTools:** 다른 모든 도구와 함께 최신 버전의 React DevTools를 사용합니다.
- **Network Inspector:** 장치의 앱에서 오고 가는 모든 네트워크 요청을 봅니다.
- **Metro and Device Logs:** Metro 및 Device에서 모든 로그를 보고 검색하고 필터링합니다.
- **Native Layout Inspector:** React Native 렌더러의 기본 레이아웃 출력을 보고 편집합니다.
- **Database and Preference Inspectors:** 장치 데이터베이스 및 환경 설정을 보고 편집합니다.

추가적으로, Flipper는 확장 가능한 플랫폼이므로 NPM에서 플러그인을 가져오는 마켓 플레이스를 제공하므로 워크 플로에 맞는 사용자 지정 플러그인을 게시하고 설치할 수 있습니다. 사용 가능한 플러그인을 [여기](https://www.npmjs.com/search?q=flipper-plugin)에서 확인하십시오.

더많은 정보는 [Fipper 문서](https://fbflipper.com/docs/features/react-native.html)를 참조하세요.

## 새로운 다크모드 기능들

선호하는 색 구성표 (밝거나 어두운)와 같은 사용자의 모양 기본 설정에 액세스 할 수 있도록 새로운 `Appearance` 모듈을 추가했습니다.

```
const colorScheme = Appearance.getColorScheme();
if (colorScheme === 'dark') {
  // Use dark color scheme
}
```

또한 사용자 환경 설정에 대한 상태 업데이트를 구독하기 위한 hook를 추가했습니다.

```
import {Text, useColorScheme} from 'react-native';

const MyComponent = () => {
  const colorScheme = useColorScheme();
  return <Text>useColorScheme(): {colorScheme}</Text>;
};
```

자세한 내용은 [Appearance](https://reactnative.dev/docs/appearance)와 [useColorScheme](https://reactnative.dev/docs/usecolorscheme) 문서를 참조 해 주세요.

## react-native-tvos로 Apple TV를 옮깁니다.

[Lean Core effort](https://reactnative.dev/blog/#lean-core)에서 언급한 바와 같이 Apple TV를 React Native Windows 및 React Native macOS와 같은 다른 플랫폼에 맞추기 위해 Apple TV 전용 코드를 코어에서 제거하기 시작했습니다.

앞으로 React Native에 대한 Apple TV 지원은 `react-native-tvos` NPM 패키지와 함께 [react-native-community/react-native-tvos](https://github.com/react-native-community/react-native-tvos)에서 유지될 것입니다. Apple TV를 지원하는 데 필요한 변경 사항만 가지고 있는 코어에서의 repository fork입니다.

`react-native-tvos`의 릴리즈는 React Native의 공개 릴리스를 기반으로 합니다. `react-native`의 0.62 릴리스의 경우 `react-native-tvos` 0.62를 사용하도록 Apple TV 프로젝트를 업그레이드하십시오.

## 더 나아진 버전 업그레이드 지원

0.61이 출시되자 community에서는 개발자들이 새로운 버전의 React Native로 업그레이드하는 것을 지원하기 위해 새로운 [upgrade helper](https://react-native-community.github.io/upgrade-helper/) tool을 도입했습니다. upgrade helper는 업그레이드 할 버전과 현재 여러분이 쓰고 있는 버전과의 다른 차이를 제공하여 특정 upgrade에 필요한 변경사항을 볼 수 있습니다.

이 툴을 사용하더라도 업그레이드 시 문제가 발생합니다. 오늘은 [Upgrade-Support](https://github.com/react-native-community/upgrade-support)를 발표하여 보다 전문적이 된 Upgrade-Support를 소개합니다. Upgrade-Support는 GitHub Issue tracker에서, 사용자가 프로젝트 업그레이드와 관련된 문제를 게시하여 커뮤니티의 도움을 받을 수 있습니다.

우리는 항상 업그레이드 환경을 개선하기 위해 노력하고 있으며, 이러한 tool이 사용자가 아직 다루지 못한 특이 케이스에 필요한 지원을 제공하기를 바랍니다.

## 다른 개선점들

- **LogBox:** 새로운 LogBox 오류 및 경고 환경을 opt-in으로 추가하고 있습니다. 이를 사용하려면 index.js 파일에 `require('react-native').unstable_enableLogBox()`를 추가하십시오.
- **React DevTools v4:** 이번 버전은 상당한 성능 향상, 개선된 탐색 경험 및 React Hooks에 대한 완전한 지원을 제공하는 최신 [React DevTools](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)로 업그레이드하는 것이 포함됩니다.
- **접근성 향상:** [accessibilityValue](https://reactnative.dev/docs/accessibility#accessibilityvalue-ios-androidhttps://reactnative.dev/docs/accessibility#accessibilityvalue-ios-android) 추가, [Touchables](https://github.com/facebook/react-native/commit/8c0c860e38f57e18296f689e47dfb4a54088c260)에 빠진 props들, `onSlidingComplete` [accessibility events](https://github.com/facebook/react-native/commit/c7aa6dc8270c0eabc913fe6c617c8131e3f4b3c5), 스위치 컴포넌트의 기본 역할을 `"button"`에서 `"switch"`로 변경하는 등 접근성을 개선했습니다.

## 주요 변경 사항들

- **PropTypes 제거:** React Native 코어가 미치는 앱 크기에 대한 영향을 줄이고 런타임 대신 컴파일 타임에 확인하는 것이 더 낫기 때문에 코어 컴포넌트에서 `propTypes` 를 제거했습니다.
- **accessibilityStates 제거:** 컴포넌트가 상태에 대한 정보를 접근성 서비스에 설명 할 수있는 보다 의미있는 방법 인 새로운 `accessibilityState` 소품을 위해 사용되지 않는 `accessibilityStates` 속성을 [제거했습니다.](https://github.com/facebook/react-native/commit/7b35f427fd66cb0f36921b992095fe5b3c14d8b9)
- **TextInput 변화**: 일반적이지 않고 W3C와 호환되지 않으며 [Fabric](https://github.com/react-native-community/discussions-and-proposals/issues/4)에서 구현하기가 어렵기 때문에 [TextInput](https://github.com/facebook/react-native/commit/3f7e0a2c9601fc186f25bfd794cd0008ac3983ab)에서 `onTextInput`을 제거했습니다. 또한 문서화되지 않은 `inputView` prop 및 `selectionState`를 제거했습니다.

## 향후 사용되지 않을 것들

- `AccessibilityInfo.fetch` 는 이미 사용되지 않습니다.하지만 우리는 이번 업데이트에서 경고창이 뜨도록 했습니다.
- 향후 기본값 전환을 지원하려면 `useNativeDriver` [설정이 필요합니다.](https://github.com/facebook/react-native/commit/5876052615f4858ed5fc32fa3da9b64695974238)
- `Animated` 컴포넌트의 `ref`는 이제 내부 컴포넌트이며 더 이상 [사용되지 않는](https://github.com/facebook/react-native/commit/66e72bb4e00aafbcb9f450ed5db261d98f99f82a) `getNode`입니다.

## 감사

0.62를 가능하게 한 수백 명의 contributors에게 감사합니다! 모든 업데이트를 보려면 [0.62 변경 로그](https://github.com/react-native-community/releases/blob/master/CHANGELOG.md#0620)를 살펴보십시오.
