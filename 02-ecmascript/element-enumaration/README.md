---
description: 최신 ECMAScript 명세에 추가된 원소 순회 수단들에 대해 알아본다.
---

# 2.5 원소 순회

어떤 컬렉션\(collection\)의 원소들을 순회하고 싶다는 요구사항은 굉장히 흔하다. 자연히 자바스크립트도 순회를 위한 다양한 방법을 제공한다.

예를 들어, 아래와 같은 배열의 원소를 순회하며 그 이름을 찍고 싶은 상황을 생각해보자.

```javascript
const langs = ['TypeScript', 'JavaScript', 'Python'];
```

먼저 C 스타일로 초기 조건, 조건문, 증가문을 이용해 순회하는 다음과 같은 방법이 존재한다.

```javascript
for (let i = 0; i < langs.length; i++) {
  console.log(langs[i]);
}
```

`for-in` 을 이용한 살짝 다른 버전의 반복도 가능하다.

```javascript
for (const index in langs) {
  console.log(langs[index]);
}
```

최신 ECMAScript에는 이런 고전적인 방법 말고도 순회를 위한 다양한 방법이 추가되었다. 어떤 방법이 추가되었고, 기존의 접근에 비해 어떤 강점을 갖는지 살펴보자.

