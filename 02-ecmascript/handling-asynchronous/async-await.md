# 2.6.2 Async / Await

프로미스는 콜백을 이용한 사용 패턴의 문제 중 상당 부분을 해소했지만, 완벽한 해결책은 아니었다. ECMAScript 2017에 추가된 `Async` / `Await` 문법은 비동기 코드를 마치 동기 코드처럼 쓸 수 있게 해준다.

`Async` / `Await` 을 지원하는 환경에서는 함수 선언 앞에 `async` 키워드를 덧붙여 비동기 함수\(async function\)를 정의할 수 있다. `async` 함수가 반환하는 값은 암시적으로 프로미스로 감싸진다.

```javascript
async function returnTheAnswer() {
  return 42;
}
const implicitlyReturnedPromise = returnTheAnswer();
console.log(implicitlyReturnedPromise instanceof Promise); // true
implicitlyReturnedPromise.then(answer => console.log(answer)); // 42
```

비동기 함수 내에서는 표현식 앞에 `await` 키워드를 사용할 수 있다. 이때 `await` 키워드가 붙은 해당 표현식 `expr`은 다음과 같은 값을 갖는다.

* 뒤따르는 값이 프로미스가 아닐 시, `expr`은 그 값을 그대로 갖는다.
* 뒤따르는 값이 프로미스일 시, 해당 프로미스가 처리될 때까지 실행을 중지한다. 만약 프로미스가 완료될 경우, `expr`은 `resolve()`의 인자로 사용된 값을 갖는다. 만약 그 프로미스가 거부 될 시, `expr`은 오류를 그대로 위로 던진다.

`Async` / `Await`을 이용하면 `then` 또는 `catch` 호출 없이도, 마치 동기 코드를 작성하듯 프로미스를 처리할 수 있다.

```javascript
async function asyncExample() {
  const a = await new Promise(resolve => resolve(42));
  const b = await 42;
  let c;
  try {
    c = await new Promise((_, reject) => reject('Error on await'));
  } catch (e) { 
    console.log(e);
  }
  console.log(`a: ${a}, b: ${b}, c: ${c}`);
}
asyncExample(); // Error on await
                // a: 42, b: 42, c: undefined
```

이때 이 코드는 비동기 루틴을 포함하고 있음에도 불구하고 코드가 작성된 순서대로, 위에서부터 아래로 순차적으로 실행된다. 예를 들어, 첫 번째 프로미스를 3초가 지나서 처리되도록 해보면 어떨까?

```javascript
async function asyncExample2() {
  const a = await new Promise(resolve => { setTimeout(() => resolve(42), 3000) });
  const b = await 42;
  let c;
  try {
    c = await new Promise((_, reject) => reject('Error on await'));
  } catch (e) { 
    console.log(e);
  }
  console.log(`a: ${a}, b: ${b}, c: ${c}`);
}
asyncExample2(); // Error on await
                 // a: 42, b: 42, c: undefined
```

3초가 지나야만 성공하는 첫 프로미스가 실행된 후에 코드가 진행되었음을 로그의 `a값`이 `42`로 찍힌 것으로부터 확인할 수 있다. 따라서 앞서 언급한 콜백 헬 예제 다음과 같이 작성 할 수 있게 된다.

```javascript
try {
  const document = await fetchDocument(url);
  const author = await fetchAuthor(document);
  const posts = await fetchPostsFromAuthor(author);
  /* do somethin gwith posts */
} catch (err) {
  console.log(err);
}
```

핸들러로 한 단계 더 들어가던 코드가 사라지고, 별도의 핸들러 없이 오류 처리도 `try-catch` 블록으로 처리하여 훨씬 읽기 쉬운 코드가 되었다. 이처럼 `Async` / `Await` 문법을 이용해 ‘이 작업을 먼저 진행한 후 그게 끝나면 이 다음 작업을 진행한다’라는 식의 사람의 마음 속 동작 방식을 그대로 코드로 옮길 수 있다.

