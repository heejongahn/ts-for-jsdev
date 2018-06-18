---
description: 유니온 타입의 특수한 경우인 서로소 유니온 타입의 의미와 쓸모에 대해 다룬다.
---

# 6.5 서로소 유니온 타입

앞에서 타입을 집합으로 바라보는 관점을, 또 그러한 관점에서 유니온 타입은 합집합을 표현하는 수단임을 배웠다. 이번엔 그러한 유니온 타입을 사용할 때 유용한 패턴에 대해 다루어 보자.

### 겹치지 않는 타입으로 이루어진 유니온 타입

```typescript
interface Employee {
  department: string;
  salary: number;
}
interface Boss {
  kind: boolean;  
}
type CompanyMember = Employee | Boss;
```

회사의 멤버는 직원 또는 사장이라고 정의했다. 이 때 유니온 타입을 구성하는 `Employee`와 `Boss` 각각을 브랜치\(branch\)라 부른다. 다음 객체를 보자. 이 객체는 어떤 브랜치에 속하는가?

```typescript
const whoAmI: CompanyMember = {
  kind: true,
  salary: 777,
  department; 'everywhere',
};
```

정답은 ‘둘 다’다.타입스크립트는 구조적 타입 시스템을 가지므로, `whoAmI` 객체는 `Employee` 타입으로 볼 수도 있고 `Boss` 타입으로 볼 수도 있다. 다르게 표현하면, `whoAmI`는 `Employee` 집합과 `Boss` 집합의 교집합에 속한다.

그렇다면 만약 `Employee`와 `Boss` 타입을 다음처럼 정의한다면 어떻게 될까? 각각의 브랜치, 즉 유니온 타입을 구성하는 타입마다 고유한 리터럴 타입 속성을 추가하는 것이다.

```typescript
interface Employee {
  type: 'Employee';
  department: string;
  salary: number;
}

interface Boss {
  type: 'Boss';
  kind: boolean;  
}

interface CompanyMember = Employee | Boss;
```

위 정의에서 두 인터페이스는 서로 다른 리터럴 타입의 `type` 속성을 갖는다. `'Boss'` 타입이면서 `'Employee'` 타입인 문자열은 없다. 따라서 동시에 `Employee` 타입이면서 `Boss` 타입인 값은 존재할 수 없다. 즉 `Employee` 집합과 `Boss` 집합의 교집합은 공집합이다.

```typescript
type EmployeeAndBoss = Employee & Boss; // 불가능하다. 따라서 never 타입.
```

이렇게 **브랜치 간에 겹치는 부분이 없는** 유니온 타입을 **서로소 유니온 타입**\(disjoint union type\)이라 부른다. “서로소”는 교집합이 없는 집합 사이의 관계를 의미하는 “서로소 집합”에서 따온 용어다.

{% hint style="info" %}
이러한 타입은 ‘서로소 유니온 타입’ 이외에도 여러가지 다른 이름을 갖고 있다.

먼저 위의 `type` 속성처럼, 특정 속성을 통해 값이 속하는 브랜치를 식별할 수 있다는 이유로 **식별 가능한 유니온**\(discriminated union type\)또는 **태그된 유니온**\(tagged union\)이라는 이름을 갖는다. 브랜치를 식별하기 위해 쓰이는 `type` 속성은 식별자\(discriminator\) 또는 태그\(tag\)라 불린다.

서로소 유니온 타입의 또 다른 이름으로 **합 타입**\(sum type\)이 있다. 다음 코드를 보자. `Bool` 타입은 2개의 값, `Num` 타입은 3개의 값을 갖는다.

```typescript
type Bool = true | false;
type Num = 1 | 2 | 3;
```

이 때 아래와 같이 정의한 서로소 유니온 타입 `SumType`은 몇 개의 값을 가질까?

```typescript
type SumType = { type: 'bool', value: Bool } | { type: 'num', value: Num }; 
```

두 브랜치에 동시에 속하는 값이 없으므로 `SumType`은 2 + 3 = 5 개의 값을 갖는다. 합 타입이라는 이름은 이렇듯 각 브랜치가 갖는 값의 수를 합친 만큼의 값을 갖는 타입이라는 데에서 유래했다.
{% endhint %}

서로소 유니온 타입 패턴을 사용해 **서로 겹치지 않는** 여러 경우 중 하나인 타입을 표현할 수 있다. 이 패턴은 매우 유용한데, 실제로 프로그래밍을 하면서 마주치는 많은 상황이 이러한 경우에 해당하기 때문이다.

* 네트워크 요청: 요청은 성공하거나 실패하겠지만, 성공한 동시에 실패할 수는 없다.
* 데이터베이스에서 특정 아이디를 갖는 컬럼을 쿼리: 해당 컬럼이 있거나 없지만, 있으면서 없을 수는 없다.
* 어떤 파일명을 갖는 파일 내용을 읽어오는 경우: 해당 파일이 있거나 없지만, 있으면서 없을 수는 없다.

