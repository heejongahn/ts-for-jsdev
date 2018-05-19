---
description: 템플릿 리터럴을 이용해 여러 줄에 걸친 문자열을 손쉽게 표현할 수 있다.
---

# 2.4.1 멀티라인 문자열

이전 버전의 자바스크립트는 멀티라인 문자열을 손쉽게 생성할 수 있는 수단을 제공하지 않았다. 따라서 프로그래머가 직접 문자열을 더하기 연산자를 이용해 연결하거나, 배열의 `Array.prototype.join` 함수 등을 이용하는 식의 접근이 필요했다.

템플릿 리터럴은 여러 라인에 걸쳐 정의할 수 있으며, 해당 문자열은 문자열 내의 공백 및 줄바꿈을 모두 보존한다. 

```javascript
const a = `
First line
Second line
`;
console.log(a); // First line
                // Second line
```

이 때 **공백도 보존된다**는 점에 주의해야 한다. 들여쓰기를 맞추기 위해 문자열 내에서 공백을 넣을 시 그 공백은 문자열에 포함된다.

```javascript
const a = `First line
           Second line`;
console.log(a); // First line
                //            Second line
```

