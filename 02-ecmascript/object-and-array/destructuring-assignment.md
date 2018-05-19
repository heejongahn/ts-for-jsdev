# 2.2.1 비구조화 할당

### **배열의 비구조화 할당**

먼저 배열을 보자. 한 배열의 각 원소에 새로운 이름을 붙여야 하는 경우, 기존에는 아래와 같이 코드를 반복해서 적어야 했다.

```javascript
const arr = [1, 2];
const a = arr[0];
const b = arr[1];
```

비구조화 할당을 지원하는 ES6부터는 위 코드를 아래와 같이 간결하게 쓸 수 있다.

```javascript
const arr = [1, 2];
const [a, b] = [1, 2];
console.log(a); // 1
console.log(b); // 2
```

만약 좌항 배열이 우항 배열보다 더 큰 `length`를 갖고 있다면, 대응하는 우항 배열의 원소가 없는 좌항 배열의 원소에는 `undefined`가 할당된다.

```javascript
const [c, d] = [1];
console.log(d); // undefined
```

### **객체의 비구조화 할당**

비슷한 문법을 객체에도 적용가능하다.

```javascript
const obj = { a: 1, b: 2 };
const a = obj.a;
const b = obj.b;
```

비구조화 할당을 이용해 위의 코드를 아래와 같이 적을 수 있다.

```javascript
const obj = { a: 1, b: 2 };
const { a, b } = obj;
console.log(a); // 1
console.log(b); // 2
```

마찬가지로 없는 속성를 참조 할 시 `undefined`가 할당되며, 기본값을 넣어줄 수도 있다.

```javascript
const { c, d, e = 3 } = { c: 1 };
console.log(d); // undefined
console.log(e); // 3
```

또한 객체의 경우, 우항과 다른 변수명을 사용하고 싶은 경우 아래와 같이 콜론\(`:`\)을 사용해 변수에 새로운 이름을 줄 수 있다.

```javascript
const { a: newA } = { a: 1 };
console.log(newA); // 1
```

비구조화 할당은 함수 인자에서도 사용 가능하다.

```javascript
function useless({ a, b }) {
  console.log(a, b);
}
useless({ a:1, b: 2}); // 1 2
```

