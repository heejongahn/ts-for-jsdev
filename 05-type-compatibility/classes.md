---
description: 타입스크립트가 클래스 간의 호환성을 판단하는 법에 대해 다룬다.
---

# 5.4 클래스의 호환성

클래스의 호환성 비교는 기본적으로 객체 호환성 비교와 비슷하게 진행된다. 이 때 **스태틱 멤버 및 생성자는 호환성 비교에 영향을 주지 않는다**는 점에 주의해야 한다. 예를 들어, 다음 코드에서 이루어지는 두 할당은 두 클래스의 생성자 타입 시그니처가 다름에도 문제 없이 진행된다.

```typescript
class Animal {
  feet: number;
  constructor(name: string, numFeet: number) { }
}

class Size {
  feet: number;
  constructor(numFeet: number) { }
}

let a: Animal;
let s: Size;
a = s; // ok
s = a; // ok
```

**private 및 protected 멤버**

`public` 멤버를 비교할 때에는 객체 속성을 비교할 때와 마찬가지로 이름이 같은지, 타입이 호환 되는지만 따진다. 하지만 `private` 멤버와 `protected` 멤버는 조금 특별하게 처리된다. `private` 및 `protected` 속성은 이름이 같다고 해도 다른 클래스로부터 정의된 멤버라면 호환이 불가능하다. 

아래 예시를 보자.

```typescript
class FacebookUser {
  constructor (id: string, private password: string) {}
}

class TwitterUser {
  constructor (id: string, private password: string) {}
}

let twitterUser: TwitterUser;
let facebookUser: FacebookUser;
twitterUser = facebookUser;
```

`TwitterUser` 타입과 `FacebookUser` 타입은 모두 `private password: string` 멤버를 갖는다. 비록 이름은 같지만 이 두 속성은 서로 다른 클래스에서 정의된 `private` 멤버다. 따라서 위와 같은 할당을 시도한다면 다음 타입 에러가 발생한다.

```typescript
// error TS2322: Type 'FacebookUser' is not assignable to type 'TwitterUser'.
//   Types have separate declarations of a private property 'password'.
```

