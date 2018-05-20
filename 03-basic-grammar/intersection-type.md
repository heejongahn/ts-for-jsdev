---
description: 인터섹션 타입을 이용해 “여러 경우에 모두 해당”하는 타입을 표현할 수 있다.
---

# 3.8 인터섹션 타입

### **동기부여**

예를 들어, 프로그래머를 나타내는 타입과 값을 다음과 같이 정의했다고 하자.

```typescript
type Programmer = { favoriteLanguage: string };
const programmer: Programmer = { favoriteLanguage: 'TypeScript' };
```

그리고 맥주를 좋아하는 사람의 타입과 값을 다음과 같이 정의했다.

```typescript
type BeerLover = { favoriteBeer: string };
const beerLover: BeerLover = { favoriteBeer: 'Imperial Stout' };
```

그렇다면, **맥주를 좋아하는 프로그래머의 타입**은 어떻게 나타낼 수 있을까? 물론 모든 필드를 다 적어 새로운 타입을 정의하는 식의 단순한 접근도 가능하다.

```typescript
type BeerLovingProgrammer = { favoriteLanguage: string; favoriteBeer: string; };
const AhnHeejong: BeerLovingProgrammer = { 
  favoriteLanguage: 'TypeScript',
  favoriteBeer: 'Imperial Stout',
};
```

하지만 이런 접근은 코드 복사–붙여넣기와 동일하게 변화에 취약하다는 단점을 갖는다. 예를 들어 추후 `Programmer` 타입에 문자열 타입 `textEditor` 속성이 추가된다면, 프로그래머를 나타내는 모든 타입을 찾아 해당 속성을 추가해야 한다. 귀찮은 것은 차치하더라도, 이 과정에서 어디인가 빼먹을 가능성이 높다.

이런 비효율을 피하고 변화에 유연하게 대응하기 위해선 **이미 존재하는 여러 타입을 모두 만족하는 타입**을 표현하기 위한 수단이 필요하다. **인터섹션 타입**은 바로 그걸 가능케 한다. 

### **문법**

여러 타입을 앰퍼샌드\(`&`\) 기호로 이어서 인터섹션 타입을 나타낼 수 있다.

```typescript
type BeerLovingProgrammer = Programmar & BeerLover;
```

`A & B` 타입의 값은 `A` 타입에도, `B` 타입에도 할당 가능해야 한다. 만약 `A`와 `B` 모두 객체 타입이라면 `A & B` 타입의 객체는 `A`와 `B` 타입 각각에 정의된 속성 모두를 가져야 한다.

이 때 어떤 값도 만족하지 않는 인터섹션 타입이 생길 수도 있다는 점에 유의하라. 

```typescript
type Infeasible = string & number
```

문자열인 **동시에** 숫자인 값은 존재하지 않으므로, 위 `Infeasible` 타입은 실제로는 어떤 값도 가질 수 없다.

인터섹션 타입 역시 몇 개든 이어가며 정의할 수 있다.

```typescript
type Awesome = Programmer & BeerLover & CatLover;
```

### **여러 줄에 걸쳐 적은 인터섹션 타입**

인터섹션 타입을 여러 줄에 걸쳐 적을 때 유니온 타입의 그것과 동일한 내용이 적용된다. 아래 두 방식으로 정의할 수 있다.

```typescript
type BeerLovingProgrammer
  = Programmer
  & BeerLover;
  
type BeerLovingProgrammer2 =
  & Programmer
  & BeerLover;
```

