---
description: 제너릭을 이용해 여러 타입에 대해 동일한 규칙을 갖고 동작하는 타입을 손쉽고 우아하게 정의할 수 있다.
---

# 3.6 제너릭

### **동기부여**

다음의 자바스크립트 함수를 생각해보자. 인자로 넘겨지는 배열은 항상 같은 타입의 값으로 이루어져 있다고 가정한다.

```typescript
function getFirstElem(arr) {
  if (!Array.isArray(arr)) {
    throw new Error('getFirstElemOrNull: Argument is not array!');
  }
  if (arr.length === 0) {
    throw new Error('getFirstElemOrNull: Argument is an empty array!');
  } 
  return arr[0] ? arr[0] : null;
}
```

배열을 받아 첫 번째 원소가 있을 시 그 원소를 리턴하는 매우 간단한 함수다. 문자열의 배열을 인자로 호출하면 문자열 타입의 값을, 숫자의 배열을 인자로 호출하면 숫자 타입의 값을 반환할 것이다. 이 함수의 타입을 어떻게 정의할 수 있을까?

만약 `getFirstElem`이 문자열과 숫자 두 타입만을 지원한다면 함수 오버로딩을 이용해 다음과 같이 쓸 수 있다.

```typescript
function getFirstElem(arr: string[]): string;
function getFirstElem(arr: number[]): number;
function getFirstElem(arr) {
  if (!Array.isArray(arr)) {
    throw new Error('getFirstElemOrNull: Argument is not array!');
  }
  if (arr.length === 0) {
    throw new Error('getFirstElemOrNull: Argument is an empty array!');
  } 
  return arr[0] ? arr[0] : null;
}
```

하지만 이 함수가 **임의의 타입 값을 원소로 갖는 배열**에 대해 동작하도록 만들려면 어떻게 해야 할까? 존재할 수 있는 모든 타입에 대해 오버로딩을 적는 건 \(가능한 타입의 수가 무한하므로\) 불가능하다. 인자와 반환 타입을 any로 정의한다면 동작은 하겠지만, 타입 정보를 모두 잃게 되므로 좋은 방법이 아니다. 

우리가 원하는 기능은 다음과 같다. **여러 타입에 대해 동작하는 함수를 정의하되, 해당 함수를 정의할 때는 알 수 없고 사용할 때에만 알 수 있는 타입 정보를 사용하고 싶다.** 제너릭은 바로 그러한 기능을 제공한다.

### **타입 변수**

함수를 호출하는 시점이 되어야만 알 수 있는 값을 함수 내부에서 사용하기 위해서는 그 값을 담아둘 매개변수가 필요하다. 마찬가지로, **요소를 사용하는 시점에서만 알 수 있는 타입을 담아두기 위해서는 타입 변수\(type variable\)가 필요하다**. 타입 변수와 타입의 관계는 매개변수와 인자 값의 관계와 비슷하다.

|  | 함수 | 제너릭 |
| --- | --- | --- | --- | --- |
| 정의 시점 | 매개변수 `a: number` | 타입 변수 `<T>` |
| 정의 예시 | `function (a: number) { ... }` | `type MyArray<T> = T[]` |
| 사용 시 | 실제 값 \(`3`, `42` 등\) 사용 | 실제 타입\(`number`, `string` 등\) 사용 |
| 사용 예시 | `a(3); a(42);` | `type MyNumberArray = MyArray<number>` |

타입 변수는 부등호\(`<`,`>`\)로 변수명을 감싸 정의한다. 이렇게 정의한 타입 변수를 요소의 타입 정의 \(예를 들어 함수의 인자 및 반환 타입\)에 사용할 수 있다. 부등호 기호 안에 정의된 타입 변수의 실제 타입은 실제 값이 넘어오는 시점에 결정된다.

컨벤션 상 타입스크립트의 타입 변수는 대문자로 시작하며 PascalCase 명명법을 사용한다.

### **제너릭 함수**

타입 변수를 이용해 위의 `getFirstElem` 함수를 다음과 같이 제너릭 함수로 정의할 수 있다.

```typescript
function getFirstElem<T>(arr: T[]): T {
  /* 함수 본문 */
}
```

위의 타입 정의는 다음과 같이 읽을 수 있다.

> **임의의 타입** `T`에 대해, `getFirstElem`은 `T` 타입 값의 배열 `arr`를 인자로 받아 `T` 타입 값을 반환하는 함수다.

보다 일반적으로는 다음과 같은 꼴이 된다. 이 때 인자 타입과 반환 타입을 표현할 때 타입 변수를 사용할 수 있다.

```typescript
function 함수명<타입 변수>(인자 타입): 반환 타입 {
  /* 함수 본문 */
}
```

함수를 호출 할 때에는 정의에서 매개변수가 있던 자리에 인자를 넣어준다. 마찬가지로, 제너릭 함수를 호출할 때에는 정의에서 타입 변수가 있던 자리에 타입 인자를 넣어준다.

```typescript
const languages: string[] = ['TypeScript', 'JavaScript'];
const language = getFirstElem<string>(languages); // 이 때 language의 타입은 문자열
```

### **제너릭 타입 별칭**

타입 별칭 정의에도 제너릭을 사용할 수 있다. 이 때 타입 변수 정의는 별칭 이름 다음에 붙여 쓴다.

```typescript
type MyArray<T> = T[];
const drinks: MyArray<string> = ['Coffee', 'Milk', 'Beer'];
```

### **제너릭의 사용처**

타입 변수와 제너릭의 핵심은 **여러 타입에 대해 동작하는 요소를 정의하되, 해당 요소를 사용할 때가 되어야 알 수 있는 타입 정보를 정의에 사용하는 것**이다. 이러한 개념이 적용되는 범위는 함수와 타입 별칭에 국한되지 않는다. 제너릭을 이용해 추후 다룰 인터페이스, 클래스 등 다양한 타입의 표현력을 높힐 수 있다. 4장에서 추후 해당 주제를 다룰 때에 좀 더 자세히 다룬다.

