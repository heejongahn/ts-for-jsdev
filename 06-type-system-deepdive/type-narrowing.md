---
description: 타입스크립트가 타입을 보다 좁은 타입으로 좁히는 다양한 상황에 대해 알아본다.
---

# 6.1 타입 좁히기

앞서 유니온 타입을 통해 **여러 경우 중 하나인 타입**을 표현할 수 있음을 배웠다. `A | B` 는 `A`타입 **이거나** `B`타입인 타입을 나타낸다. 객체의 선택 속성 역시 유니온 타입의 특수한 경우라고 생각할 수 있다. 선택 속성 `prop?: T`에서 `prop`은 `undefined` **이거나** T인 타입이고, 이는 `prop: T | undefined` 와 유사하기 때문이다.

{% hint style="info" %}
`prop?: T` 와 `prop: T | undefined` 는 정확히 동일한 의미는 아니다. 후자는 `prop` 이라는 이름의 속성이 존재할 것을 보장하지만, 전자는 그렇지 않다. 아래 예제를 보면 그 차이가 쉽게 이해 될 것이다.

```typescript
interface OptionalProp {
  prop?: number;
}
interface UnionProp {
  prop: number | undefined;
}
const optional: OptionalProp = {}; // ok
const union: UnionProp = {}; 
// error TS2322: Type '{}' is not assignableto type 'UnionProp'.
//   Property 'prop' is missing in type '{}'.
```

다만 지금 다룰 타입 좁히기라는 주제에 있어서는 선택 속성을 유니온 타입으로 보는 것에 큰 무리는 없다.
{% endhint %}

하지만 프로그램의 로직에 의해 값이 여러 경우의 수 중 어떤 타입을 가지는지, 또 어떤 타입은 확실히 아닌지가 명백해지는 상황들이 있다. 다음 코드를 보자.

```typescript
interface Person {
  favoriteLanguage?: string;
}

function isFavoriteLangScript(p: Person): boolean {
  // A
  if (p.favoriteLanguage === undefined) {
    return false;
  }
  
  //B
  const lowerCased = p.favoriteLanguage.toLowerCase();
  return lowerCased.includes('script');
}
```

`A` 시점에서 `p.favoriteLanguage`의 타입은 당연히 `string | undefined` 다. 하지만 `B`에서는 어떨까? 

함수 내의 `if` 문을 보면, `p.favoriteLanguage`가 `undefined` 라면 함수는 이른 반환\(early return\)을 수행한다. 이 경우에는 실행 흐름이 `B`에 도달하기 전에 함수를 빠져나간다. 따라서 실행 흐름이 `B`에 도달했다면 `p.favoriteLanguage`는 `string` 타입일 수 밖에 없다. 타입스크립트는 이러한 상황을 이해하여 위의 `if` 문 이후부터는 `p.favoriteLanguage`의 타입을 `string` 으로 인식한다.

이런 식으로 보다 넓은\(더 많은 경우의 수를 갖는\) 타입을 더 좁은\(더 적은 경우의 수를 갖는\) 타입으로 재정의하는 행위를 **타입 좁히기**\(type narrowing\)라 부른다. 위의 예제에서는 `if` 문을 통해 `string | undefined` 라는 넓은 타입으로부터 `string` 이라는 좁은 타입으로의 타입 좁히기가 일어났다.

타입 좁히기가 없다면 불필요한 런타임 검사를 계속 해야 하고, 결과적으로 유니온 타입을 사용하기가 훨씬 불편해질 것이다. 예를 들어 위 예제 코드에서 타입 좁히기가 일어나지 않으면 어떻게 될까? 아래와 같이 `B` 이후로도 `p.favoriteLanguage`를 사용하려 할 때마다 `undefined` 여부를 체크해줘야 할 것이다.

```typescript
function isFavoriteLangScript(p: Person): boolean {
  if (p.favoriteLanguage === undefined) {
    return false;
  }
  
  // String 타입의 메소드에 접근하기 위해 체크가 필요
  if (p.favoriteLanguage !== undefined) {
    const lowerCased = p.favoriteLanguage.toLowerCase();
    return lowerCased.includes('script');
  }
  // 반환 타입을 맞추기 위해 적어줘야 함
  return false;
}
```

### **타입 가드**