그 외에도 여러가지 선택지 중 하나의 결과를 갖는 대부분의 상황을 서로소 유니온 타입으로 표현할 수 있다.

### **switch를 이용한 패턴 매칭**

서로소 유니온 타입의 브랜치 사이에 겹치는 값이 없다. 이 성질과 패턴 매칭을 사용하면 간결하고 직관적인 코드를 짤 수 있다. 이런 장점 때문에 하스켈, 스칼라, 러스트 등의 함수형 프로그래밍 언어는 서로소 유니온 타입과 패턴 매칭을 매우 적극적으로 사용한다.

아쉽게도 타입스크립트는 언어 차원에서 패턴 매칭 문법을 제공하지 않는다. 하지만 `switch`-`case` 제어 구조와 타입 좁히기의 힘을 빌려 비슷한 효과를 흉내낼 수 있다. `kind` 속성을 식별자로 갖는 다음 서로소 유니온 타입을 생각해보자.

```typescript
interface Square {
  kind: "square";
  size: number;
}
interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}
interface Circle {
  kind: "circle";
  radius: number;
}
type Shape = Square | Rectangle | Circle;
```

이 때 `Shape` 타입의 값을 받아 면적을 반환하는 함수를 다음과 같이 구현할 수 있다.

```typescript
function area(s: Shape): number {
  switch (s.kind) {
    case "square": {
      // s의 타입 Square로 좁혀짐
      return s.size * s.size;
    }
    case "rectangle": {
      // s의 타입 Rectangle로 좁혀짐
      return s.height * s.width;
    }
    case "circle": {
      // s의 타입 Circle로 좁혀짐
      return Math.PI * s.radius ** 2;
    }
  }
}
```

타입스크립트의 타입 시스템은 `s.kind`를 식별자로 사용해 각각의 `case` 문 내에서 `s`의 타입을 성공적으로 좁혀낼 수 있다. 또한, 세 가지 `case`문을 통해 가능한 모든 경우를 이미 처리했음을 알고 있으므로 `default` 브랜치가 없이도 이 함수의 반환 타입이 `number`라는 사실을 이해한다. 

다음과 같이 프로그래머가 실수로 한 브랜치를 처리하지 않는 경우를 생각해보자. 

```typescript
function incompleteArea(s: Shape): number {
  switch (s.kind) {
    case "square": {
      // s의 타입 Square로 좁혀짐
      return s.size * s.size;
    }
    case "rectangle": {
      // s의 타입 Rectangle로 좁혀짐
      return s.height * s.width;
    }
  }
}
```

위 코드는 다음 에러를 발생시킨다. `number` 타입을 반환해야 하는데 `undefined` 를 반환하는\(즉 반환문이 없는\) 경우가 있으므로 무언가 잘못되었음을 알려주는 것이다.

```typescript
error TS2366: Function lacks ending return statement and return type does not include 'undefined'.
```

그 뿐만이 아니다. 타입스크립트는 실수로 식별자 이름에 오타를 내는 경우에도 그런 식별자는 존재하지 않는다는 사실을 지적해 줄 수 있다.

```typescript
function areaWithTypo(s: Shape): number {
  switch (s.kind) {
    case "square": {
      // s의 타입 Square로 좁혀짐
      return s.size * s.size;
    }
    case "rectangl": {
      // 오타 발생
      return s.height * s.width;
    }
    case "circle": {
      // s의 타입 Circle로 좁혀짐
      return Math.PI * s.radius ** 2;
    }
  }
}
// error TS2678: Type '"rectangl"' is not comparable to type '"square" | "circle" | "rectangle"'.
```

{% hint style="info" %}
이런 식으로 타입스크립트가 모든 브랜치가 올바르게 처리되었는지 수행하는 검사를 완전함 검사\(exhaustiveness check\)라 부른다.
{% endhint %}

이렇듯, 서로소 유니온 타입과 `switch`-`case`를 사용해 언어가 지원하지 않는 패턴매칭을 흉내 낼 수 있다.  실제로 이 패턴은 redux를 비롯해 여러 인기 있는 라이브러리에서 쓰인다.

{% hint style="info" %}
사실 [자바스크립트에 패턴 매칭을 도입하려는 프로포절](https://github.com/tc39/proposal-pattern-matching)이 존재한다. 2018년 3월 현재 아직까지는 stage 0에 머물러 있지만, 만약 이 프로포절이 살아남는다면 몇 년 후에는 자바스크립트와 타입스크립트에서도 언어 차원에서 지원되는 패턴 매칭을 사용할 수 있을지 모른다.
{% endhint %}

