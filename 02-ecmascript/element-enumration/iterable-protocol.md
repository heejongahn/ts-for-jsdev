# 2.5.4 이터러블 프로토콜

어떤 객체가 `Symbol.iterator`의 키의 값으로 메소드를 갖고, 해당 메소드를 실행했을 때 이터레이터 인스턴스가 반환될 때 그 객체가 **이터러블 프로토콜을 구현한다**, 또는 **순회 가능하다**고 한다. `Symbol.iterator`는 ES6에 추가된 `Symbol` 타입의 값으로, 그 중에서도 표준에 정의되어 있는 특수한 심볼이다.

예를 들어, 다음과 같이 순회 가능한 객체 `iterableObj`를 정의할 수 있다.

```javascript
function makeIterator(array) {
    var nextIndex = 0;
    return {
       next: function() {
           return nextIndex < array.length
               ? { value: array[nextIndex++], done: false } 
               : { done: true};
       }
    };
}
const iterableObj = {
  [Symbol.iterator]() { return makeIterator([1, 2, 3]); }
};
```

순회 가능한 객체에 대해서는 `for-of` 문법이 사용 가능하다. 이 때 `for-of` 반복문은 내부적으로 이터레이터의 `next()`를 실행해 `done === true`인 경우 반복을 중단한다. `done === false` 인 경우, `for (const elem of iterable)` 문의 변수 `elem`에 `value`를 할당하고, 함수 본문을 실행한다.

```javascript
for (const elem of iterableObj) {
  console.log(elem);
}
// 1
// 2
// 3
```

또한, 순회 가능한 객체에 대해 전개 연산자가 사용 가능하다. 이 때 전개 연산자는 해당 순회 가능한 객체의 이터레이터가 리턴한 `value` 들의 나열로 대체된다.

```javascript
console.log(...iterableObj); // [1, 2, 3]
```

`Array`, `Map`, `Set` 등의 표준 객체는 모두 이터러블 프로토콜을 구현한다. 그 덕분에 프로그래머는 서로 전혀 다른 형태의 여러 객체들을 일관적인 문법을 사용해 순회할 수 있다. **이터러블 프로토콜은 프로그래머에게 임의의 객체에 대해 해당 객체를 어떻게 순회할지를 명시하고, 동일할 문법으로 여러 객체를 순회할 수 있는 수단을 제공한다**.

