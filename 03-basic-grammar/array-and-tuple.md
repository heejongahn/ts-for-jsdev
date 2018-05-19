---
description: >-
  순서가 있는 원소의 모음(collection)을 나타내는 가장 간단하면서도 유용한 자료구조인 배열, 그리고 그 사촌 튜플을 나타내는 타입에
  대해 다룬다.
---

# 3.2 배열과 튜플

### **배열**

배열 타입은 자바스크립트 `Array` 값의 타입을 나타내는데 쓰인다. 원소 타입 뒤에 대괄호\(`[]`\)를 붙여 표현한다.

```typescript
const pibonacci: number[] = [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55];
const myFavoriteBeers: string[] = ['Imperial Stout', 'India Pale Ale', 'Weizenbock'];
```

배열 타입을 표현하는 또다른 방식이 있다.

```typescript
const pibonacci: Array<number> = [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55];
const myFavoriteBeers: Array<string> = ['Imperial Stout', 'India Pale Ale', 'Weizenbock'];
```

이 문법의 의미는 3.6절에서 **제너릭**을 소개하며 좀 더 자세히 살펴본다.

### **튜플**

튜플 타입을 이용해 원소의 수와 각 원소의 타입이 정확히 지정된 배열의 타입을 정의할 수 있다.

```typescript
const nameAndHeight: [string, number] = ['안희종', 176]
```

**튜플 타입 변수는 정확히 명시된 개수 만큼의 원소만을 가질 수 있다**. 만약 타입 정의보다 더 많은, 혹은 더 적은 원소를 갖는 배열을 할당한다면 에러를 낸다.

```typescript
const invalidNameAndHeight: [string, number] = ['안희종', 176, 42];
// error TS2322: Type '[string, number, boolean]' is not assignable to type '[string, number]'.
//   Types of property 'length' are incompatible.
//     Type '3' is not assignable to type '2'.
```

다만 튜플 타입의 값을 `Array` 프로토타입의 메소드를 통해 조작하는 것은 금지되지 않는다는 점에 유의해야 한다. 예를 들어 아래와 같은 코드는 에러를 내지 않는다.

```typescript
const validNameAndHeight: [string, number] = ['안희종', 176];
validNameAndHeight.push(42); // no error
```

{% hint style="info" %}
타입스크립트 2.6 이하 버전에서는 튜플 타입은 정확한 원소 개수를 보장하지 않는다. 예를 들어 다음과 같은 코드가 허용되었다.

```typescript
const nameAndHeight: [string, number] = ['안희종', 176, true];
```

이러한 동작 방식은 대부분의 실사용례에서 도움이 되지 않는다고 판단되어 2.7 버전부터는 현재와 같이 동작하도록 변경되었다.
{% endhint %}

