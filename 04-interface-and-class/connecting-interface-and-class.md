---
description: 인터페이스와 클래스가 서로 어떤 방식으로 의존할 수 있는지에 대해 이야기한다.
---

# 4.7 인터페이스와 클래스의 관계

값이 가져야 하는 특정한 형태를 기술한다는 점에서 인터페이스와 클래스는 의미상으로 유사한 지점이 있다. 때문에 인터페이스 또는 클래스 확장과 유사하게 인터페이스와 클래스 사이에도 연결고리를 생성하는 방법 또한 존재한다.

### **클래스의 인터페이스 구현**

앞서 언급했듯 인터페이스는 본질적으로 값이 어떤 멤버를 반드시 가져야 하며 그 멤버들의 타입은 어때야 한다는 **제약**을 나타내는 수단이다. `implements` 키워드를 사용해 클래스가 이러한 제약을 따라야 함을 표현할 수 있다. 아래 코드를 보자.

```typescript
interface Animal {
  legs: number;
}

class Dog implements Animal { }
```

이 코드는 다음 에러를 발생시킨다.

```typescript
error TS2420: Class 'Dog' incorrectly implements interface 'Animal'.
  Property 'legs' is missing in type 'Dog'.
```

`Dog` 클래스가 따라야 할 제약인 `Animal` 인터페이스에 따르면 `legs: number` 속성이 존재해야 하는데, 그렇지 않다는 내용이다. 해당 속성을 추가해 클래스가 인터페이스를 구현하도록 변경하면 에러는 사라진다.

```typescript
interface Animal {
  legs: number;
}

class Dog implements Animal {
  legs: number = 4;
}
// Okay
```

인터페이스 구현 문법을 사용해 클래스가 특정 인터페이스를 따르도록 할 수 있다.

### **인터페이스의 클래스 확장**

앞서 클래스가 인터페이스를 구현하는 경우를 살펴보았다. 반대로, 인터페이스가 기존에 존재하는 클래스의 **형태**를 확장하는 것 또한 가능하다. 인터페이스 확장과 유사하게 `extends` 키워드를 사용해 클래스를 확장할 수 있다. 공식 문서에 있는 예제를 살펴보자.

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

const point3d: Point3d = {x: 1, y: 2, z: 3};
```

이 경우, `Point3d` 인터페이스는 자신의 `z: number` 속성 이외에도 `Point` 클래스의 멤버인 `x: number`, `y: number` 속성을 가진다.

