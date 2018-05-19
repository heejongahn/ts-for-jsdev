# 3.3 객체

### **객체 타입**

자바스크립트의 오브젝트 리터럴을 정의하듯 중괄호\(`{}`\)를 이용해 객체 타입\(object type\)을 표현할 수 있다. 

```typescript
const user: { name: string; height: number; } = { name: '안희종', height: 176 };
```

이 때 객체 타입 정의는 오브젝트 리터럴과 다음과 같은 차이점을 갖는다.

* 콜론\(`:`\)의 우변에는 값 대신 해당 속성의 타입이 들어간다.
* 구분자로 콤마\(`,`\) 뿐만 아니라 세미콜론\(`;`\)을 사용할 수 있다.

### **선택 속성**

함수의 선택 매개변수와 비슷하게 속성명 뒤에 물음표\(`?`\)를 붙여 해당 속성이 존재하지 않을 수도 있음을 표현할 수 있다.

```typescript
const userWithUnknownHeight: { name: string; height?: number; } = { 
  name: '김수한무' 
};
```

### **읽기 전용 속성**

속성명 앞에 `readonly` 키워드를 붙여 해당 속성의 재할당을 막을 수 있다. `readonly` 키워드가 붙은 속성은 `const` 키워드를 이용한 변수의 정의와 비슷하게 동작한다.

```typescript
const user: { 
  readonly name: string; 
  height: numer; 
} = { name: '안희종', height: 176 };
user.name = '종희안'; // error TS2540: Cannot assign to 'name' because it is a constant or a read-only property.
```

