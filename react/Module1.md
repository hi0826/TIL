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
- 재사용의 핵심 장치

### Basic Syntax

- 보통의 function 선언과 화살표 함수 모두 사용해도 상관없음
- HTML 태그와 컴포넌트는 공존할 수 없으므로, 컴포넌트의 이름은 파스칼 케이스의 컨벤션을 차용한다

### Children Prop

- 컴포넌트의 내부의 내용은 자동으로 children prop을 통해 전달
- children 속성을 사용해서 지정도 가능
  - 하지만, 두가지 모두 사용했을 경우 컴포넌트 내부의 내용이 더 우선순위로 전달

## Application Structure

- index.js 파일에서는 글로벌 css 설정이나 로깅 파일을 가져오는 것이 좋음

## Fragments

- 컴포넌트에서 여러 HTML 태그를 동시에 리턴하는 것은 불가능
- 그렇다고 div로 감싸기에는 다른 레이아웃을 깨버림
- `<React.Fragment>`를 사용하자 `<>`단축어로 이렇게 사용

## Iteration

### keys

- 배열과 같은 엘리먼트들을 표현할 때, React가 식별할 수 있도록 고유 식별자를 줘야한다
  - 배열의 요소가 변화하거나 할 때 React가 어떤 요소를 변경할지 식별하는 역할
- React element 는 key를 최상위 필드로 가지고 있음
- key 는 props 로 보이지만, react element를 확인하는 특별한 것
- key 를 사용할 때는 top-level element에 사용할 것
- 전역적으로 unique로 관리되지 않고, 포함된 배열에서만 unique로 관리됨

#### exercise

```jsx
import Avatar from './Avatar';

const avatarArr = [
  {
    src: 'https://sandpack-bundler.vercel.app/img/avatars/001.png',
    alt: 'person with curly hair and a black T-shirt',
  },
  {
    src: 'https://sandpack-bundler.vercel.app/img/avatars/002.png',
    alt: 'person wearing a hijab and glasses',
  },
  {
    src: 'https://sandpack-bundler.vercel.app/img/avatars/003.png',
    alt: 'person with short hair wearing a blue hoodie',
  },
  {
    src: 'https://sandpack-bundler.vercel.app/img/avatars/004.png',
    alt: 'person with a pink mohawk and a raised eyebrow',
  },
];

function App() {
  return (
    <div className="avatar-set">
      {avatarArr.map(({ src, alt }, id) => (
        <Avatar key={id} src={src} alt={alt} />
      ))}
    </div>
  );
}

export default App;
```

## Conditional Rendering

- react attribute에는 null like 한 값들은 DOM 노드에 설정되지 않음
- react component를 만들 때, {} 사이에 표현식은 사용 가능하지만, 문장은 사용할 수 없다 `if() {...}`
- &&(제어 흐름 연산)로 if 로직을 대신해서 사용 가능
- 리액트는 0이나 ''와 같은 falsy 값을 무시 하므로 항상 boolean 타입과 사용하자
- 삼항연산자와도 사용 가능

## RangeUtility

- 배열이 없는 경우에 range 메서드를 사용하자
