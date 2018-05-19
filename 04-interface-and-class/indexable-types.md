---
description: 동적인 색인을 표현하는 색인 가능 타입에 대해 다룬다.
---

# 4.2 색인 가능 타입

앞서 다룬 예시들은 모두 코드 작성 시점에 속성 이름이 알려져 있었다. 하지만 **코드의 실행 시점에서만 알 수 있는 이름의 동적 속성을 갖는 타입**은 어떻게 표시해야 할까? 

```typescript
const users:  = [
  { name: '안희종', height: 176, favoriteLanguage: 'TypeScript' },
  { name: '이방인', height: 42 }
];
interface NameHeightMap {
  // ??
}
const nameHeightMap: NameHeightMap = {};
users.map(user => {
  nameHeightMap[user.name] = user.height;
});
console.log(userHeightMap) // { '안희종': 176, 'Stranger': 42 }
```

위 코드의 `nameHeightMap`은 **임의의 유저 목록**을 받아 유저의 이름을 키로, 유저의 신장을 값으로 갖는 매핑이다. 이 때 이 객체의 키들은 임의의 유저 이름이므로 코드를 작성하는 시점에는 모든 가능한 키를 나열하는 것이 불가능하다. 예를 들어 위 타입을 아래와 같이 정의한다고 해 보자.

```typescript
interface NameHeightMap {
  안희종: number;
  이방인: number;
}
```

이 경우 이후 `users` 값에 `{ name: '뉴페이스', height: 777 }` 등의 유저가 추가되는 경우를 제대로 처리하지 못 할 것이다. 또한 실제로는 `users` 와 같은 정보를 실행 시간에 서버로부터 얻어오는 등의 경우가 많은데, 이런 경우 역시 커버할 수 없다. 이럴 때 필요한 것이 바로 **색인 가능 타입**\(indexable type\)이다. 

### **색인 시그니쳐**

색인 가능 타입을 이용해 색인 가능한\(indexable\) 객체의 타입을 정의할 수 있다. 색인 가능 타입을 정의하기 위해서는 색인에 접근할 때 사용하는 기호인 대괄호\(`[]`\)를 이용해 객체의 색인 시그니쳐\(index signature\)를 적어줘야 한다.

예를 들어 위의 `NameHeightMap` 인터페이스는 색인 가능 타입을 사용해 아래와 같이 적을 수 있다.

```typescript
interface NameHeightMap {
  [userName: string]: number | undefined;
}
```

위 정의는 다음과 같이 읽는다.

* `NameHeightMap` 타입의 값을
* 임의의 `string` 타입 값 `userName` 으로 색인한 값 \(`[userName: string]`  → 인덱스 시그니쳐\)
* 즉 `nameHeightMap[userName]`은 `number` 또는 `undefined` 타입의 값이다. \(`: number | undefined`\)

위 예제에선 색인된 값이 `number` 가 아닌 `number | undefined` 타입을 가지는 것에 유의하라. `nameHeightMap`이 모든 문자열을 키로 갖고 있다는 보장이 없으므로 `nameHeightMap['없는 유저']` 따위의 값은 `undefined` 일 수 있기 때문이다.

이 경우 색인된 값을 `number` 타입의 값으로 사용하고 싶다면 먼저 `undefined`인지 여부를 체크해줘야 한다.

```typescript
const h = nameHeightMap['안희종']; // 이 시점에서 h의 타입은 number | undefined
if (h !== undefined) {
  // 이 시점에서 h의 타입은 number
  console.log(h.toString()); // ok
}
```

### **색인과 타입**

색인의 타입으로는 **문자열 또는 숫자**만이 사용 가능하다. 이 때 주의해야 할 점은 만약 문자열 색인과 숫자 색인이 모두 존재하는 경우, **숫자로 색인 된 값의 타입은 문자열로 색인 된 값 타입의 서브타입이어야 한다**는 것이다.

즉, 아래 예제에서 `B`는 `A`의 서브타입이어야 한다.

```typescript
inteface Mixed<A, B> {
  [stringIndex: string]: A;
  [numberIndex: number]: B;
}
```

