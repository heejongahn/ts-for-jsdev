---
description: 타입 단언을 통해 컴파일러에게 특정 타입 정보의 사용을 강제할 수 있다.
---

# 6.3 타입 단언

타입스크립트 컴파일러는 타입 표기, 타입 좁히기와 타입 추론 등의 기법을 이용해 값의 타입을 판단한다. 하지만 때로는 컴파일러가 가진 정보를 무시하고 프로그래머가 원하는 임의의 타입을 값에 할당하고 싶을 수 있다. 이럴 때 필요한 것이 바로 **타입 단언**\(type assertion\)이다.

### 타입 단언 문법

`value as Type` 문법을 사용해 값 `value`를 `Type` 타입으로 단언할 수 있다. 이 표현은 다음과 같은 의미를 갖는다.

> `value`가 어떤 타입인지는 내가 가장 잘 알아. 책임은 프로그래머인 내가 질테니, 네가 갖고 있는 정보는 다 무시하고 `value`를 `Type` 타입의 값이라 생각하고 진행해.

일례로 아래 코드는 `Animal` 타입의 값을 `Fish`로 단언한다. 원래대로라면 `Dog`와 `Insect` 타입에는 `swim` 메소드가 없다는 에러가 났겠지만, 타입 단언으로 인해 컴파일러는 `animal`을 `Fish` 타입으로 해석하고, 타입 에러 없이 컴파일된다.

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
  (animal as Fish).swim();
}
```

주의할 점은 타입 단언은 타입 에러를 없애줄 뿐 런타임 에러를 막아주지 않는다는 점이다. 오히려 그 반대인데, 컴파일 타입에 잡을 수 있는 에러를 없앰으로서 원래대로면 생기지 않았을 런타임 에러를 발생시킬 수 있다. 실제로 위 함수는 런타임에 `Dog` 혹은 `Insect` 타입 값을 받으면 터질 것이다.

### **any** **타입 단언**

3장에서 타입스크립트의 비상 탈출구인 `any` 타입에 대해 다루었다. 값을 `any` 타입으로 단언함으로써 특정 값에 대한 타입 검사를 사실상 완전히 무효화할 수 있다.

```typescript
(3 as any).substr(0, 3);
```

위 코드는 실제로 실행한다면 런타임 에러가 발생하지만, 타입 검사는 통과한다. 번거로운 타입 검사를 피할 수 있지만, any를 사용한 타입 단언은 어쩔 수 없는 경우를 제외하곤 피하는 것이 좋다. 타입스크립트를 사용하는 근본적인 이유는 런타임에 발생할 에러를 컴파일 타임에 방지하기 위해서인데, any를 사용한 타입 단언은 그 의도에 정확히 반하기 때문이다.

### **다중 단언**

타입 단언은 여러번 겹쳐 사용할 수 있다.

```typescript
const wowSuchAny = (((42 as any) as any) as any) as any);
```

이러한 다중 단언은 호환되지 않는 것이 명백한 타입으로의 단언을 가능케 한다. 아래와 같은 타입 단언에선 타입 에러가 발생한다. `Dog` 타입을 `Insect`로 취급할 수 없다는 것을 컴파일러가 알기 때문이다.

```typescript
interface Dog {
  legs: 4;
  bark(): void;
}

interface Insect {
  legs: number;
  creepy: boolean;
}

const dog: Dog = { legs: 4, bark() { console.log('bark') } };
const insect: Insect = dog as Insect;
// error TS2352: Type 'Dog' cannot be converted to type 'Insect'.
//   Property 'creepy' is missing in type 'Dog'.
```

하지만 이러한 제약은 `any`로 한 번 타입 단언을 한 뒤, 그 값을 다시 `Insect`로 단언함으로서 피해갈 수 있다. 일단 `any` 타입으로 취급된 그 값은 모든 타입에 할당 가능하기 때문이다.

```typescript
const insect2: Insect = (dog as any) as Insect; // ok
```

다시 한 번 강조하지만, 타입 단언이 막아주는 건 타입 에러 뿐이다. 절대 런타임 오류가 나지 않을 것이라는 확신이 있거나 런타임 에러가 나도 상관 없는 상황이 아니라면 이런 식으로 호환되지 않는 타입을 `any`를 거쳐 단언하는 일은 피하는게 좋다.

