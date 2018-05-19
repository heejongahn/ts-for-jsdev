# 2.4.2 문자열 치환

매우 흔하게 요구되는 사항임에도 불구하고 이전 버전의 자바스크립트에는 문자열 포매팅을 위한 이렇다할 기능이 없었다. 때문에 문자열 포매팅을 위해선 주로 `Array.prototype.join` 메소드를 사용하거나 문자열을 더하는 방식이 사용되었다.

```javascript
const name = 'Ahn Heejong';
function greetingsWithConcat(name) {
  console.log('Hello, ' + name + '!');
}
function greetingsWithArrayJoin(name) {
  const greetings = ['Hello, ', name, '!'];
  console.log(greetings.join(''));
}
```

템플릿 리터럴의 가장 유용한 사용예 중 하나가 바로 문자열 포매팅이다. 템플릿 리터럴 문법을 사용하면 문자열의 특정 부분을 자바스크립트 값으로 런타임에 손쉽게 치환할 수 있다. 템플릿 리터럴 내에서 `${value}`로 참조된 부분은 런타임에 자바스크립트 값 `value`로 대체된다.

```javascript
function beforeES6(firstName, lastName) {
  console.log('My name is ' + firstName + ' '  + lastName +'!');
}
function sinceES6(firstName, lastName) {
  console.log(`My name is ${firstName} {lastName}!`);
}
beforeES6('Heejong', 'Ahn'); // My name is Heejong Ahn!
sinceES6('Heejong', 'Ahn'); // My name is Heejong Ahn!
```

