---
description: 타입스크립트가 기본 타입 간의 호환성을 판단하는 법에 대해 다룬다.
---

# 5.1 기본 타입의 호환성

타입 간의 할당 가능성 판단은 결국 다음 질문에 답하는 것과 같다.

> 두 가지 다른 타입 `A`와 `B`에 대해, 모든 `A` 타입의 값을 `B` 타입의 값으로도 취급할 수 있는가?

다음 코드를 보자.

```typescript
type OneDigitOdd = 1 | 3 | 5 | 7 | 9;
const three: OneDigitOdd = 3;
const num: number = three;
```

위의 코드는 `number` 타입의 값에 `OneDigitOdd` 타입의 값을 할당한다. 이 때, `OneDigitOdd` 타입이 가질 수 있는 값인 `1`, `3`, `5`, `7`, `9` 는 모두 `number` 에 속한다. `OneDigitOdd` 타입의 모든 값이 `number` 타입의 값이기도 하므로 위의 코드는 오류 없이 실행 된다. 즉, `OneDigitOdd` 타입은 `number` 타입에 **할당 가능**\(assignable\)하다. 

반면 아래의 코드를 보자.

```typescript
const four: number = 4;
const oneDigitOdd: OneDigitOdd = four; 
// error TS2322: Type 'number' is not assignable to type 'OneDigitOdd'.
```

`number` 타입은 `1`, `3`, `5`, `7`, `9` 이외의 다른 값 또한 가질 수 있다. 예를 들어, `const four: number = 4`는 `OneDigitOdd` 타입의 값으로 허용되지 않는다. `number` 타입의 값이지만 `OneDigitOdd` 값으로 취급할 수 없는 값이 존재하므로, `number` 타입은 `OneDigitOdd` 타입에 **할당 불가능**하다.

위에서 보았듯, 기본적인 타입 간의 할당 가능 여부를 판단하는 건 간단하다. 그렇다면 보다 복잡한 타입 사이의 호환성은 어떻게 판단할 수 있을지 살펴보자.

