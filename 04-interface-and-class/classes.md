# 4.4 클래스

ES6에는 기존의 객체 지향 언어와 비슷하게 클래스를 선언할 수 있는 `class` 키워드가 추가되었다. 타입스크립트의 클래스는 ES6 클래스의 상위집합으로, ES6 클래스를 포함할 뿐 아니라 여러 추가기능을 제공한다. 가장 간단한 형태의 클래스는 다음과 같다.

```typescript
class NothingImportant {}
```

이렇게 정의된 클래스는 `new` 키워드를 이용해 인스턴스화\(instantiation\) 할 수 있다. 클래스는 인스턴스를 찍어내는 일종의 틀로 생각할 수 있다.

```typescript
const nothingImportant: NothingImportant = new NothingImportant()
```

`A`라는 클래스를 정의할 때, 이름은 같지만 의미는 다른 두 식별자가 동시에 생성된다는 점에 주의하라.

* 타입 `A`: `A` 클래스 인스턴스의 타입 \(`const a: A`\)
* 함수 `A`: `new` 키워드와 함께 호출되는 클래스 `A`의 생성자 \(`new A()`\)

예를 들어, `const a: A = new A()` 라는 코드에서 앞의 `A`는 인스턴스의 **타입**을 가리키는 반면, 뒤의 `A`는 해당 클래스의 생성자, 즉 함수 타입의 **값**이다.

### **생성자**

`constructor` 키워드를 사용해 클래스 생성자\(class constructor\)를 정의할 수 있다. 만약 어떤 클래스가 생성자를 정의하지 않았다면, 본문이 비어있는 함수가 생성자로 사용된다. \(`constructor() {}` \)객체 생성자와 유사하게, 클래스 생성자를 통해 클래스 인스턴스가 생성될 때 실행될 로직을 정의할 수 있다.

```typescript
class Dog {
  constructor() {
    console.log('constructing!');
  }
}
const dog: Dog = new Dog(); // constructing!
```

생성자는 임의의 매개변수를 취할 수 있다. 이 변수들은 클래스를 인스턴스화 할 때 인자로 넘겨진다. 예를 들어, 아래에서는 생성자의 `barkingSound` 매개변수에 들어갈 값으로 인자 `'월'` 을 넘기고 있다.

```typescript
class BarkingDog {
  constructor(barkingSound: string) {
    console.log(`${barkingSound}!`);
  }
}
const barkingDog: BarkingDog = new BarkingDog('월');
barkingDog.bark(); // 월!
```

생성자의 함수 시그니쳐와 맞지 않는 타입의 인스턴스화를 시도할 시 에러가 발생한다.

```typescript
const err: BarkingDog = new BarkingDog(); 
// error TS2554: Expected 1 arguments, but got 0.

const err2: BarkingDog = new BarkingDog(3); 
// error TS2345: Argument of type '3' is not assignable to parameter of type 'string'.
```

### **속성**

객체 속성과 유사하게 클래스 인스턴스도 속성\(`property`\)를 가질 수 있다. 클래스 내에서는 속성엔 `this` 키워드를 이용해 접근 가능하다. 모든 클래스 속성은 `이름: 타입` 꼴의 속성 선언\(property declaration\)을 통해 타입을 표기해주어야 한다.

```typescript
class Triangle {
  // 속성 선언
  vertices: number;
  constructor() {
    // 속성 할당
    this.vertices = 3;
  }
}
const triangle: Triangle = new Triangle();
console.log(triangle.vertices); // 3
```

{% hint style="info" %}
**--strictPropertyInitialization 옵션**

앞서 3장의 첫 부분에서 우리는 `--strict` 컴파일러 옵션이 켜진 환경을 가정한다 언급한 적 있다. `--strict` 옵션은 `--strictPropertyInitialization` 옵션을 포함한다. 이 옵션이 켜진 환경에선 `undefined` 를 포함하지 않는 클래스 속성이 반드시 **속성 선언** 또는 **생성자**, 두 장소 중 한 곳에서 초기화 되어야 한다. 