특정 스코프 내에서 값의 타입을 좁히는, 즉 타입 좁히기를 유발하는 표현을 **타입 가드**\(type guard\)라 부른다. 타입 가드는 크게 두 종류로 나뉜다. 한 종류는 **제어 흐름 분석**\(control flow analysis\)을 통해 타입을 좁히는 가드들이다. 그리고 다른 하나는 프로그래머가 값을 어떤 타입으로 좁혀야 하는지 직접 명시할 수 있는 수단인 **사용자 정의 타입 가드**\(user defined type guard\)다.

### **제어 흐름 분석**

기본적으로 비동기 실행 코드를 제외하면 코드는 위에서 아래로 차례대로 실행된다. 대부분의 프로그래밍 언어는 특정 조건이 만족될 때에만 코드를 실행하거나 같은 코드를 여러번 실행하는 식으로 순차적 실행을 벗어난 실행을 가능하게 하는 제어 구조\(control structure\)를 제공한다. 자바스크립트와 타입스크립트 역시 예외는 아니며, 이 언어들의 대표적인 제어 구조는 다음과 같다.

* `if`, `else if`, `else`
* `while`, `for`
* `switch`, `case`
* `break`, `continue`
* `return`

컴파일러는 이런 제어 구조로부터 코드의 특정 시점에서 프로그램이 갖는 상태에 대한 정보를 얻어낼 수 있다. 그리고 컴파일러는 이러한 정보를 이용해 제어 흐름 분석\(control flow analysis\)를 진행해 특정 값의 타입을 좁혀낼 수 있다. 제어 흐름 분석을 가능케 하는 타입 가드들엔 어떤 종류가 있고, 타입이 어떻게 좁혀지는지 살펴보자.

#### **undefined / null 과의 비교**

`undefined` 또는 `null`과의 비교는 각각 대응하는 타입에 대한 타입 가드로 동작한다. 아래 예제에서는 `if` 문에서의 null 체크가 타입 가드로 동작한다.

```typescript
interface Animal {
  ownerName: string | null;
}

function getOwnerName(animal: Animal): string {
  if (animal.ownerName === null) {
    return 'wildness';
  } else {
    // animal.ownerName 타입은 string
    return animal.ownerName;
  }
}
```

앞서 살펴본 `Person.favoriteLanguage` 예제 역시 `undefined` 와의 비교가 타입 가드로 동작한 경우다.

#### **리터럴 타입과의 비교**

리터럴 타입과의 비교 또한 타입 가드로 동작한다. 아래 예제를 보자.

```typescript
interface TeamLeader {
  type: 'leader';
  leadingSince: Date;
}

interface Newcomer {
  type: 'newcomer';
  major: string;
}

type Employee = TeamLeader | NewComer;

function doSomething(employee: Employee) {
  switch (employee.type) {
    case 'leader': {
      // employee는 TeamLeader 타입
      return employee.leadingSince;
    }
    case 'newcomer': {
      // employee는 Newcomer 타입
      return employee.major;
    }
    default: {
      // employee는 never 타입
      return null;
    }
  }
}
```

리터럴 타입인 `employee.type`을 기반으로 `switch`-`case`를 통해 각 브랜치에서 `employee`의 타입을 좁힐 수 있었다. 앞의 두 케이스가 가능한 모든 케이스를 처리했으므로 `default` 브랜치에서 `employee`는 `never` 타입이 되는데, 이 역시 타입 좁히기 덕분이다.

{% hint style="info" %}
위와 같은 식으로 리터럴 타입 식별자\(discriminator\)를 갖는 유니온 타입을 서로소 유니온 타입이라 부른다. 서로소 유니온 타입에 대해서는 이 장의 끝부분에서 다시 자세히 다룬다.
{% endhint %}

#### **typeof 연산자**

`typeof` 연산자는 하나의 인자를 받아 해당 인자의 타입을 나타내는 문자열을 반환한다. `typeof`의 반환값과 문자열을 비교한 결과를 타입 가드로 사용할 수 있다. 공식 문서의 예제를 보자.

```typescript
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  
  if (typeof padding === "string") {
    return padding + value;
  }
  
  throw new Error(`Expected string or number, got '${padding}'.`);
}
```

{% hint style="info" %}
`typeof` 연산자는 보통의 프로그래머의 예상과는 다르게 동작한다는 점을 주의해야 한다. 흔히 쓰이는 값의 타입과 `typeof` 연산자의 반환값을 보면 아래 표와 같다.

