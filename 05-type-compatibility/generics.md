---
description: 타입스크립트가 제너릭 타입 간의 호환성을 판단하는 법에 대해 다룬다.
---

# 5.5 제너릭의 호환성

제너릭의 호환성은 기본적으로 객체의 호환성과 비슷하게 동작한다. 이 때 크게 두 가지 경우의 수가 있는데, 모든 타입 변수가 어떤 타입인지 알려진 경우와 그렇지 않은 경우이다. 공식 예제와 함께 살펴보자.

### **모든 타입 변수가 어떤 타입인지 알려진 경우**

```typescript
interface NotEmpty<T> {
  data: T;
}

let x: NotEmpty<number>;
let y: NotEmpty<string>;
```

위의 예제에서 x와 y는 각각 `NotEmpty<number>`와 `NotEmpty<string>` 타입을 가진다. 하지만 이 경우, 제너릭 인터페이스의 정의를 잘 보면

* `NotEmpty<number>` 는 `{ data: number }` 로
* `NotEmpty<string>`은 `{ data: string }` 으로 

고쳐 씀으로써 타입 변수를 완전히 제거할 수 있다. 따라서 `NotEmpty<number>`가 `NotEmpty<string>`에 할당 가능한지 여부를 판단하는 일은 객체 타입간의 할당 여부를 판단하는 일과 다를 바 없다. 

이 경우, `number`는 `string`에 할당 불가능하므로 아래와 같은 할당을 시도한다면 타입 에러가 날 것이다.

```typescript
y = x; // 타입 에
```

### **어떤 타입인지 알려지지 않은 타입 변수가 있는 경우**

제너릭 타입의 호환성을 판단하는 시점에 타입 변수가 알려져 있지 않은 경우도 존재한다.

```typescript
const identity = function<T>(x: T): T {
  // ...
};

const reverse = function<U>(y: U): U {
  // ...
};
```

이 때, `identity`와 `reverse` 함수의 타입에는 타입 변수가 남아 있다. 이럴 때에는 **아직 남아 있는 타입 변수를 모두 `any` 타입으로 대체하고 호환성을 판단**한다. 예를 들어 아래와 같은 할당은 허용된다. 

```typescript
identity = reverse;
```

타입 변수 `T`를 `any`로 대체한 `(x: any) => any` 와 타입 변수 `U`를 `any`로 대체한 `(y: any) => any` 는 서로 할당 가능한 타입이기 때문이다.

