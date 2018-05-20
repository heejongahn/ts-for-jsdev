---
description: 타입스크립트가 제공하는 기본 타입을 살펴본다. 이후 다룰 모든 타입은 이 기본 타입들로부터 파생된다.
---

# 3.1 기본 타입

## **불리언**

자바스크립트의 `boolean`에 대응하는, 참 또는 거짓을 나타내는 타입이다.

```typescript
const isTypeScriptAwesome: boolean = true;
const doesJavaScriptHasTypes: boolean = false;
```

## **숫자**

숫자를 나타내는 타입이다. 자바스크립트에서는 정수, 부동 소수점 등의 구분이 따로 없고 모든 수가 [IEEE754](https://ko.wikipedia.org/wiki/IEEE_754) 표준을 따르는 부동소수점이고, 타입스크립트의 `number` 타입도 마찬가지다.

```typescript
const yourScore: number = 100;
const ieee754IsAwesome: number = 0.1 + 0.2; // 0.30000000000000004
```

## **문자열**

문자열을 나타내는 타입이다. ES6 템플릿 리터럴 역시 `string` 타입의 값이다.

```typescript
const authorName: string = '안희종';
const toReaders: string = `
책을 읽어주셔서 감사합니다.
도움이 되었으면 좋겠습니다.
`;
```

## **null / undefined**

`null` 타입과 `undefined` 타입은 각각 `null`과 `undefined`라는 하나의 값만을 갖는다. 이 두 값을 자기 자신의 타입, 그리고 아래에서 언급될 `void` 타입 이외의 타입에 할당하려 하면 타입 에러\(`TS2322: Type 'null' is not assignable to type 'number'` 등\)가 발생한다.

```typescript
const nullValue: null = null;
const undefinedValue: undefined = undefined;
const numberValue: number = null; // TS2322: Type 'null' is not assignable to type 'number'
```

{% hint style="info" %}
타입스크립트에서, 원래 `null`과 `undefined`는 기본적으로 모든 타입의 서브타입이다. 즉 아무런 설정이 없다면 아래와 같은 식의 할당이 허용된다.

```typescript
const a: number = null; // okay
```

하지만 이런 동작은 버그를 양산하기 쉽다. 때문에 타입스크립트 2.0에 `null`과 `undefined` 값을 다른 타입에 할당하는 것을 막는 `--strictNullChecks` 플래그가 [추가되었다](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#--strictnullchecks).

앞서 언급했듯 이 책의 모든 코드 예제는 `--strict`플래그가 켜진 환경을 가정하고 있으며, `--strictNullChecks` 플래그는 `--strict` 플래그에 포함된다. 실 프로젝트에서도 해당 플래그를 켜는 것을 추천한다.
{% endhint %}

## **특별한 타입**

자바스크립트에서 직접적으로 대응되는 값은 없지만 타입스크립트가 제공하는 특수한 타입이 몇 가지 있다.

### **any**

`any` 타입은 모든 타입과 호환 가능하다. 즉, 모든 값의 타입을 `any` 로 지정할 수 있고, `any` 타입의 변수에는 모든 값을 할당할 수 있다.

```typescript
let bool: any = true;
bool = 3;
bool = 'whatever';
bool = {};
```

또한 `any` 타입 값의 메소드를 호출할 시에도 타입 검사가 아예 수행되지 않는다. 이 때 해당 실제로 존재하지 않는다면 타입 검사는 통과하되 런타임 에러가 발생할 것이다.

```typescript
bool.nonExistingMethod();
bool.whatever(false);
```

`any` 타입은 타입스크립트 타입 시스템의 비상 탈출구\(escape hatch\)이다. `any`는 타입 정의를 제공하지 않는 라이브러리, 일단 무시하고 넘어가고 이후에 정확히 적고 싶은 부분 또는 코드 작성 시점에 형태를 알 수 없는 값 등의 타입 표기에 유용하다. 하지만 `any`를 남용하면 타입 안정성에 구멍이 뚫린 코드가 되어 타입스크립트를 사용하는 의의가 사라지므로 꼭 필요한 경우에만 사용해야 한다.

### **void**

`void`는 `null`과 `undefined` 만을 값으로 가질 수 있는 타입이다. 아무런 값도 반환하지 않는 함수의 반환 타입을 표시할 때 사용한다.

```typescript
function nothing(): void { }
```

### **never**

`never`는 아무런 값도 가질 수 없는 타입이다. 아무런 값도 가질 수 없는 타입은 과연 어떤 쓸모가 있을까? 아래 함수를 보자.

```typescript
function alwaysThrow(): ??? {
  throw new Error(`I'm a wicked function!`);
}
```

의미상으로 `never` 타입은 – 그 이름이 암시하듯 – 절대 존재할 수 없는 값을 암시한다. 따라서 `never` 타입의 변수에는 `null`, `undefined`를 포함해 어떤 값도 할당할 수 없다. 위의 `alwaysThrow` 함수는 항상 에러를 `throw` 하므로 어떤 값도 반환하지 않는다. 이 때, 이런 함수의 반환 타입을 `never` 타입을 사용해 나타낼 수 있다.

```typescript
function alwaysThrow(): never {
  throw new Error(`I'm a wicked function!`);
}
```

