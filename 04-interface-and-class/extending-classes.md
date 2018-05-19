# 4.5 클래스 확장

인터페이스 확장과 유사하게, 클래스 역시 `extends` 키워드를 사용해 기존에 존재하는 클래스를 확장할 수 있다. 클래스 `A`가 클래스 `B`를 확장 할 때,

* `A`를 `B`의 서브클래스\(subclass\)
* `B`를 `A`의 슈퍼클래스\(superclass\)

라고 부른다. 인터페이스 확장과 마찬가지로, 서브클래스는 슈퍼클래스의 멤버\(속성, 메소드\)를 갖는다.

```typescript
class Base {
  answer: number = 42;
  greetings() {
    console.log('Hello, world!');
  }
}
class Extended extends Base { }
const extended: Extended = new Extended();
console.log(extended.answer); // 42
extended.greetings(); // Hello, world!
```

### 클래스 확장 시 **생성자**

**슈퍼클래스의 생성자는 서브클래스의 생성자에서 자동 호출되지 않는다**. 따라서 서브클래스의 생성자에선 반드시 `super` 키워드를 사용해 슈퍼클래스의 생성자를 호출해줘야 한다.

```typescript
class Base {
  baseProp: number;
  constructor() {
    this.baseProp = 123;
  }
}
class Extended extends Base {
  extendedProp: number;
  constructor() {
    super(); // 반드시 이 호출을 직접 해 주어야 한.
    this.extendedProp = 456;
  }
}
const extended: Extended = new Extended();
console.log(extended.baseProp); // 123
console.log(extended.extendedProp); // 456
```

만약 서브클래스 생성자에서 슈퍼클래스 생성자의 호출을 빠트릴 경우 에러가 발생한다.

```typescript
class ExtendedWithoutSuper extends Base {
  constructor() { }
} 
// error TS2377: Constructors for derived classesmust contain a 'super' call.
```

