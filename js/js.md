- temporal dead zone
  - let, const이 호이스팅 된 후 선언부 사이에서 사용하려면 생기는 에러
  - const, let 도 var처럼 호이스팅되지만, var는 undefined로 선언되어 접근이 가능한 반면에 let과 const는 referenced error를 일으켜 접근이 불가능하다.

```javascript
console.log(foo);
const foo = 1;
```

`Uncaught ReferenceError: foo is not defined`
