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

`let`으로 선언한 변수는 호이스팅 되지 않는다. 따라서 블록 내의 선언문 이전에 해당 변수를 참조한다면 `ReferenceError`가 발생한다.

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

