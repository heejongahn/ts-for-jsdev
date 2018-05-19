# 3.5 함수

### **함수의 타입**

함수의 타입을 결정하기 위해서는 다음 두 가지 정보가 필요하다.

* 매개변수\(parameter\)의 타입
* 반환값\(return value\)의 타입 \(반환 타입\)

매개변수의 경우, 변수의 타입을 표기할 때와 마찬가지로 매개변수 뒤에 콜론\(`:`\)을 붙이고 타입을 적는다. \(`param1: number`\)

반환 타입은 매개변수 목록을 닫는 괄호\(`)`\)와 함수 본문을 여는 여는 대괄호\(`{`\) 사이에 콜론을 붙이고 표기한다. \(`function (): number { ... }`\)

예를 들어 **두 숫자를 받아** 그 **합을 반환**하는 함수는 다음과 같이 타입 표기한다.

```typescript
function sum(a: number, b: number): number {
  return (a + b);
}
```

만약 함수가 아무런 값도 반환하지 않고 종료된다면 반환 타입으로 `void` 를 사용한다.

```typescript
function logGreetings(name: string): void {
  console.log(`Hello, ${name}!`);
}
```

`void` 반환 타입을 갖는 함수가 `undefined`나 `null` 이외의 값을 반환하면 타입 에러가 발생한다. `void`가 아닌 반환 타입을 갖는 함수가 아무 값도 반환하지 않는 경우도 마찬가지다.

```typescript
function notReallyVoid(): void {
  return 1;
}
// error TS2322: Type '1' is not assignable to type 'void'.

function actuallyVoid(): number { }
// error TS2355: A function whose declared type is neither 'void' nor 'any' must return a value.
```

### **함수 값의 타입 표기**

함수 타입의 값에 타입 표기를 붙이기 위해서는 화살표 함수 정의 문법과 비슷한 문법을 사용한다.

```typescript
(...매개변수 나열) => 반환 타
```

매개변수가 없는 함수의 경우 매개변수를 생략해 아래와 같이 적는다.

```typescript
() => 반환 타입
```

예시를 들어보면 아래와 같다. 화살표 함수 문법을 사용한 함수 또한 비슷하게 정의 가능하다.

```typescript
const yetAnotherSum: (a: number, b: number) => number = sum;
const onePlusOne: () => number = () => 2;
const arrowSum: (a: number, b: number) => number = (a, b) => (a + b);
```

타입 별칭 또한 사용 가능하다.

```typescript
type SumFunction = (a: number, b: number) => number;
const definitelySum: SumFunction = (a, b) => (a + b);
```

### **기본 매개변수**

ES6와 마찬가지로, 타입스크립트에서도 기본 매개변수 문법을 사용할 수 있다. 이 때 기본값은 

```typescript
매개변수명: 타입 = 기본값
```

의 형태로 표기한다.

```typescript
function greetings(name: string = 'stranger'): void {
  console.log(`Hello, ${name}`);
}
greetings('Heejong'); // Hello, Heejong!
greetings(); // Hello, stranger!
```

### **선택 매개변수**

많은 프로그래밍 언어는 함수 정의에 명시된 매개변수의 수보다 많거나 적은 수의 인자가 들어온 경우 에러를 뱉는다. 한편, 자바스크립트는 더 들어온 인자는 버리고, 덜 들어온 인자는 `undefined`가 들어온 것과 동일하게 취급한 후 어떻게든 함수를 실행하려 시도한다.

이런 언어의 특성 및 기존 사용례를 포용하면서 타입 안정성을 확보하기 위해 타입스크립트는 **선택 매개변수**를 지원한다. 함수의 매개변수 이름 뒤에 물음표\(`?`\) 기호를 붙여 해당 매개변수가 생략 될 수 있음을 명시할 수 있다. 예를 들어, `optional?: number` 로 선언된 선택 매개변수 `optional`를 함수 본문에서 `number` 타입 값으로 사용하려면 해당 값이 `undefined`가 아닌지를 먼저 검사해야 한다.

```typescript
function fetchVideo(url: string, subtitleLanguage?: string) {
  const option = { url };
  if (subtitleLanguage) {
    option.subtitleLanguage = true;
  }
  /* ... */
}
fetchVideo('https://example.com', 'ko'); // okay
fetchVideo('https://example.com'); // also okay
```

이 때 매개변수 정의 순서에서 선택 매개변수 이후에 필수 매개변수를 두는 것은 허용되지 않는다.

```typescript
function invalidFetchVideo(subtitleUrl?: string, url: string) {
  /* ... */
}
//error TS1016: A required parameter cannot follow an optional parameter.
```

