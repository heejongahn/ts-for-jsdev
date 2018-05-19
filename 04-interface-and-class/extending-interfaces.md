# 4.3 인터페이스 확장

앞서 예시로 여러번 들었던 `User` 인터페이스를 다시 살펴보자.

```typescript
interface User {
  name: string;
  readonly height: number;
  favoriteLanguage?: string;
}
```

이 때, `User` 인터페이스가 갖는 특성을 모두 가지면서 추가적인 속성을 갖는 타입을 정의하고 싶은 경우를 생각해보자. 예를 들어, 로그인한 시간을 나타내는 `loggedInAt: Date` 속성을 추가로 들고 있는 `LoggedInUser` 와 같은 타입이 필요 할 수 있다.

이런 경우 `extends` 키워드를 이용한 **인터페이스 확장**을 통해 기존 인터페이스를 효율적으로 재사용 할 수 있다. 인터페이스 확장 문법은 아래와 같다.

```typescript
interface LoggedInUser extends User {
  loggedInAt: Date;
}
```

`LoggedInUser` 인터페이스는 `User` 인터페이스의 모든 속성\(`name: string`, `readonly height: number`, `favoriteLanguage?: string`\)을 가지며, 추가적으로 `loggedInAt: Date` 속성 또한 갖는다.

### **다수의 인터페이스 동시 확장**

인터페이스는 동시에 하나 이상의 인터페이스를 확장할 수 있다. 이 경우 확장 대상이 되는 인터페이스들은 반점\(`,`\)으로 구분한다. 새로 정의할 인터페이스는 모든 확장 대상 인터페이스의 속성에 자신의 속성을 더한 타입을 갖는다.

```typescript
interface ElectricDevice {
  voltage: number;
}
interface SquareShape {
  width: number;
  height: number;
}
interface Laptop extends ElectricDevice, SquareShape {
  color: string;
}
const macbook15: Laptop = { voltage: 220, width: 30, height: 21; color: 'white' };
```

이 때, 확장 대상인 인터페이스 중 여러 인터페이스가 같은 이름의 속성을 가질 수 있다. 그런 경우, **이름이 겹치는 속성의 타입은 모든 확장 대상 인터페이스에서 같아야 한다**. 만약 같은 이름의 속성이 다른 타입을 갖는 경우엔 타입 에러가 발생한다.

```typescript
interface BeverageLover {
  favoriteDrink: string;
}
interface BeerLover {
  favoriteDrink: 'beer';
}
interface CoolPerson extends BeverageLover, BeerLover {
  name: string;
}
// error TS2320: 
// Interface 'CoolPerson' cannot simultaneously extend types 'BeverageLover' and 'BeerLover'.
// Named property 'favoriteDrink' of types 'BeverageLover' and 'BeerLover' are not identical.
```

