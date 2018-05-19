---
description: >-
  1장에선 타입스크립트가 무엇인지 소개한다. 타입스크립트란 무엇인지 알아보고, 그 역사와 더불어 왜 타입스크립트를 배워야 하는지에 대한 근거를
  제시한다.
---

# 1.0 들어가며

2018년 1월 현재, 타입스크립트 홈페이지의 대문엔 다음과 같은 문장이 적혀 있다.

> 타입스크립트는 자바스크립트로 컴파일되는, 자바스크립트의 타입이 있는 상위집합입니다. _TypeScript is a typed superset of JavaScript that compiles to plain JavaScript._

이 문장에서 핵심이 되는 두 단어는 타입이 있는\(typed\) 과 상위집합\(superset\)이다.

### 타입이 있는 자바스크립트

먼저 타입이 있는 자바스크립트란 단어는 보다 정확히는 **정적 타입 시스템\(static type system\)을 도입한 자바스크립트**라는 뜻이다. 정적 타입 시스템이 있는 언어, 즉 정적 타입 언어\(statically typed language\)에서는 프로그램의 예상 동작을 타입을 통해 나타내고, 그 예상에 걸맞게 동작할 지의 여부를 타입 검사기\(type checker\)를 통해 실행 전에 확인할 수 있다. 

예를 들어 아래와 같은 자바스크립트 코드를:

```javascript
function preferTypeScript(person) {
  return person.favoriteLanguages.includes('TypeScript');
}
```

타입스크립트로는 아래와 같이 작성할 수 있다.

```typescript
type Language = 'TypeScript' | 'JavaScript' | 'Python' | 'Rust' | 'Haskell';
interface Person {
  favoriteLanguages: Array<Language>;
}
function preferTypeScript(person: Person): boolean {
  return person.favoriteLanguages.includes('TypeScript');
}
```

이 코드를 지금 당장 이해할 수 없다고 해서 당황할 필요는 없다. 기본적으로는 같은 내용이지만 함수의 인자나 리턴값 등이 만족해야 할 특정 조건에 대한 정보를 추가적으로 담고 있다는 정도로만 받아들이면 충분하다. 타입스크립트 컴파일러에 의해, 이 코드는 앞서 봤던 것과 동일한 자바스크립트 코드로 컴파일된다.

```javascript
function preferTypeScript(person) {
    return person.favoriteLanguages.includes('TypeScript');
}
```

### 자바스크립트의 상위집합

다음으로, **타입스크립트는 자바스크립트의 상위집합이다**. 정적 타입 시스템이라는, 기존 자바스크립트에 전혀 없었던 개념을 제공함에도 불구하고 타입스크립트는 자바스크립트와 완전히 동떨어진 다른 언어가 아니다. 모든 적법한 자바스크립트 코드는 적법한 타입스크립트 코드이며, 타입스크립트는 한 글자의 추가적 타이핑도 없이 자바스크립트 코드를 이해할 수 있다. 그 뿐만 아니라 거대한 자바스크립트 프로젝트에서의 점진적인 마이그레이션 또한 지원한다.

새로운 언어를 바닥부터 만드는 대신, 그 난해함으로 악명 높은 자바스크립트 코드를 포용하는 결정에는 분명 비용이 뒤따른다. 하지만 바로 그 결정으로 인해 타입스크립트 도입의 진입 장벽은 한껏 낮아졌다. 덕분에 전세계의 수많은 자바스크립트 프로젝트는 보다 적은 노력으로도 정적 타입 분석의 이점을 누릴 수 있게 되었다.

타입스크립트는 현존하는 자바스크립트의 문제를 풀기 위해 등장했고, 그 수단으로 정적 타입 분석을 내세웠다. 그렇다면 정적 타입 분석은 무엇이며, 어떤 장점을 제공할까? 정적 타입 분석을 제공하는 여러 대체재 중 타입스크립트를 사용해야 하는 이유는 무엇일까? 또 타입스크립트는 어떤 요소들로 구성되어 있으며, 어떤 역사를 가질까?

1장에선 이런 질문들에 대해 답하고자 한다.

