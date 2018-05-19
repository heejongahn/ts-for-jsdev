---
description: 유한한 상수 값의 집합을 표현하기 위해 사용하는 열거형(enum) 타입에 대해 배운다.
---

# 3.9 열거형

### **숫자 열거형\(Numeric Enum\)**

숫자 열거형은 `number` 타입 값에 기반한 열거형이다. 만약 열거형을 정의하며 멤버의 값을 초기화하지 않을 경우, 해당 멤버의 값은 `0`부터 순차적으로 증가하는 숫자 값을 갖는다. 예를 들어 아래 예제 두 예제는 동일하게 동작한다.

```typescript
enum Direction {
  East,
  West,
  South,
  North
}
enum ExplicitDirection {
  East = 0,
  West = 1,
  South = 2,
  North = 3
}
```

이렇게 정의한 열거형의 멤버에는 객체의 속성에 접근하는 것과 동일한 방식으로 접근할 수 있다. 어떤 열거형 `Enum`의 모든 멤버는 `Enum` 타입을 갖는다.

```typescript
const south: Direction = Direction.South;
console.log(south); // 2
```

### **멤버 값 초기화**

`0`부터 시작되는 자동 초기화에 의존하는 대신, 각 멤버의 값을 직접 초기화 할 수 있다.

```typescript
enum InitializedDirection {
  East = 2,
  West = 4,
  South = 8,
  North = 16
}
```

만약 초기화 되지 않은 멤버가 섞여있다면, 그 멤버의 값은 이전에 초기화된 멤버의 값으로부터 순차적으로 증가해서 결정된다.

```typescript
enum InitializedDirection2 {
  East = 3,
  West /* 4 */,
  South = 7,
  North /* 8 */
}
```

### **문자열 열거형\(String Enum\)**

`number` 타입 값 대신 `string` 타입 값을 사용해서 멤버 값을 초기화하는 것도 가능하다. 

```typescript
enum Direction {
  East = 'EAST',
  West = 'WEST',
  South = 'SOUTH',
  North = 'NORTH'
}
```

문자열 열거형은 숫자 열거형과 다음 부분을 제외하고는 많은 부분 동일하다.

* 문자열을 ‘자동 증가’ 시킨다는 개념은 성립하지 않는다. 따라서 문자열 멤버 이후로 정의된 모든 멤버는 명시적으로 초기화되어야 한다.
* 숫자 열거형과 달리, 문자열 열거형이 컴파일된 자바스크립트 코드에는 값 → 키 의 역방향 매핑\(reverse mapping\)이 존재하지 않는다.

{% hint style="info" %}
한 열거형에서 숫자 멤버와 문자열 멤버를 모두 사용하는 식의 이형 열거형\(Heterogeneous Enum\)도 문법 상 허용은 된다. 하지만 이형 열거형을 사용해 큰 이득을 얻을 수 있는 경우는 드물고, 대부분의 경우 혼란을 불러 올 수 있어 권장되지 않는다.
{% endhint %}

### **상수 멤버와 계산된 멤버**

지금까지 다룬 열거형의 멤버는 모두 명시적이든, 암시적든 컴파일 타임에 알 수 있는 상수값으로 초기화 되었다. 이런 열거형 멤버를 상수 멤버\(constant member\)라 부른다.

한 편, 런타임에 결정되는 값을 열거형의 멤버 값으로 사용할 수도 있다. 이런 멤버를 계산된 멤버\(computed member\)라고 부른다. 계산된 멤버의 값은 실제로 코드를 실행시켜봐야만 알 수 있으므로, 계산된 멤버 뒤에 오는 멤버는 반드시 초기화되어야 한다는 점에 유의하라.

```typescript
function getAnswer() {
  return 42;
}
enum SpecialNumbers {
  Answer = getAnswer(),
  Mystery // error TS1061: Enum member must have initializer.
}
```

### **런타임에서의 열거형**

기본적으로 아래와 같은 타입스크립트 코드에서의 열거형 정의 및 접근은

```typescript
enum Direction {
  East,
  West,
  South,
  North
}
const east: Direction = Direction.East;
```

아래와 같은 자바스크립트 코드로 컴파일 된다.

```javascript
var Direction;
(function (Direction) {
    Direction[Direction["East"] = 0] = "East";
    Direction[Direction["West"] = 1] = "West";
    Direction[Direction["South"] = 2] = "South";
    Direction[Direction["North"] = 3] = "North";
})(Direction || (Direction = {}));
var east = Direction.East;
```

