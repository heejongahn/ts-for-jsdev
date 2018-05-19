---
description: 나머지 연산자와 전개 연산자는 여러 값을 보다 간결하게 할당하는 것을 돕는다.
---

# 2.2.2 나머지 연산자와 전개 연산자

## **나머지 연산자 \(rest operator\)**

비구조화 할당을 사용하되, **배열의 일부 부분 배열을 다른 변수에 할당하고자 할 때** 나머지 연산자를 이용할 수 있다.

```javascript
const [a, ...restArr] = [1, 2, 3, 4];
console.log(restArr); // [2, 3, 4]
```

나머지 연산자는 함수 인자에서도 사용할 수 있다.

```javascript
function normalFunc(p1, p2, ...rest) {
  console.log(rest);
}
normalFunc(1, 2, 3, 4); // [3, 4]
```

## **전개 연산자 \(spread operator\)**

**여러 개의 변수가 들어갈 자리에 한 배열의 원소들을 분포시키고자 할 때**에 전개 연산자를 사용할 수 있다. 나머지 연산자가 여러 원소를 하나의 배열로 묶어주는 역할을 한다면, 전개 연산자는 하나의 배열을 여러 원소들로 풀어주는 역할을 한다. 둘은 일종의 함수–역함수 관계에 있고, 이런 맥락에서 같은 기호\(`...`\)의 사용을 이해할 수 있다.

```javascript
function logThings(a, b, c) {
  console.log(a);
  console.log(b);
  console.log(c);
}
const arr = [1, 2, 3];
logThings(...[1, 2, 3]);
```

배열뿐만이 아닌 객체에 대해서도 비슷하게 나머지/전개 연산자를 추가하는 [`proposal-object-rest-spread` 프러포절](https://github.com/tc39/proposal-object-rest-spread)이 진행중이며, 2017년 12월 기준으 3단계에 있다.

```javascript
const { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x; // 1 
y; // 2 
z; // { a: 3, b: 4 }
const n = { x, y, ...z };
n; // { x: 1, y: 2, a: 3, b: 4 }
```

