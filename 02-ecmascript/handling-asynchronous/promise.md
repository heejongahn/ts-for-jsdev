# 2.6.1 프로미스

콜백 헬의 대안으로 가장 먼저 제안된 API는 프로미스\(`Promise`\)다. 프로미스는 비동기로 처리될 수 있는 연산에 사용되며, 생성자의 인자로 `resolve`와 `reject`, 두 핸들러를 인자로 받는 한 함수를 받다. 프로그래머는 함수 본문에서 \(비동기로 작동할 수 있는\) 특정 로직을 실행하고 그 결과에 따라 `resolve` 또는 `reject`를 호출할 수 있다.

```javascript
function getRandomPromise () {
  return new Promise((resolve, reject) => {
    setTimeout(function () {
      const destiny = Math.random();
      if (destiny > 0.5) {
        resolve();
      } else {
        reject();
      }
    })
  });
}
```

### **then 과 catch 메소드**

프로미스 인스턴스는 아래 두 개의 메소드를 갖는다.

* `resolve()`의 호출된 경우, 즉 해당 비동기 작업이 완료 된 경우의 핸들러인 `then()`
* `reject()`의 호출된 경우, 즉 해당 비동기 작업이 거부된 경우의 핸들러인 `catch()`

각 핸들러는 `resolve()` 혹은 `reject()`에 넘겨진 인자를 그대로 받는다. 프로미스가 완료되거나 거부되는 것을 처리되었다\(setteled\)고 하는데, 한 프로미스는 최대 한 번만 처리 될 수 있다. 즉 이미 완료 혹은 거부가 일어난 후의 또다른 `resolve()` 또는 `reject()` 호출은 무의미하다.

아래 예시에서는 웹에서의 네트워크 요청을 위한 Fetch API의 사용을 예로 살펴본다. 모던 브라우저에 내장되어 있는 `fetch()` 함수는 네트워크 요청을 만들기 위한 여러 정보를 인자로 받아 네트워크 요청을 실행하고, 그 요청에 연결된 프로미스를 리턴한다.

네트워크 요청이 성공적으로 끝난 경우, 이 프로미스는 내부적으로 서버가 넘긴 응답을 인자로 `resolve()`를 호출한다. 이 응답은 `then()` 핸들러에서 접근 가능하다.

```javascript
fetch('http://example.com').then(response => {
  const  { ok, status } = response;
  console.log(ok, status); // true, 200
});
```

만약 네트워크 요청을 보내는 과정에서 오류가 발생했을 경우, 이 프로미스는 내부적으로 던져진 에러를 인자로 `reject()`를 호출한다. 이 응답은 `catch()` 핸들러에서 접근 가능하다.

```javascript
fetch('https://this-is-invalid-url.really').catch(err => { 
  const { message } = err;
  console.log(message); // Failed to fetch
});
```

`then` 핸들러는 실제로는 두 개의 콜백을 인자로 받는다. 그  두 번째 콜백은 에러가 발생했을 때에 실행되며 에러 객체를 인자로 받는다. 즉 위 코드는 아래와 같이 다시 쓸 수 있다.

```javascript
fetch('https://this-is-invalid-url.really').then(null, err => { 
  const { message } = err;
  console.log(message); // Failed to fetch
});
```

### **프로미스 체인**

`then`과 `catch` 두 메소드는 호출된 경우 또다시 프로미스를 반환한다. 때문에 프로미스는 연쇄될 수 있다\(chainable\). 이 때, 프로미스 체인의 다음 프로미스는 이번 프로미스가 반환한 값으로 `resolve`를 호출한다.

```javascript
fetch('http://example.com').then(response => {
  const { status } = response;
  return status;
}).then(status => {
  console.log(`The request has status ${status}!`); // The request has status 200!
});
```

종합하면, 프로미스가 지원되는 환경이라면 앞서 살펴보았던 콜백 헬 예제를 다음과 같이 고쳐 쓸 수 있다.

```javascript
function errorHandler(err) {
  if (err) {
    console.log(err);
  }
}
fetchDocument(url)
.then(document => fetchAuthor(document), errorHandler)
.then(author => fetchPostsFromAuthor(author), errorHandler)
.then(posts => /* do something with posts */, errorHandler);
```

### **정리**

콜백을 이용한 접근에 비해 갖는 장점을 금세 확인할 수 있을 것이다.

* 들여쓰기의 깊이가 단계의 수에 비례해 늘어나지 않는다.
* 코드의 흐름이 보다 한 눈에 들어온다.
* 성공한 경우의 핸들러와 에러가 발생한 경우의 핸들러 중 한 쪽만 실행된다는 것이 보장되므로 `if-else` 문을 사용하지 않고 훨씬 깔끔한 코드를 작성할 수 있다.