이 코드를 보면 두 가지 일이 일어나고 있음을 확인할 수 있다.

* 식별자에 키 → 값으로의 매핑이 정의된다. \(`Direction["EAST"] = 0`\)
* 식별자에 값 → 키로의 역방향 매핑이 정의된다. \(`Direction[Direction["East"] = 0] = "East"`\)

{% hint style="info" %}
문자열 열거형의 경우 앞서 언급한대로 역방향 매핑이 존재하지 않는다. 아래의 문자열 열거형은

```typescript
enum Direction {
  East = 'EAST',
  West = 'WEST',
  South = 'SOUTH',
  North = 'NORTH'
}
```

아래 자바스크립트 코드로 컴파일된다.

```typescript
var Direction;
(function (Direction) {
    Direction["East"] = "EAST";
    Direction["West"] = "WEST";
    Direction["South"] = "SOUTH";
    Direction["North"] = "NORTH";
})(Direction || (Direction = {}));
```
{% endhint %}

컴파일된 코드로부터 열거형 멤버에 접근 할 때 실제로 코드가 실행될 때에도 객체 속성 접근이 발생함을 알 수 있다. 이 오버헤드는 대부분의 경우 무시 가능할 수준이다. 그럼에도 성능 향상을 꾀하고 싶다면 `const` 열거형을 사용할 수 있다.

모든 멤버가 컴파일 시간에 알려진 상수값인 열거형의 경우 `enum` 키워드 대신 `const enum` 키워드를 이용해 정의할 수 있다. 이렇게 정의한 열거형의 구조는 컴파일 과정에서 완전히 사라지고, 멤버의 값은 상수값으로 대체된다. 아래의 예제를 보자.

```typescript
const enum ConstEnum {
  A,
  B = 2,
  C = B * 2,
  D = -C,
}
console.log(ConstEnum.A);
```

위 코드는 아래 자바스크립트 코드로 컴파일된다.

```typescript
console.log(0 /* A */);
```

주석을 제외하고는 열거형의 원래 구조에 대한 어떠한 정보도 남아있지 않고, 상수값으로 대체되어 있음을 확인할 수 있다.

### **유니온 열거형**

열거형의 모든 멤버가 아래 경우 중 하나에 해당하는 열거형을 유니온 열거형\(union enum\)이라 부른다.

* 암시적으로 초기화 된 값 \(값이 표기되지 않음\)
* 문자열 리터럴
* 숫자 리터럴

예를 들어 아래 `ShapeKind` 열거형은 유니온 열거형이다.

```typescript
enum ShapeKind {
  Circle,
  Triangle = 3,
  Square
}
```

유니온 열거형의 멤버는 값인 동시에 타입이 된다. 따라서 예를 들어 아래와 같은 코드를 작성할 수 있다.

```typescript
type Circle = {
  kind: ShapeKind.Circle;
  radius: number;
}
type Triangle = {
  kind: ShapeKind.Triangle;
  maxAngle: number;
}
type Square = {
  kind: ShapeKind.Square;
  maxLength: number;
}
type Shape = Circle | Triangle | Square;
```

또한 컴파일러는 유니온 열거형의 특징으로부터 컴파일 타임에 추가적인 검사를 시행할 수 있다. 이에 대해서는 추후 타입 좁히기\(type narrowing\)에 대해 다룰 때 함께 다룬다.

### **유니온 타입을 이용한 열거형 표현**

타입스크립트는 숫자, 문자열 그리고 불리언 값을 타입으로 사용하는 리터럴 타입\(literal type\)을 지원한다. 리터럴 타입을 이용해 단 하나의 값만을 갖는 타입을 정의할 수 있다.

```typescript
const answer: 42 = 42;
const wrongAnswer: 42 = 24; // error TS2322: Type '24' is not assignable to type '42'.
```

이 때 리터럴 타입과 유니온 타입을 조합해 열거형과 유사한 타입을 만들 수 있다.

```typescript
type Direction = 'EAST' | 'WEST' | 'SOUTH' | 'NORTH';
const east: Direction = 'EAST';
const center: Direction = 'CENTER'; // error TS2322: Type '"CENTER"' is not assignable to type 'Direction'.
```