생성자와 속성 선언 두 곳 모두에서 초기화되지 않는 경우 속성 값이 `undefined`일 가능성이 존재한다. 따라서 타입스크립트는 그 정보가 타입에 포함되어 있지 않다면 타입 에러를 낸다. 다음 예시를 보자.

```typescript
class User {
  private password: string;
}
// error TS2564: Property 'password' has no initializer and is not definitely assigned in the constructor.
```

`password`는 속성 선언에서도, 생성자에서도 초기화되지 않고 있으므로 접근되는 시점에 `undefined` 일 수 있다. 이 값은 `string` 타입이 아니므로 타입 에러가 발생하는 것이 합당하다. 이 에러를 없애기 위해선 두 가지 방법이 존재한다.

* 실제 상황을 정확히 반영하기 위해 `password`을 선택 속성으로 선언한다.
  * 즉 `password: string` 를 `password?: string` \(또는 `password: string | undefined`\) 로 변경한다.
* `password`가 `undefined`여서 문제가 생길 일이 없다고 확신한다면, 속성 이름 뒤에 느낌표\(`!`\)를 붙여 확정적 할당 단언\(definitive assignment assertion\)을 제공할 수 있다.
  * 즉 `password: T`를 `password!: T`로 변경한다. 이 경우 컴파일러는 `password`의 초기화 체크를 건너뛴다.
{% endhint %}

### **속성 기본값**

함수의 기본 인자와 유사하게 클래스 속성에도 기본값을 제공할 수 있다.

```typescript
class Triangle {
  vertices: number = 3;
}
const triangle: Triangle = new Triangle();
console.log(triangle.vertices); // 3
```

### **읽기 전용 속성**

`readonly` 키워드를 사용해 읽기 전용 속성을 정의할 수 있다. **속성 선언 또는 생성자 외의 장소에서는 읽기 전용 속성에 값을 할당 할 수 없다**.

```typescript
class Triangle {
  readonly vertices: number;
  constructor() {
    this.vertices = 3;
  }
}
const triangle: Triangle = new Triangle();
triangle.vertices = 4;
// error TS2540: Cannot assign to 'vertices' because it is a constant or a read-only property.
```

이 때, 읽기 전용 속성은 `--strictPropertyInitialization` 옵션 여부와 **무관하게** 반드시 속성 선언 또는 생성자에서 초기화되어야 한다. 읽기 전용이라는 정의 자체에 의해 그 두 곳 이외에선 할당 자체가 불가능하기 때문이다. 만약 둘 중 어느 곳에서도 초기화 되지 않았다면 에러가 발생한다.

```typescript
class Triangle {
  vertices: number;
  readonly angles: number[];
  constructor() {
    this.vertices = 3;
  }
}
// error TS2564: Property 'angles' has no initializer and is not definitely assigned in the constructor.
```

### **메소드**

객체의 단축 메소드명과 유사한 문법을 사용해 인스턴스의 메소드를 정의할 수 있다. 메소드 내에서는 `this` 키워드를 사용해 해당 메소드가 호출되는 인스턴스를 참조할 수 있다. 예를 들어, 메소드를 사용해 위의 예제를 아래와 같이 변경할 수 있다.

```typescript
class BarkingDog {
  barkingSound: string;
  
  constructor(barkingSound: string) {
    this.barkingSound = barkingSound;
  }
  
  bark(): void {
    console.log(`${this.barkingSound}!`);
  }
}
const barkingDog: BarkingDog = new BarkingDog('월');
barkingDog.bark(); // 월!
```

위에서처럼 함수를 속성으로 정의한 경우엔 별도의 속성 선언이 필요하지만, 메소드 문법을 사용할 경우 속성 선언을 생략할 수 있다.