하위 호환성 이슈로 인해 `typeof null`은 `"null"`이 아닌 `"object"` 다. 또한 `typeof [] === "array"` 일 것이란 예상과 달리 배열을 나타내는 별도의 반환값은 존재하지 않는다. 이런 혼란스러운 동작 탓에 일반적으로 `typeof` 타입 가드는 `boolean`, `string`, `number`, `symbol` 등 단순한 타입에 대해서만 사용하는 것이 권장된다.
{% endhint %}

| 타입 | `typeof` 반환값 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Undefined | "undefined" |
| Null | "object" |
| Boolean | "boolean" |
| Number | "number" |
| String | "string" |
| Symbol | "symbol" |
| Function | "function" |
| 그 외 모든 객체 | "object" |

#### **instanceof** **연산자**

instanceof 연산자는 값과 생성자를 받아 해당 값의 프로토타입 체인에 해당 생성자가 등장하는지를 확인한다. ES6 클래스 역시 내부적으로는 프로토타입 체인에 기반해 돌아가므로, 클래스의 인스턴스 여부도 instanceof를 이용해 확인할 수 있다. 공식 예제를 보자.

```typescript
interface Padder {
  getPaddingString(): string;
}

class SpaceRepeatingPadder implements Padder {
  constructor(private numSpaces: number) { }
  getPaddingString() {
    return Array(this.numSpaces + 1).join(" ");
  }
}

class StringPadder implements Padder {
  constructor(private value: string) { }
  getPaddingString() {
    return this.value;
  }
}

function getRandomPadder() {
  return Math.random() < 0.5 ?
    new SpaceRepeatingPadder(4) :
    new StringPadder("  ");
}

// 이 시점에선 'SpaceRepeatingPadder | StringPadder'
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
  padder; // SpaceRepeatingPadder 로 좁혀짐
}

if (padder instanceof StringPadder) {
  padder; // StringPadder 로 좁혀짐
}
```

#### **in 연산자**

`in` 연산자는 객체에 특정 속성이 존재하는지 여부를 확인할 때 사용된다.

```typescript
const obj = { a: 123 };
console.log('a' in obj); // true
console.log('b' in obj); // false
```

`in` 연산자의 결과 역시 역시 타입 가드로 쓸 수 있다.

```typescript
interface Dog {
  legs: 4;
  bark(): void;
}

interface Insect {
  legs: number;
  creepy: boolean;
}

interface Fish {
  swim(): void;
}

type Animal = Dog | Insect | Fish;

function doSomethingWithAnimal(animal: Animal) {
  if ('legs' in animal) {
    // animal은 Dog | Insect 타입
    console.log(animal.legs);
  } else {
    // animal은 Fish 타입
    animal.swim();
  }
}
```

### **사용자 정의 타입 가드**

지금까지는 타입스크립트 언어에 내장된 제어 흐름에 기반하여 동작하는 타입 가드를 살펴보았다. 그와 다르게 프로그래머가 직접 임의의 기준을 사용해 타입 가드를 정의할 수도 있다. 이러한 타입 가드를 사용자 정의 타입 가드라 부른다.

사용자 정의 타입 가드는 `value is Type` 형태의 반환 타입을 갖는 **함수**로 정의한다. 예를 들어 아래와 같이 `isFish` 사용자 정의 타입 가드를 정의해서 위의 `doSomethingWithAnimal` 함수를 고쳐 쓸 수 있다.

```typescript
function isFish(animal: Animal): animal is Fish {
  if ('legs' in animal) {
    return false;
  }
  return true;
}

function doSomethingWithAnimal(animal: Animal) {
  if (isFish(animal)) {
    // animal은 Fish 타입
    animal.swim();
  } else {
    // animal은 Dog | Insect 타입
    console.log(animal.legs);
  }
}
```

{% hint style="info" %}
초기 버전의 타입스크립트는 타입 시스템의 힘이 강력하지 않았고, 제어 흐름 분석에 기반한 타입 좁히기가 거의 이루어지지 않았다. 때문에 사용자 정의 타입 가드를 사용해야 하는 경우가 많았다. 하지만 꾸준히 발전을 거듭한 오늘날의 타입스크립트에선 타입 좁히기가 똑똑하게 이루어지고, 대부분의 사용례는 위에서 다룬 내장 타입 가드로도 충분히 커버할 수 있다.
{% endhint %}

