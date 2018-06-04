---
description: 타입스크립트가 객체 타입 간의 호환성을 판단하는 법에 대해 다룬다.
---

# 5.2 객체 타입의 호환성

다음 두 인터페이스를 보자.

```typescript
interface User {
  name: string;
  height: number;
}
interface Pet {
  name: string;
  species?: string;
}
const user: User = { name: '안희종', age: 176 };
const puppy: Pet = { name: '해피' };
```

`user`의 타입은 `User`이고, `puppy`의 타입은 `Pet`이다. 이 때 아래 코드를 실행했을 때 어떤 결과가 나올까? 

```typescript
const pet2: Pet = user; // ??
const user2: User = pet; // ??
```

즉, `User`는 `Pet`에 할당 가능할까? 또 반대로 `Pet`은 `User`에 할당 가능할까?

### **두 가지 기준**

타입스크립트가 객체 타입 사이의 할당 가능성을 판단하는 기준은 사실 꽤 간단하다. 타입 `A`와 `B`에 대해, `A`가 `B`에 할당 가능하려면 다음 두 기준을 만족해야 한.

* `B` 타입의 모든 필수 멤버에 대해, `A` 에도 같은 이름의 멤버가 존재하는가?
* `B` 타입과 `A` 타입에 동시에 존재하는 멤버 `m`에 대해, `A.m` 의 타입을 `M`, `B.m`의 타입을 `M'`라 하자.  이 때, 모든 `m`에 대해서 `M`이 `M'`에 할당 가능한가?

이 때, 두 질문에 대한 답이 **모두** “예”라면 `A`는 `B`에 할당 가능하다. 반대로 하나라도 만족되지 않으면, `A`는 `B`에 할당 불가능하다. 예시를 들어 살펴보자. 

#### **User는** **Pet에 할당 가능한가?**

* `Pet`의 필수 멤버는 `name` 하나다. 같은 이름의 멤버가 `User`에도 존재한다.
* `Pet`의 `name` 멤버 타입은 `string` 이다\(`type M = string`\). `User` 의 `name` 멤버 타입 또한 그렇다\(`type M' = string`\). 둘은 같은 타입이므로 당연히 서로 할당 가능하다.

두 조건이 모두 만족되므로, `User`는 `Pet`에 할당 가능하다. 이 때 두 가지 눈여겨 볼 점이 있다. 

* `Pet`에는 `User`의 `height: number` 속성이 존재하지 않지만 이는 무관하다. **할당 가능성을 따질 때엔 할당을 받는 쪽의 타입만이 중요하다**.
* 선택 속성으로 정의된 `species?: string` 은 없어도 무방하다.

#### **Pet은** **User에 할당 가능한가?**

* `User`의 필수 멤버는 `name`과 `height` 둘이다. 이 때, `Pet` 타입엔 `height` 멤버가 존재하지 않는다.

따라서 두 번째 조건은 볼 것도 없이, `Pet`은 `User`에 할당 불가능하다. `const user2: User = pet` 과 같은 시도는 타입 에러를 발생시킨다.

### **구조적 타입 시스템**

이렇듯, 타입스크립트에서는 두 타입의 구조\(structure\)만을 비교하여 호환성을 결정한다. 어떤 타입이 다른 타입이 갖는 멤버를 전부 가지고 있다면 그걸로 충분하며, 두 타입이 호환되는 타입이라는 명시적인 표시는 필요하지 않다. 이렇게 동작하는 타입 시스템을 **구조적 타입 시스템**\(structural type system\)이라 부른다. 

반대되는 개념으로는 C++, Java 등의 언어가 채택한 노미널 타입 시스템\(nominal type system\)이 있다. 노미널 타입 시스템을 갖는 언어에서는 특정 키워드를 통해 서로 호환 가능하다고 명시적으로 표현 된 타입 간의 할당만이 허용된다.

### **객체 리터럴과 과잉 속성 검사**

다음 코드를 보자.

```typescript
interface Color {
  R: number;
  G: number;
  B: number;
}

const white: Color = {
  R: 255,
  G: 255,
  B: 255,
  A: 1
};
```

변수 `white`는 `Color` 타입을 갖는다. 그리고 `white`에 할당하려는 객체는 `R`, `G`, `B` 세 멤버를 모두 갖고 있고, 세 멤버의 모두 `number` 타입다. 따라서 위에서 살펴본 구조적 타입 검사에 의하면 이 할당에는 아무런 문제가 없어야 한다.

하지만 실제로 위의 할당은 에러가 발생한다. 에러 메시지는 다음과 같다.

```typescript
error TS2322: Type '{ R: number; G: number; B: number; A: number; }' is not assignable to type 'Color'.
  Object literal may only specify known properties, and 'A' does not exist in type 'Color'.
```

객체 리터럴은 알려진 속성\(known property\)만을 가질 수 있는데, `Color` 타입에는 `A` 속성이 존재하지 않으므로 할당이 불가능하다는 메시지이다. 하지만 분명 할당을 받는 쪽의 타입만이 중요하고, 추가적인 멤버를 갖는건 상관 없다고 하지 않았나? 이게 어찌 된 일일까?

이러한 현상이 발생하는 이유는 우리가 할당하는 값이 변수나 표현식이 아닌 **객체 리터럴**이기 때문이다. 객체 리터럴을 할당하는 경우에는 그 리터럴이 알려지지 않은 속성\(unknown property\), 즉 할당 받는 타입에 존재하지 않는 속성을 포함한다면 타입 에러가 발생한다.

실제로 위의 코드를 아래와 같이 객체 리터럴이 아닌 변수를 할당하도록 바꾸면 에러는 사라진다.

```typescript
interface Color {
  R: number;
  G: number;
  B: number;
}

const someColor = {
  R: 255,
  G: 255,
  B: 255,
  A: 1
};
const white: Color = someColor;
```

이렇게 객체 리터럴에 대해서만 알려지지 않은 속성은 없는지 추가적으로 시행하는 검사를 과잉 속성 검사\(excess property checking\)라 부른다.

#### **과잉 속성 검사가 존재하는 이유**

과잉 속성 검사는 프로그래머의 실수를 막기 위해 존재한다. 어떤 타입의 값에 객체 리터럴을 직접 할당하는 경우, 만약 해당 타입에 정의되지 않은 멤버는 오타 등의 실수로 인해 존재할 확률이 높다고 가정하는 것이다. 공식 문서에서 빌려온 아래 예제를 보자.

```typescript
interface SquareConfig {
  width?: number;
  color?: string;
}
const squareConfig: SquareConfig = {
  width: 100,
  colour: red
};
```

구조적 타입 시스템의 원칙에 따르면 위 할당에는 문제가 없다. 하지만 할당 시점에 딱 한 번만 사용될 객체 리터럴에 `colour`라는 알려지지 않은 속성이 존재한다면, `color`를 타이핑하려다 오타가 난 경우가 대부분일 것이다. 과잉 속성 검사를 시행해서 이런 흔하게 예상되는 오류를 컴파일러가 잡아 줄 수 있다.

