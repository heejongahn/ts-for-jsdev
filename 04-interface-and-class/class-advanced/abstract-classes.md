# 4.6.4 추상 클래스

`class` 키워드 대신 `abstract class` 키워드를 사용해 추상 클래스를 선언할 수 있다. 일반 클래스는 `extends` 키워드를 사용해 추상 클래스를 확장할 수 있다. 추상 클래스는 **인스턴스화가 불가능하다**는 점에서 일반 클래스와 다르다. 또한 추상 클래스는 **구현을 일부 포함할 수 있다**는 점에서 인터페이스와 다르다. 공식 문서의 예제를 보자.

```typescript
abstract class Animal {
    move(): void {
        console.log("roaming the earth...");
    }
    abstract makeSound(): void;
}
```

`abstract class` 키워드를 이용해 정의된 추상 클래스 `Animal`은 두 멤버를 갖는다. 먼저 `move` 메소드는 일반 클래스 메소드와 동일하다. 한 편, `abstract` 키워드가 앞에 붙어 있는 `makeSound` 는 **가상 메소드**\(`abstract method`\)로, 타입 정보 이외의 실제 구현은 포함하지 않고 있다.

가상 클래스를 확장하는 서브 클래스는 슈퍼 클래스의 모든 가상 메소드를 구현해야 한다. 만약 이 예시에서 `Animal`을 확장하는 일반 클래스가 `makeSound` 메소드를 올바르게 구현하지 않는다면 타입 에러가 발생한다.

```typescript
class Dog extends Animal { }
// error TS2515: Non-abstract class 'Dog' does not implement inherited abstract member 'makeSound' from class 'Animal'.
```