이러한 제약이 존재하는 이유는 만약 필수 매개변수가 선택 매개변수 뒤에 있을 시, 인자가 어떤 매개변수의 값인지 구분할 수 없기 때문이다. 예를 들어 위의 두 함수를 아래와 같이 호출하는 경우를 생각해보자.

```typescript
fetchVideo('https://example.com');
invalidFetchVideo('https://example.com');
```

이 때 첫 번째 호출의 경우 인자가 `url` 매개변수의 값이라는 것이 명백하다. 한편 두 번째 호출에서는 `'https://example.com'` 이라는 값이 선택매개변수인 `subtitleUrl`의 값으로 쓰인건지, 또는 `url`의 값으로 쓰인 건지 모호하다. 따라서 타입스크립트는 이런 식의 함수 정의를 만나면 오류를 발생시킨다.

### **함수 오버로딩**

자바스크립트에서는 한 함수가 여러 쌍의 매개변수-반환 타입 쌍을 갖는 경우가 매우 흔하다. 이런 함수의 타입을 정의할 수 있게 하고자 타입스크립트는 함수 오버로딩\(function overloading\)을 지원한다.

타입스크립트의 함수 오버로딩은 다음과 같은 특징을 갖는다.

* 함수는 **하나 이상의 타입 시그니처**를 가질 수 있다.
* 함수는 **단 하나의 구현**을 가질 수 있다.

즉, 오버로딩을 통해서 여러 형태의 함수 타입을 정의할 수 있지만, 실제 구현은 한 번만 가능하므로 여러 경우에 대한 분기는 함수 본문 내에서 이루어져야 한다.

예를 들어 다음 함수들을 보자.

```typescript
function doubleString(str: string): string {
    return `${str}${str}`;
}
function doubleNumber(num: number): number {
    return (num * 2);
}
function doubleBooleanArray(arr: boolean[]): boolean[] {
    return arr.concat(arr);
}
```

이 함수들은 각각 문자열, 숫자, 그리고 불리언의 배열을 받아 두 배로 만드는 함수다. 이 때, ‘두 배’가 의미하는 건 타입에 따라 다르고, 세 함수는 인풋 타입에 따라 다른 타입의 값을 반환한다. 이 세 함수를 함수 오버로딩을 사용해서 하나의 `double` 이라는 함수로 합쳐보자.

먼저 각 경우의 타입 시그니쳐를 정의한다. 타입 시그니쳐는 함수 정의와 동일하되, 본문이 생략된 형태다.

```typescript
function double(str: string): string;
function double(num: number): number;
function double(arr: boolean[]): boolean[];
```

그 후엔 함수의 본문을 구현한다.

```typescript
function double(arg) {
    if (typeof arg === 'string') {
        return `${arg}${arg}`;
    } else if (typeof arg === 'number') {
        return arg * 2;
    } else if (Array.isArray(arg)) {
        return arg.concat(arg);
    }
}
```

이렇게 오버로딩을 통해 정의된 `double` 함수는 호출하는 인자의 타입에 따라 반환 타입이 달라진다.

```typescript
const num = double(3); // number
const str = double('ab'); // string
const arr = double([true, false]); // boolean[]
```

### **This 타입**

앞서 2장에서 언급했지만, 자바스크립트 함수 내부에서의 `this` 값은 함수가 정의되는 시점이 아닌 **실행되는 시점**에 결정된다. 이런 특성은 함수 내부에서 `this` 의 타입을 추론하는 일을 매우 어렵게 만든다. 타입스크립트는 이런 어려움을 해결하기 위해 함수 내에서의 `this` 타입을 명시할 수 있는 수단을 제공한다.

함수의 `this` 타입을 명시하기 위해선 함수의 타입 시그니쳐에서 매개변수 가장 앞에 `this` 를 추가해야 한다. 이 때 `this` 타입은 타입 시스템을 위해서만 존재하는 일종의 가짜 타입이다. 즉, `this` 매개변수를 추가한다고 해도 함수가 받는 인자 수와 같은 실제 동작은 변하지 않는다.

```typescript
interface HTMLElement {
  tagName: string;
  /* ... */
}
interface Handler {
  (this: HTMLElement, event: Event, callback: () => void): void;
}
let cb: any;
// 실제 함수 매개변수에는 this가 나타나지 않음
const onClick: Handler = function(event, cb) {
  // this는 HTMLElement 타입
  console.log(this.tagName);
  cb();
}
```

만약 `this`의 타입을 `void`로 명시한다면 함수 내부에서 `this`에 접근하는 일 자체를 막을 수 있다. 

```typescript
interface NoThis {
  (this: void): void;
}
const noThis: NoThis = function() {
  console.log(this.a); // Property 'a' does not exist on type 'void'.
}
```

