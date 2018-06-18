---
description: let을 이용해 재할당이 가능한 블록 레벨 변수를 선언할 수 있다.
---

# 2.1.1 let을 이용한 선언

`let`을 이용해 **재할당이 가능한 블록 레벨 변수**를 선언할 수 있다. 아래 예시 코드를 보자.

```javascript
{
  let goOut = true;
  if (true) {
    let goOut = false;
  }
  console.log(goOut); // true
  goOut = false;
  console.log(goOut); // false
}
console.log(goOut); // ReferenceError: goOut is not defined.
```

두 가지 주목할 점이 있다.

1. `let`을 이용한 선언은 블록 수준에서 이루어지므로, 대괄호 블록과 `if` 블록 내에서 `goOut`의 값을 선언하는 행위는 해당 블록 안에서만 의미를 갖는다.
2. `var`을 이용한 선언과 마찬가지로, `let`으로 선언한 변수를 블록 내에서 재할당할 수 있다.

`let`으로 선언한 변수는 엄밀한 의미에서 호이스팅 되지만, `var`로 선언한 변수와 달리 변수의 정의문이 평가되기 전 해당 변수를 참조할 경우 `ReferenceError`가 발생한다. 이 때 변수 접근이 에러를 발생시키는, 정의문이 평가되기 전까지의 구간을 [Temporal Dead Zone](http://jsrocks.org/2015/01/temporal-dead-zone-tdz-demystified)이라 한다.

{% hint style="info" %}
이 부분에 존재했던 오류에 [정성스러운 이슈](https://github.com/heejongahn/ts-for-jsdev/issues/14)를 제기해주신 한재엽님 감사합니다 :\)
{% endhint %}

```javascript
function foo() {
  console.log(a);
  let a = 3;
}

foo(); // ReferenceError: a is not defined
```

또한 `var`과 다르게 어떤 변수명에 대한 `let`을 이용한 선언은 한 블록 내에서 한 번만 가능하다.

```javascript
function ok() {
  var a = 1;
  var a = 2;
  console.log(a);
}
function notOk() {
  let a = 1;
  let a = 2;
  console.log(a);
}
ok(); // 2
notOk(); // SyntaxError: Identifier 'a' has already been declared
```

