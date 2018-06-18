---
description: 컴파일러는 타입 추론을 통해 명시적인 타입 표기 없이도 타입 정보를 이해할 수 있다.
---

# 6.2 타입 추론

지금까지의 이 책의 모든 코드 예제는 타입을 명시적으로 표기해줬다. 하지만 3장의 도입부에서 언급했듯, 타입스크립트는 타입 추론을 지원한다. 프로그래머가 타입 표기를 적지 않아도 값의 타입을 추론해 낼 수 있는 것이다. 다음은 타입 표기를 명시적으로 적어준 버전이다.

```typescript
let x: number = 3;
```

하지만 위의 코드에서 타입 표기를 없애더라도 타입스크립트는 `3`이라는 값으로부터 `x`가 `number` 타입일 것이라 추론해낸다.

```typescript
let x = 3; // number 타입으로 추론
```

한 편, `let` 대신 `const` 를 사용하면 어떨까? 타입스크립트는 이처럼 재할당이 불가능한 경우에는 변수의 타입을 보다 구체적인 숫자 리터럴 타입으로 추론한다. 이러한 속성은 인터페이스나 클래스의 `readonly` 속성에도 비슷하게 적용된다.

```typescript
const x = 3; // 3 타입으로 추론
```

물론 의도한 타입이 할당하는 값만으로는 드러나지 않는 경우엔 여전히 타입 표기가 필요하다. 예를 들어 아래의 코드에서 타입 표기 없이 컴파일러가 `1 | 3` 이라는 타입을 추론하긴 불가능 할 것이다.

```typescript
const oneOrThree: 1 | 3 = 3;
```

### **최적의 공통 타입**

하나의 값에 대한 타입 추론은 단순하다. 그렇다면 여러 값이 연관된 타입을 추론할 때는 어떨까? 다음 코드를 보자.

```typescript
interface Animal {
  legs: number;
}

interface Dog extends Animal {
  bark(): void;
}

interface Cat extends Animal {
  meow(): void;
}

let dog: Dog;
let cat: Cat;
const dogAndCat = [dog, cat]; // ??
```

`dogAndCat`은 `Dog` 타입의 원소와 `Cat` 타입의 원소를 갖는 배열이다. 이러한 배열의 타입은 어떻게 추론해야 할까?

이런 상황에서 타입스크립트는 **최적 공통 타입**\(best common type\)이란 접근법을 사용한다. 원리는 간단한데, 모든 가능한 타입의 유니온 타입을 사용하는 것이다. 예를 들어 위 `dogAndCat`의 타입을 `Array<Dog | Cat>` 으로 추론하는 식이다.

이 때 두 인터페이스 모두 `Animal` 인터페이스를 호환받았으니 `Array<Animal>`로 추론하면 되는 것 아닐지 궁금해 할 수 있다. 그렇게 동작하지 않는 이유를 이해하기 위해 다음 예제를 보자.

```typescript
interface Camel extends Animal {
  humps: number;
}

function getSoundFunction(dogOrCat: Dog | Cat) {
  if ('meow' in dogOrCat) {
    return dogOrCat.meow;
  } else {
    return dogOrCat.bark;
  }
}
```

타입스크립트는 최적 공통 타입에 따라 `dogAndCat`를 `Array<Dog | Cat>` 타입으로 추론한다. 따라서 다음 코드는 실제로 타입 검사를 통과한다. 아래 코드는 실제로 문제가 생길 여지가 없으므로 이는 바람직한 동작이다.

```typescript
dogAndCat.map(dogOrCat => getSoundFunction(dogOrCat));
```

하지만 만약 배열의 타입을 `Array<Animal>`로 추론한다면 어떻게 될지 생각해보자. `getSoundFunction` 함수는 `Camel` 타입을 인자로 받지 않는다. 그 때문에, `dogAndCat` 내에는 `Camel` 타입 값이 존재하지 않음에도 위의 코드에선 타입 에러가 날 것이다.

이런 불편함을 막고자, 최적 공통 타입에서는 타입 추론에 사용된 값들의 타입\(이 경우엔 `Dog`와 `Cat`\)만을 재료로 사용한다. `dogAndCat`이 `Array<Animal>` 타입을 갖길 원한다면 타입 추론에 의존하는 대신 명시적으로 타입 표기를 해 주면 된다.

### **문맥 상의 타입**

할당이 일어날 때, 타입 추론은 할당 받는 값\(왼쪽 항\)의 타입 뿐만 아니라 할당하는 값\(오른쪽 항\)의 타입에 대해서도 일어난다. 이렇게 추론된 타입을 문맥 상의 타입\(contextual type\)이라 부른다.

```typescript
window.onmousedown = function(mouseEvent) {
  console.log(mouseEvent.a);
};
```

이 때, `Window` 인터페이스의 `onmousedown` 속성은 아래와 같이 정의되어 있다.

```typescript
interface MouseEvent {
  /* ... */
  /* button 속성 없음! */
}

interface Window {
  /* ... */
  onmousedown: (event: MouseEvent) => void;
}
```

따라서 타입스크립트는 우변의 함수가 `(event: MouseEvent) => void` 타입일 것이라고 추론한다. 이 때 함수 내부에서 `event.a` 속성에 접근하는데, `a` 속성은 `MouseEvent` 타입에 존재하지 않으므로 타입 에러가 발생한다.

만약 타입 표기가 주어졌다면 문맥 상의 타입은 무시된다. 예를 들어, 다음과 같이 `mouseEvent` 매개변수의 타입을 표기해주면 위의 에러는 사라진다.

```typescript
window.onmousedown = function(mouseEvent: any) {
  console.log(mouseEvent.a);
};
```

