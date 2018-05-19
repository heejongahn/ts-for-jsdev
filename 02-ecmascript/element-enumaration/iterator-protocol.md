# 2.5.3 이터레이터 프로토콜

객체가 특정 조건을 만족하는 `next()` 메소드를 가질 때, 이러한 객체를 이터레이터\(반복자, Iterator\)라 부른다. 이 때, `next()` 메소드는 해당 객체의 요소들을 어떤 방식과 어떤 순서로 순회할지를 정의하며, 호출 될 때마다 아래 두 가지 값을 담은 객체를 반환해야 한다.

* 순회가 끝났는지를 나타내는 불리언 값인 `done`
* \(`done === false` 일시\) 이번 원소의 값인 `value`

아래 코드는 객체를 받아 객체의 요소를 순회하기 위한 이터레이터를 생성하고, 실제로 해당 이터레이터를 순회하는 간단한 예시다.

```javascript
function makeIterator(array) {
    var nextIndex = 0;
    return {
       next: function() {
           return nextIndex < array.length
               ? { value: array[nextIndex++], done: false } 
               : { done: true};
       }
    };
}
const iter = makeIterator([1, 2, 3]);
iter.next(); // { value: 1, done: false }
iter.next(); // { value: 2, done: false }
iter.next(); // { value: 3, done: false }
iter.next(); // { done: true }
```

