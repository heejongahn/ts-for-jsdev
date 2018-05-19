# 3.4 타입 별칭

### **타입 별칭 정의**

타입 별칭은 다음과 같이 정의한다.

```typescript
type NewType = Type;
```

별칭을 갖게 될 타입\(위에서는 `Type`\)의 자리엔 기본 타입을 포함한 모든 타입이 올 수 있다.

```typescript
type UUID = string;
type Height = number;
type AnotherUUID = UUID;
type Animals = Animal[];
type User = {
  name: string;
  height: number;
};
```

이 때 별칭은 단순히 새로운 이름을 붙일 뿐이고, 실제로 새로운 타입이 생성되는 것은 아니라는 점에 유의하라. 예를 들어, 아래와 같은 코드의 에러 메시지에는 `UUID` 대신 `string` 이 사용된다.

```typescript
type UUID = string;
function getUser(uuid: UUID) {
  /* 함수 본문 */
}
getUser(7); // error TS2345: Argument of type '7' is not assignable to parameter of type 'string'.
```

