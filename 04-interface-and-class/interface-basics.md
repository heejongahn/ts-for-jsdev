---
description: 인터페이스의 기초적인 용례를 알아본다.
---

# 4.1 인터페이스 기초

`interface` 키워드를 사용해 값이 특정한 형태\(shape\)를 갖도록 제약할 수 있다. 인터페이스를 정의하는 기본적인 문법은 객체 타입의 그것과 유사하다.

```typescript
interface User {
  name: string;
  height: number;
}
```

또한 객체 타입에서와 비슷하게 인터페이스의 속성을 **읽기 전용 속성** 또는 **선택 속성**으로 정의할 수 있다.

```typescript
interface User {
  name: string;
  readonly height: number;
  favoriteLanguage?: string;
}
const author: User = { name: '안희종', height: 176 }; // ok
author.height = 183; // error TS2540: Cannot assign to 'height' because it is a constant or a read-only property.
```

### **함수 인터페이스**

인터페이스를 이용해 함수 타입을 표현할 수 있다. 함수 타입의 표현을 위해선 호출 시그니쳐\(call signature\)를 제공해야 하는데, 함수 타입 정의와 유사한 아래 문법을 사용한다.

```typescript
(매개변수1 이름: 매개변수1 타입, 매개변수2 이름: 매개변수2 타입, ...): 반환 타입
```

예를 들어, `User` 타입의 값 `user`를 받아 이름을 반환하는 함수 인터페이스를 다음과 같이 적을 수 있다.

```typescript
interface GetUserName {
  (user: User): string;
}
const getUserName: GetUserName = function (user) {
  return user.name;
};
```

이 때 실제 함수 정의와 인터페이스에서의 매개변수 이름은 꼭 같을 필요는 없다. 즉 위 코드에서 아래처럼 매개변수명을 `user`가 아닌 `u`로 바꾸어 써도 매개변수의 타입 순서만 맞는다면 에러는 발생하지 않는다.

```typescript
const getUserName: GetUserName = function (u) {
  return u.name;
};
```

### **하이브리드 타입**

자바스크립트에서는 jQuery의 `$`과 같이 호출 가능한\(callable\) 동시에 추가적으로 여러 속성을 갖는 객체가 존재할 수 있다. 이런 객체의 타입을 표현하기 위해서 호출 시그니쳐와 속성 타입 정의를 동시에 적을 수 있다. 타입스크립트 공식 문서의 `Counter` 예제를 살펴보자.

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}
let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

위의 `Counter` 타입의 값은 함수로서 호출 할 수 있고, 따라서 호출 시그니쳐를 갖는다. 한편, 이 인터페이스는 추가적으로 `interval`과 `reset` 이라는 속성을 가진다. 따라서 인터페이스는 해당 속성의 타입 정보 또한 포함한다.

이렇게 호출 시그니쳐와 속성 타입을 동시에 갖는 인터페이스를 하이브리드 타입\(hybrid type\)이라 부른다.

### **제너릭 인터페이스**

인터페이스 이름 뒤에 타입 변수 정의를 붙여 제너릭 인터페이스\(generic interface\)를 정의할 수 있다. 예를 들어, 서버로부터 받은 임의의 응답을 나타내는 `Response` 인터페이스를 아래와 같이 정의할 수 있다.

```typescript
interface MyResponse<Data> {
  data: Data;
  status: number;
  ok: boolean;
  /* ... */
}
inteface User {
  name: string;
  readonly height: number;
  /* ... */
}
const user: MyReponse<User> = await getUserApiCall(userId);
user.name; // 타입 시스템은 user.name이 string임을 알 수 있다.
```

함수 인터페이스의 정의에도 제너릭을 사용 할 수 있다. 이 경우 타입 변수는 매개변수의 앞에 적는다.

```typescript
interface GetData {
  <Data>(response: MyResponse<Data>): Data;
}
```

### **타입 별칭과의 차이**

타입에 새로운 이름을 붙이는 수단이라는 점에서 인터페이스와 앞서 살펴본 타입 별칭은 비슷한 점이 많다. 하지만 두 개념 사이엔 다음과 같은 차이점이 있다.

* 타입 별칭을 이용해서 기본 타입, 배열과 튜플, 유니온 타입 등에 새로운 이름을 붙일 수 있다 \(`type UUID = string`\). 인터페이스로는 해당 타입을 표현하는 것이 불가능하다.
* 타입 별칭은 실제로는 새 타입을 생성하지 않는다. 따라서 `type User = { name: string; }` 타입과 관련된 타입 에러가 발생했을 시 에러 메시지는 `User` 대신 `{ name: string; }` 를 보여준다. 한편 인터페이스는 실제로 새 타입을 생성하고, `interface User { name: string; }` 과 관련된 에러 메시지에는 `User` 가 등장한다.
* 인터페이스는 곧 다룰 `extends` 키워드를 이용해 확장할 수 있는 반면, 타입 별칭의 경우는 그런 수단을 제공하지 않는다.

이런 차이점 때문에 타입스크립트 공식 문서는 가능한 경우 항상 타입 별칭보다 인터페이스를 사용할 것을 권장한다. **기본적으로 인터페이스로 표현할 수 있는 모든 타입은 인터페이스로 표현하고, 기본 타입에 새로운 이름을 붙이고 싶거나 유니온 타입을 명명하고 싶은 경우 등 인터페이스의 능력 밖인 부분에서만 타입 별칭을 사용하라**.

