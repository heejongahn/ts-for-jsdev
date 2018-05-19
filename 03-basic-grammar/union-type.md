# 3.7 유니온 타입

### **동기부여**

아래 함수를 한 번 살펴보자.

```typescript
function square(value: number, returnString: boolean = false): ??? {
  const squared = value * value;
  if (returnString) {
    return squared.toString();
  }
  return squared;
}
```

함수 `square`는 숫자 타입 인자를 하나 받고, 불리언 타입 인자를 하나 더 받아 그 값에 따라 문자열 또는 숫자 타입의 값을 반환한다. 이 함수의 반환 타입은 어떻게 표현할 수 있을까? 일단 이 경우 반환 타입이 **인자의 타입이 아닌 값에 의존한다**. 따라서 제너릭으로는 표현하기 까다롭다 짐작할 수 있다.

오버로딩을 이용하면 아래와 같이 표현은 가능하다. 하지만 하나의 타입을 제외하고 모든 부분이 똑같은데도 여러번 써야 해 비효율적이다. 게다가 오버로딩으로 함수를 정의한다 한들, 반환값을 할당하는 변수의 타입을 정의하기 어렵다는 문제가 남는다.

```typescript
function square(value: number, returnString: boolean): number;
function square(value: number, returnString: boolean): string;
function square(value, returnString = false) {
  /* 본문 동일 */
}
const mystery: ??? = square(randomNumber, randomBoolean);
```

**어떤 타입이 가질 수 있는 경우의 수를 나열**할 때 사용하는 **유니온 타입**으로 이 함수의 반환 타입을 표현할 수 있다. 

### **문법**

유니온 타입은 가능한 모든 타입을 파이프\(`|`\) 기호로 이어서 표현한다. “`A` 또는 `B` 타입일 수 있는 타입”을 `A | B` 로 쓰는 식이다. `square` 함수의 타입은 아래와 같이 적을 수 있다.

```typescript
function square(value: number, returnString: boolean = false): string | number {
  /* 본문 동일 */
}
const stringOrNumber: string | number = square(randomNumber, randomBoolean);
```

타입 별칭 문법을 사용해 유니온 타입에 이름을 붙일 수 있다. 자주 사용되는 타입, 또는 인라인으로 작성하기에 너무 복잡한 타입의 경우 이 방식을 추천한다.

```typescript
type SquaredType = string | number;
function square(value: number, returnOnString: boolean = false): SquaredType {
  /* 본문 동일 */
}
```

유니온 타입이 가질 수 있는 타입의 수가 꼭 2개일 필요는 없다. 몇 개든 이어가며 정의할 수 있다.

```typescript
type Whatever = number | string | boolean;
```

### **여러 줄에 걸친 유니온 타입**

여러 줄에 걸쳐 유니온 타입을 적을 때에는 보통 아래와 같이 정렬을 맞춘다.

```typescript
type Fruits
  = Apple
  | Banana
  | Cherry;
```

추가로 유니온 타입의 맨 앞에도 파이프를 쓰는 것이 허용된다. 이렇게 하면 여러 줄에 걸쳐 유니온 타입을 정의할 때 각 라인의 형태를 통일할 수 있다. 실질적인 의미 차이는 없으니 선호대로 사용하면 된다.

```typescript
type Fruits =
  | Apple
  | Banana
  | Cherry;
```

