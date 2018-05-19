---
description: 최신 ECMAScript에서의 객체 리터럴 변경사항에 대해 다룬다.
---

# 2.2.3 객체 리터럴 변경사항

### **트레일링 콤마 \(trailing comma\)**

ES5로부터는 객체 내에서의 트레일링 콤마 사용이 허용된다.

```javascript
const objWithTrailingComma = {
  a: 1,
  b: 1,
};
```

트레일링 콤마가 허용되면 원소의 순서를 재배열하거나 이후 새로운 원소를 추가할 때에 보다 깔끔한 변경사항을 가질 수 있다. 이는 git을 비롯한 버전 관리 시스템의 사용에 큰 도움이 된다.

### **단축 속성명 \(shorthand property name\)**

ES6부터는 이미 존재하는 어떤 변수의 변수명을 속성 이름으로, 변수의 값을 속성 값으로 사용해 객체 리터럴을 생성할 때 보다 간결한 문법을 사용 할 수 있다.

```javascript
const [a, b] = [1, 2];
const obj = { a: a, b: b };
const obj2 = { a, b }; // same as { a: a, b: b }
```

### **단축 메소드명 \(shorthand method name\)**

또한 ES6에는 객체 메소드를 정의하기 위한 보다 간결한 문법이 추가되었다.

```javascript
// old
const objWithFunction = {
  f: function () { console.log(1); }
};
objWithFunction.f(); // 1

// new (ES6~ )
const objWithFunction2 = {
  f() { console.log(1); }
};
objWithFunction2.f(); // 1
```

### **계산된 속성 이름\(computed property name\)**

마지막으로, ES6부터 계산된 속성 이름을 사용할 수 있다. 객체 리터럴에서 키를 중괄호\(`[]`\)로 감쌀 경우 해당 표현식이 계산된 값을 속성 이름으로 사용한다. 이 때 중괄호 안에는 모든 표현식이 들어갈 수 있다.

```javascript
const name = 'heejong';
const obj = { [name]: 'ahn' };
console.log(obj); // { heejong: 'ahn' }
const obj3 = { ['ab' + 'c']: 3 };
console.log(obj3); // { abc: 3 }
```

