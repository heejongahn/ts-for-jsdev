# 2.5.2 for-of 문법

ES6에는 어떤 컬렉션\(collection\)의 각 요소를 순회하며 특정 작업을 반복 수행하기 위한 `for-of` 문법이 추가되었다.

```javascript
const languages = ['JavaScript', 'TypeScript', 'Python'];
for (const lang of languages) {
  if (!lang.includes('Script')) {
    break;
  }
  console.log(lang);
}

// JavaScript
// TypeScript
```

비슷하게 생겼지만, `for-of` 문법은 기존의 `for-in` 문법과 비교했을 때 아래와 같은 차이점을 갖고 있다.

* 순회 순서가 항상 같을 것이 보장된다.
* `for (const elem in obj) { ... }` 에서 `elem`은 항상 문자열인 반면, `for (const elem in arr) { ... }` 의 elem은 임의의 타입일 수 있다.

또한 `forEach`와 달리 `break` / `continue` 등의 키워드로 실행 흐름을 제어할 수 있다.

주의할 점은 임의의 `Object` 인스턴스에 대해 `for-of`를 사용 가능한 것은 아니라는 것이다.

```javascript
const obj = { a: 1 };
for (const elem of obj) { 
  console.log(elem);
}
// TypeError: obj is not iterable
```

에러 메시지를 보면 짐작할 수 있듯이, `for-of` 문법은 이터러블\(`iterable`\) 프로토콜을 구현한 \(즉, 순회 가능한\) 객체에 대해서만 사용할 수 있다. 이터러블 프로토콜을 구현한 객체란 이터레이터를 원소로 갖는다. 이 용어들이 어떤 의미를 갖는지 차례로 살펴보자.

