# 4.6.2 접근 제어자

현재까지 다룬 모든 클래스 멤버\(생성자, 속성, 메소드 등\)는 클래스 바깥에서 자유롭게 접근 할 수 있었다. 하지만 보안적/구조적 이유로 그러한 접근을 제한하고 싶을 수 있다. 이럴 때 사용할 수 있는 것이 접근 제어자\(access modifier\)다. 접근 제어자를 사용해 인스턴스의 멤버에 대한 접근 권한을 지정할 수 있다.

{% hint style="info" %}
접근 제어자는 타입스크립트의 기능으로, ES6 클래스에는 포함되지 않는다.
{% endhint %}

### **public**

`public` 멤버는 접근 제한이 전혀 존재하지 않으며, 프로그램의 어느 곳에서나 접근 가능하다. 접근 제어자가 명시되지 않은 멤버는 모두 암시적으로 `public` 접근 권한을 갖는다. 아래의 두 정의는 의미상 동일하다.

```typescript
// implicit public member
class Triangle {
  public vertices: number;
  
  public constructor() {
    this.vertices = 3;
  }
}

// explicit public member
class Triangle {
  public vertices: number;
  
  public constructor() {
    this.vertices = 3;
  }
}
```

### **private**

`private` 멤버에는 해당 클래스 내부의 코드만이 접근 가능하다. 만약 클래스 바깥에서 `private` 멤버에 접근하려 할 시 에러가 발생한다.

```typescript
class User {
  private password: string;
  
  constructor (password: string) {
    this.password = password;
  }
}

const yoonha = new User('486');
console.log(yoonha.password); 
// error TS2341: Property 'password' is private and only accessible within class 'User'.
```

`private` 멤버의 접근 제한은 서브클래스에도 적용된다. 기본적으로 서브클래스는 슈퍼클래스의 멤버에 접근할 수 있지만, 만약 해당 멤버가 `private` 멤버라면 접근할 수 없다.

```typescript
class CarOwner extends User {
  carId: string;
  
  constructor (password: string, carId: string) {
    super(password);
    this.carId = carId;
  }
  
  setPassword(newPassword: string) {
    this.password = newPassword;
    // error TS2341: Property 'password' is private and only accessible within class 'User'.
  }
 }
```

### **protected**

`protected` 권한의 멤버는 `private`과 비슷하게 동작하지만, **서브클래스에서의 접근 또한 허용된다**는 점이 다르다. 위의 예시에서 `User`의 멤버 `password`의 접근 제어자를 `private`에서 `protected`로 변경하면 에러가 사라진다.

```typescript
class User {
  protected password: string;
  
  constructor (password: string) {
    this.password = password;
  }
}

class CarOwner extends User {
  carId: string;
  
  constructor (password: string, carId: string) {
    super(password);
    this.carId = carId;
  }
  
  setPassword(newPassword: string) {
    this.password = newPassword;
    // Okay
  }
}
```

### **생성자에서의 접근 제어자**

멤버 선언 외에도 생성자의 매개변수 앞에 접근 제어자를 명시할 수 있다. **접근 제어자가 붙은 생성자 매개변수는 같은 이름의 속성으로 선언되고, 해당 매개변수의 인자는 암묵적으로 인스턴스에 할당된다**. 즉 다음 코드는

```typescript
class User {
  constructor (public id: string, private password: string) { }
}
```

아래와 동일하게 동작한다.

```typescript
class User {
  public id: string;
  private password: string;
  
  constructor (id: string, password: string) {
    this.id = id;
    this.password = password;
  }
}
```

생성자의 인자로 받은 값을 인스턴스에 할당하는 건 실제로 매우 빈번하게 보이는 패턴이다. 생성자에서 접근 제어자를 사용함으로써 그런 패턴의 코드를 보다 간결하게 작성할 수 있다.

