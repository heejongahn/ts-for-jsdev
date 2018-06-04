---
description: 타입스크립트가 함수 타입 간의 호환성을 판단하는 법에 대해 다룬다.
---

# 5.3 함수 타입의 호환성

### 매개변수 수가 같은 경우

함수 타입 간의 호환성을 판단하려 할 때, 가장 간단한 경우는 두 함수의 매개변수의 갯수가 같은 경우다. 이 때, 할당을 받는 함수의 타입을 `Target`, 할당하려는 함수의 타입을 `Source`라 하자.

```typescript
let source: Source;
const target: Target = source;
```

위 코드가 허용되는지, 즉 `Target`이 `Source`에 할당 가능한지를 보기 위해선 다음 두 질문에 답해야 한다.

* `Target`과 `Source`의 모든 매개변수 타입에 대해, `Source`의 매개변수 타입이 `Target`의 매개변수 타입에 할당 가능한가?
* `Target`의 반환 타입이 `Source`의 반환 타입에 할당 가능한가?

두 질문에 대한 답이 모두 “예”라면, `Target`은 `Source`에 할당 가능하다. 

{% hint style="info" %}
직관적으로는, 선택 매개변수와 필수 매개변수는 호환성을 판단할 때 다르게 취급되는 게 맞아 보인다. 하지만 타입스크립트에선 매개변수가 선택 매개변수인지 필수 매개변수인지는 함수 타입의 호환성 판단에 아무런 영향을 주지 않는다.
{% endhint %}

#### **할당 가능한 경우**

먼저 할당 가능한 경우를 살펴보자.

```typescript
type Sum = (sumFirst: number, sumSecond: number) => number;
type Multiply = (mulFirst: number, mulSecond: number) => number;
```

* 모든 매개변수 타입은 `number`로, 서로 할당 가능하다.
* `Multiply`의 반환 타입인 `number`는 `Sum`의 반환 타입인 `number`에 할당 가능하다.

따라서 `Sum`은 `Multiply`에 할당 가능하다.

```typescript
const sum: Sum (sumFirst: number, sumSecond: number) => {
  return sumFirst + sumSecond;
};
const multiply: Multiply = sum; // ok
```

#### **할당 불가능한 경우**

다음으로는 할당이 불가능한 예제를 살펴보자.

```typescript
interface Animal { animalProp: string };
interface Dog extends Animal { dogProp: number };

let f = (animal: Animal) => animal.animalProp;
let g = (dog: Dog) => { doSomething(dog.dogProp) };

f = g;
```

* 할당받는 함수의 매개변수 타입 `Animal`은 할당하는 함수의 매개변수 타입 `Dog`에 할당 불가능하다. 

만족되지 않는 기준이 있으므로 `g`는 `f`에 할당할 수 없다. 

{% hint style="info" %}
이 때, 이 기준을 만족해야 하는 이유는 뭘까? 즉, `f`의 매개변수 타입인 `Animal` 이 `g`의 매개변수 타입인 `Dog`에 할당 불가능하면 왜 `g`를 `f`에 할당하지 못해야 할까?

해당 할당을 허용하는 경우를 생각해보자. 만약 `f = g` 의 할당을 허용한다면 다음과 같이 `Animal` 타입의 인자를 넘길 수 있을 것이다.

```typescript
const cat: Animal = { animalProp: 'cute' };
f(cat); // 컴파일러는 통과 시켜 줌
```

하지만 `g` 는 인자가 `Dog` 일 것이라 가정하고, `Dog` 에만 존재하는 속성 `dogProp` 에 접근하고 있다. 따라서 런타임 에러가 발생할 수 있다. 이런 상황을 막기 위해 타입스크립트는 이 경우 `f = g` 와 같은 할당을 금지한다.
{% endhint %}

### **매개변수 수가 다른 경우**

매개변수 수가 같은 두 함수의 호환성을 비교하는 법에 대해 살펴봤다. 이번엔 매개변수의 수가 다른 경우엔 상황이 어떻게 달라지는지를 다음 두 함수 타입을 통해 살펴보자.

```typescript
type Login = (id: string) => Response<Data>;
type LoginWithToken = (id: string, token: string) => Response<Data>;
```

#### **할당하는 함수의 매개변수 수가 더 많은 경우**

아래 코드에서 할당하는 함수인 `loginWithToken`은 할당받는 함수 `login`에 비해 `token: string` 이라는 매개변수를 추가적으로 갖고 있다. 

```typescript
const loginWithToken: LoginWithToken = (id: string, token: string) => { /* ... */ };
const login: Login = loginWithToken;
```

이런 경우는 할당이 **불가능**하다. 만약 이 할당을 허용한다고 생각해보자. 프로그래머는 이 함수를 다음과 같은 식으로 호출할 것이다.

```typescript
login('myId');
```

이는 `loginWithToken` 함수를 `token` 인자 없이 호출하는 셈이다. `loginWithToken` 함수 내에서 `token`을 `string` 타입이라 생각하고 사용했다면, `string`이 필요한 자리에 `undefined` 값이 넘어와서 런타임 에러가 발생할 것이다. 따라서 이런 할당은 허용되지 않는다.

#### **할당받는 함수의 매개변수 수가 더 많은 경우**

아래 코드에서 할당하는 함수인 `login`은 할당받는 함수인 `loginWithToken`에 비해 매개변수 수가 하나 모자라다. 

```typescript
const login: Login = (id: string) => { /* ... */ };
const loginWithToken: LoginWithToken = login;
```

이런 경우, **초과 매개변수는 무시된다**. 그리고 매개변수 수가 같을 때와 동일한 알고리즘으로 호환성을 판단한다. 위의 경우, 초과 매개변수인 `token: string` 을 제외하고 첫 번째 매개변수는 동일한 타입을 가지므로 할당은 문제 없이 진행된다.

