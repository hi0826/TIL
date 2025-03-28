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
