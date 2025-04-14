# Module1

## About React 19

- 리액트 19에서 변경된 점

1. 개선 및 조정 사항
2. 서버 컴포넌트의 안정화
3. Actions의 도입

## Hello React

### 1.Import Dependencies

```js
import React from 'react';
import { createRoot } from 'react-dom/client';
```

- 두 개의 개별 패키지를 가지고 있는 이유는 리액트가 플랫폼 독립적이기 때문
- 리액트 코어 패키지가 있고, 플랫폼별 특화된 렌더러가 있다.
  - react-dom : web
  - react-native : mobile / desktop application
  - react-three-fiber : 3D scenes WebGL, Three.js
- 모든 플랫폼이 사용하는 코어인 리액트 프레임워크가 react 패키지에 있음

### 2.Create a React Element

```js
const element = React.createElement('p', { id: 'hello' }, 'Hello World!');
```

- element type, element가 가지는 property, element의 컨텐츠
- React element 를 리턴하는 함수
- React element는 js object

### 3.Render the application

```js
const container = document.querySelector('#root');
const root = createRoot(container);
root.render(element);
```

```html
<!-- index.html -->
<div id="root"></div>
```

- createRoot 메서드를 사용하여 어플리케이션의 시작점을 지정
- render 메서드는 react element들의 요소를 DOM 구조로 변경
- DOM element가 생성되면, root에 추가,

### Build Your Own React

- 직접 리액트의 렌더링 함수 만들어보기 예제

## Understanding JSX

- JSX를 사용하는 이유는 트리 구조를 더 보기 쉽게 함이다
- JSX 파일에서 React를 import 하였으나 사용하지는 않는데 왜 그런지?
  - JSX 가 JS로 컴파일 되는 과정에서 사용됨, 하지만 초보자들에겐 진입장벽이었음
  - React 17버전부터는 자동적으로 import 되도록 변경

### Expression Slots

- `{}`로 표현식이나 속성추가가 가능
- `{}`에 있는 값들은 HTML에서는 값들이 문자열이여야 하므로 리액트가 자동으로 타입을 변환

### Differences from HTML

- JSX와 HTML 은 겉보기엔 비슷하지만 기능적으로 다른 부분이 존재

### JSX와 Template 언어와의 비교

- JSX는 얇은 추상화 계층, 템플릿 언어처럼 특이한 문법을 익히거나 하지 않아도 Javascript의 문법만 알고 있으면 사용 가능하다

## Components

- 유저 인터페이스의 특정 부분에 대한 마크업, 디자인, 로직 등의 동작의 묶음
