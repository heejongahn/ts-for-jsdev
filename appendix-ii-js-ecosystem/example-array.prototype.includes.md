# 실제 예시 - Array.prototype.includes

TC39 프로세스가 구체적으로 어떻게 진행되는지 좀 더 구체적인 감각을 갖기 위해, 실제 예시를 들어 살펴보자. 오늘의 초대 손님은 `Array.prototype.includes` 메소드다. 이 메소드는 이름이 암시하듯 배열에 어떤 원소가 들어있는지 검사한다. 

매우 흔한 요구사항인 것을 감안하면 놀랍게도 이 메소드는 2016년이 되어서야 표준 – ECMAScript 2016 – 에 추가되었다. 이 프러포절의 챔피언은 Google의 Domenic Denicola이 맡았고, [2014년 4월 9일 회의](http://tc39.github.io/tc39-notes/2014-04_apr-9.html#45-arrayprototypecontains)에서 처음 언급되었다.

### **0단계: 허수아비 \(stage 0: strawman\)**

앞서 언급된 2014년 4월 9일 회의록에서 Rick Waldron은 ES6에 추가될 예정인 String.prototype.contains 와 비슷한 메소드 Array.prototype.contains 를 배열 프로토타입에 추가하는 것이 어떻겠냐는 제안을 제시한다. 메일링 리스트에서 논의되던 이 프러포절은 이로서 0단계 프러포절이 된다.

### **1단계: 제안 \(stage 1: proposal\)**

이 프러포절은 [2014년 7월 31일 회의](http://tc39.github.io/tc39-notes/2014-07_jul-31.html)에서 다음으로 등장한다. 동작에 대한 간단한 논의 \(인자로 받는 게 배열의 원소여야 하는지 서브 배열이어야 하는지\)와 네이밍에 대한 논의 후에 1단계 승격이 결정된다. 이후 논의에서 상대적으로 간단한 제안인 만큼 프로세스를 TC39 회의 밖에서 비동기적으로 진행하고 싶다는 의견이 나오는데, Allen Wirfs-Brock와 Mark Miller에 의해 기각된다.

[1단계 프러포절로 승격된 시점의 저장소](https://github.com/tc39/Array.prototype.includes/tree/4fafe65eaa57e6da65ecbe48aa3978b199087645)를 보면, 이 프러포절이 필요한 이유와 API, 그리고 많이들 궁금해 할 법한 질문과 사용 예시 모두가 `README.md` 에 정리되어 있으며, 데모 구현 또한 저장소에 포함되어 있다. 승격에 필요한 요건의 체크리스트는 [6번 이슈](https://github.com/tc39/Array.prototype.includes/issues/6)에서 볼 수 있다.

{% hint style="info" %}
이후 이 제안은 [2014년 11월 18일 회의](http://tc39.github.io/tc39-notes/2014-11_nov-18.html#44-arrayprototypecontains-breaks-mootools)의 안건으로 올라온다. 이 안건에서는 `Array.prototype.contains` 가 표준에 포함되면 이를 구현한 브라우저에서 [Mootools](https://mootools.net/)라는 라이브러리를 사용하고 있는 기존 웹사이트가 깨질 것이라는 문제가 제기된다. 

당일 챔피언이 도착하고 [이어진 논의](http://tc39.github.io/tc39-notes/2014-11_nov-18.html#51--44-arrayprototypecontains-and-stringprototypecontains)에서 결국 `String.prototype.contains` 와 `Array.prototype.contains` 의 메소드 명을 `includes` 로 변경하는 결정이 난다. 회의 전에 이루어진 [관련 대화 쓰레드](https://esdiscuss.org/topic/having-a-non-enumerable-array-prototype-contains-may-not-be-web-compatible)를 읽어보면 설령 유용한 새 기능을 포기하더라도 \(그리고 그것이 TC39나 브라우저의 잘못으로 인한 상황이 아니더라도\) 이미 존재하는 웹을 깨트리지 않겠다는 참여자들의 의지를 엿볼 수 있어 흥미롭다.
{% endhint %}

### **2단계: 초고 \(stage 2: draft\)**

이름을 바꾸기로 결정한 논의의 이틀 후인 [2014년 11월 20일](http://tc39.github.io/tc39-notes/2014-11_nov-20.html#55-arrayprototypeincludes-proposal-to-move-to-stage-2), 이 프러포절을 2단계로 승격시키는 안건이 올라오고, 통과된다. 1단계와 마찬가지로 [2단계로 승격된 시점의 저장소](https://github.com/tc39/Array.prototype.includes/tree/6e3b78c927aeda20b9d40e81303f9d44596cd904)와 [체크리스트를 담은 이슈](https://github.com/tc39/Array.prototype.includes/issues/10)를 깃허브 저장소에서 확인할 수 있다.

저장소를 확인해보면 스펙 초안은 사실 1단계가 되는 시점에서 이미 준비되어 있다. 2단계의 또다른 요구사항인 \(예를 들어 V8에서 `--harmony-array-includes` 등의 플래그에 의해 제어되는\) 실험적인 구현이 완성된 시점에서 안건에 올라온 것으로 보인다. 앞으로 패치가 좀 더 있을 예정이므로 3단계로 올릴 수는 없다는 내용이 언급된다. 

### **3단계: 후보 \(stage 3: candidate\)**

2단계로 승격된 지 여덟 달이 지난 [2015년 7월 28일](http://tc39.github.io/tc39-notes/2015-07_july-28.html#6i-advance-arrayprototypeincludes-to-stage-3), 이 제안은 3단계로 승격된다. \([저장소](https://github.com/tc39/Array.prototype.includes/tree/5c3538772881d6efefb6c328d2c6ac0f8fe5170a), [이슈](https://github.com/tc39/Array.prototype.includes/issues/12)\)

각종 엣지 케이스들에 대한 디자인 문제를 해결한 후 명세 문서가 완전히 정리된 상태다. 또한 지명된 리뷰어와 ECMAScript 편집자의 승인도 받은 것을 볼 수 있다. 실제로 명세를 담고 있는 [spec.html 파일의 히스토리](https://github.com/tc39/Array.prototype.includes/commits/master/spec.html)를 보면 3단계로 승격된 이후에는 명세를 적은 마크업의 문법 버전 변경을 제외하면 변경 사항이 거의 전무하다.

### **4단계: 완료됨 \(stage 4: finished\)**

[2015년 11월 17일 회의](http://tc39.github.io/tc39-notes/2015-11_nov-17.html#arrayprototypeincludes)에서 Array.prototype.includes 메소드 프러포절은 최종적으로 4단계로 승격된다. 이 프러포절은 2016년 6월에 발표된 ECMAScript 2016 표준에 거듭제곱 연산자\(\*\*\)와 함께 언어의 표준 기능으로 포함되어 배포된다.

