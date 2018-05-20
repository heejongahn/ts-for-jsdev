# 4.6.1 스태틱 멤버

지금까지 다룬 속성과 메소드는 클래스의 인스턴스 별로 각각 생성되고 관리되었다. 이와는 다르게 **클래스 전체에서 공유되는 값**이 필요한 경우, 스태틱 멤버\(static member\)를 사용할 수 있다. 스태틱 멤버에는 클래스 이름을 사용해 접근 할 수 있다.

### **스태틱 속성**

속성 선언 앞에 `static` 키워드를 붙여 스태틱 속성을 정의할 수 있다. 아래 예시에서는 `count` 라는 이름의 스태틱 속성을 정의하고, 접근하고 있다. 스태틱 속성이므로 `this.count` 가 아닌 `Counter.count` 와 같은 방식으로 접근함을 볼 수 있다.

```typescript
class Counter {
  static count: number = 0;
}
console.log(Counter.count); // 0
```

### **스태틱 메소드**

비슷하게, 메소드 선언 앞에 `static` 키워드를 붙여 스태틱 메소드를 정의할 수 있다.

```typescript
class Counter {
  static count: number = 0;
  static increaseCount() {
    Counter.count += 1;
  }
  static getCount() {
    return Counter.count;
  }
}
Counter.increaseCount();
console.log(Counter.getCount()); // 1
Counter.increaseCount();
console.log(Counter.getCount()); // 2
```

