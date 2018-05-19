# 2.3.2 화살표 함수

기본적으로 자바스크립트 함수 내부에서 참조된 `this` 키워드의 값은 함수가 정의되는 시점이 아닌 실행되는 시점에 결정된다. 동일한 내부 구조를 가진 함수가 동일한 블록 내에서 실행 되더라도 어떤 방식으로 호출되냐에 따라 함수 내에서의 `this`값은 달라질 수 있다.

```javascript
function getName() {
  console.log(this.name);
}
const a = {
  name: 'javascript',
  getName: getName
};
function getNames() {
  a.getName(); // 'javascript'
  getName(); // TypeError: Cannot read property 'name' of undefined
}
```

이러한 `this`의 동작은 자주 혼란을 야기한다. 

ES6에 추가된 화살표 함수\(arrow function\) 문법을 사용하면 함수 내의 `this`가 실행 시점이 아닌 정의 시점에 결정되는 함수를 정의할 수 있다. 화살표 함수 내에서의 모든 `this` 참조는 해당 함수가 정의되는 시점에서 함수를 둘러싼 문맥의 `this` 값으로 고정된다. 화살표 함수는 `(인자) => (함수 본문)`의 문법으로 정의한다.

```javascript
const obj = {
  a: 1,
  normalFunc: function() { console.log(this); },
  arrowFunc: () => { console.log(this); },
};
const { normalFunc, arrowFunc } = obj;
obj.normalFunc(); // { 
                  //   a: 1,
                  //   normalFunc: [Function: normalFunc],
                  //   arrowFunc: [Function: arrowFunc] 
                  // }
normalFunc(); // undefined
obj.arrowFunc(); // (global object)
arrowFunc(); // (global object)
```

만약 인자가 하나일 경우, 화살표 함수의 인자를 둘러싼 괄호를 생략할 수 있다.

```javascript
const a = args => { console.log(args); };
console.log(a);
```

함수 본문이 한 줄의 표현식으로 이루어졌을 시, 본문을 감싸는 대괄호를 생략할 수 있다. 이 때 해당 표현식의 값이 함수의 반환값이 된다.

```javascript
const isOdd = n => (n % 2 === 1);
console.log(isOdd(3)); // true
```

`this`의 동작 이외에도 화살표 함수는 `function` 키워드를 이용해 선언한 함수와 다음의 차이점을 갖는다.

* 생성자로 사용할 수 없다.
* 함수 내에 `arguments` 바인딩이 존재하지 않는다.
* `prototype` 프로퍼티를 갖고 있지 않는다.