이 때 “`B`가 `A`의 서브타입이다”는 말의 의미는 “`B` 타입의 모든 값은 `A` 타입에도 속한다” 정도로 이해할 수 있다. 예를 들어, 모든 정수를 나타내는 타입 `Int`와 모든 숫자를 나타내는 타입 `Num`이 존재한다고 하자. 모든 정수는 숫자이므로 \(즉 `Int` 타입의 모든 값을 `Num` 타입의 값으로도 사용할 수 있으므로\) `Int`는 `Num`의 서브타입이다.

이러한 제약이 존재하는 이유는 **자바스크립트 색인의 동작 방식** 때문이다. 자바스크립트 코드에서 객체의 색인에 접근할 때, 내부적으로는 색인의 `toString()` 메소드를 호출해 문자열로 변형된 값을 색인으로 사용한다. 예를 들어 `1.toString() === '1'` 이므로 `obj[1]` 이라고 적은 코드는 실제로는 `obj['1']`와 동일하다.

이 때, 만약 다음 `ErrorProne` 타입과 같이 숫자로 색인 된 값의 타입\(`boolean`\)이 문자열로 색인 된 타입\(`number`\)의 서브타입이 아닌 경우가 허용된다고 가정해보자.

```typescript
interface ErrorProne {
  [str: string]: number;
  [num: number]: boolean;
}
let errorProne: ErrorProne = {
  'abc': 3,
  3: true
};
errorProne[3];
```

가장 아래 줄을 보면, `3`이라는 색인은 숫자 타입이므로 타입 시스템은 `errorProne[3]`의 타입이 `boolean`일 것이라 추측할 것이다. 하지만 위에서 언급한 색인의 동작 방식에 의해 실제로 해당 값은 `errorProne['3']`과 같고, 이는 문자열 색인으로 접근한 `number` 타입의 값이다. 타입 시스템이 알고 있는 정보\(`boolean`\)와 실제 상황\(`number`\) 이 달라지는 것이다.

따라서 타입스크립트는 이런 코드를 작성하는 것을 허용하지 않고, `error TS2413: Numeric index type 'boolean' is not assignable to string index type 'number'.` 와 같은 에러를 발생시킨다. 숫자 색인으로 접근한 타입 `boolean`을 문자열 색인으로 접근한 타입 `number`에 할당할 수 없다는 의미다.

비슷한 이유로, 문자열 색인 시그니처가 존재한다면 그 외 모든 속성의 값 타입은 문자열 색인으로 접근한 값의 타입의 서브타입이여야 한다. 모든 속성 접근은 \(`user.name === user['name']` 이므로\) 결국 문자열 색인 접근의 특수한 케이스이기 때문이다. 아래와 같은 선언은 타입 에러를 발생시킨다.

```typescript
interface User {
    [randomProp: string]: number;
    name: string; 
}
// error TS2411: Property 'name' of type 'string' is not assignable to string index type 'number'.
```

### **읽기 전용 색인**

색인 역시 읽기 전용으로 선언할 수 있다. 객체 타입, 인터페이스에서의 `readonly`의 동작과 마찬가지로 `readonly`로 선언된 색인의 값은 재할당이 불가능하다.

```typescript
interface ReadonlyNameHeightMap {
  readonly [name: string]: height;
}
const m: ReadonlyNameHeightMap = { '안희종': 176 };
m['안희종'] = 177; // error TS2542: Index signature in type 'ReadonlyNameHeightMap' only permits reading.
```

### **색인 가능 타입의 사용예**

색인 가능 타입을 사용하는 가장 간단하면서도 유용한 인터페이스 중 하나로 `Array` 인터페이스를 꼽을 수 있다. 만약 색인 가능 타입이 없이 `T` 타입의 원소를 갖는 `Array` 인터페이스를 작성한다면 대략 아래와 같은 식으로 모든 색인에 대한 타입을 일일이 정의해야 할 것이다.

```typescript
interface Array<T> {
  length: number;
  0?: T;
  1?: T;
  /* ... */
  Number.MAX_SAFE_INTEGER?: T;
  /* 메소드 정의 */
} 
```

인덱스 타입을 이용하면 위 코드를 다음처럼 간결하게 대체할 수 있다.

```typescript
interface Array<T> {
  length: number;
  [index: number]?: T;
  /* 메소드 정의 */
}
```

