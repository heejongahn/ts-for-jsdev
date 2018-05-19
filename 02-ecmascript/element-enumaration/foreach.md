# 2.5.1 forEach 메소드

ES5부터 `Array.prototype.forEach` 메소드가 추가되었다. 이 메소드는 배열의 원소를 인자로 받는 콜백 함수를 인자로 받아, 배열 내의 각 원소에 대해 해당 콜백을 실행한다.

```javascript
langs.forEach(lang => {
  console.log(lang);
}
```

`forEach` 메소드는 간결하지만, 반복문 중간에 `break` 혹은 `return` 등을 사용해 **실행 흐름을 제어할 수 없다**는 단점을 갖고 있다.

