---
description: ES6 기본 매개변수 문법을 사용해 매개변수의 기본값을 간결하게 표현할 수 있다.
---

# 2.3.1 기본 매개변수

자바스크립트 함수는 매개변수의 수를 느슨하게 체크한다. 예를 들어, 3개의 매개변수를 기대하는 함수에 1개 혹은 2개만 주어졌을 때, 런타임 에러를 내는 대신 나머지 매개변수로 `undefined` 값이 들어온 것과 동일하게 함수를 실행한다.

```javascript
function looseCheck(a, b, c) {
  console.log([a, b, c]);
}
looseCheck(1, 2, 3); // [1, 2, 3]
looseCheck(1, 2); // [1, 2, undefined)
looseCheck(1); // [1, undefined, undefined)
```

이러한 함수의 특성 상 모든 매개변수가 넘겨졌는지 검사하고 싶거나, 기본값을 채워주고 싶은 경우 해당 처리를 프로그래머가 매번 수동으로 해와야 했다.

```javascript
function tightCheck(a, b, c) {
  if (
    (typeof a === 'undefined') ||
    (typeof b === 'undefined') ||
    (typeof c === 'undefined')
  ) {
    throw new Error('Not all parameters are specified.');
  }
  console.log([a, b, c]);
}
tightCheck(1, 2, 3); // [1, 2, 3]
tightCheck(1, 2); // Error: Not all parameters are specified.
function useDefault(_a, _b, _c) {
  const a = typeof _a === 'undefined' ? 1 : _a;
  const b = typeof _b === 'undefined' ? 1 : _b;
  const c = typeof _c === 'undefined' ? 1 : _c;
  console.log([a, b, c]);
}
useDefault(1, 2, 3); // [1, 2, 3]
useDefault(1, 2); // [1, 2, 1]
```

이러한 수고를 덜기 위해 ES6에는 기본 매개변수 문법이 추가되었다. 이 문법은 위의 `useDefault` 함수와 거의 동일한 일을 하는 문법 설탕이다. 매개변수 뒤에 `= (기본값)`을 덧붙여 해당 매개변수의 기본값을 설정할 수 있다.

```javascript
function useDefaultES6(a = 1, b = 2, c = 3) {
  console.log([a, b, c]);
}
useDefault(1, 2, 3); // [1, 2, 3]
useDefault(1, 2); // [1, 2, 3]
useDefault(1, undefined, 4); // [1, 2, 4]
```

