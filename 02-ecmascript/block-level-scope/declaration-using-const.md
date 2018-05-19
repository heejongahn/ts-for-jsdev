# 2.2.2 const를 이용한 선언

`const`를 이용해 **재할당이 불가능한 블록 레벨 변수**를 선언할 수 있다. `const`를 이용해 선언한 변수의 값을 블록 내에서 재할당하려 하면 에러가 발생한다.

```javascript
function notOk() {
  const a = 1;
  a = 2;
}
notOk(); // TypeError: Assignment to constant variable.
```

`let`과 마찬가지로 `const`으로 선언한 변수는 호이스팅 되지 않는다.

```javascript
function foo() {
  console.log(a);
  const a = 3;
}
foo(); // ReferenceError: a is not defined
```

선언 후 재할당이 불가능하단 점에서 짐작할 수 있듯이 `const`를 이용한 선언은 항상 값의 초기화를 수반해야 한다. `var`이나 `let`을 사용할 때처럼 변수를 선언만 해놓은 뒤 그 값을 추후에 초기화하는 것은 불가능하다.

```javascript
function ok() {
  const a = 3;
  console.log(a);
}
function notOk() {
  const a;
  a = 3;
  console.log(a);
}
ok(); // 3
notOk(); // SyntaxError: Missing initializer in const declaration
```

`const`로 선언한 변수는 **재할당이 불가능할 뿐, 불변값이 아니라는 점**을 명심해야 한다. 예를 들어, `Object`나 `Array` 타입의 변수를 `const`로 정의 했더라도 그 객체의 내부 상태를 조작하는 다양한 수단은 모두 아무런 문제 없이 실행할 수 있다.

```javascript
const a = 3;
a = 4; // TypeError: Assignment to constant variable.
const obj = {};
obj.a = 42; // OK
const arr = [];
arr.push(3); // OK
```

`const` 선언으로 막을 수 있는 것은 오로지 블록 내 값의 재할당 뿐이다.

