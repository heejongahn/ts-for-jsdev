# 4.6.3 접근자



{% hint style="info" %}
접근자는 타입스크립트 컴파일 타겟이 ES3 이하인 경우 사용할 수 없다.
{% endhint %}

지금까지 다룬 클래스들의 모든 속성은 외부에 직접적으로 노출되었다. 많은 경우엔 이러한 접근 방식으로도 충분하지만, 속성을 읽거나 쓸 때마다 매번 특정한 로직을 실행하고 싶은 경우엔 어떻게 할까?

한 가지 방법은 접근 제어자를 사용해 직접적인 접근을 막고 읽기, 쓰기를 위한 메소드를 노출하는 것이다.

```typescript
class Shape {
  private _vertices: number = 3;
  
  getVertices() {
    console.log('Vertices getter called.');
    return this._vertices;
  }
  
  setVertices(value) {
    console.log('Vertices setter called.');
    this._vertices = value;
  }
}
```

이런 메소드를 이용한 접근법은 잘 동작하고, 논리상으로는 전혀 문제가 없다. 하지만 평소처럼 속성에 직접 접근해도\(`instance.prop`\) 내부적으로는 관련 로직이 실행되도록 정의할 수 있다면 보다 간결하고 직관적인 코드 작성이 가능할 것이다.

타입스크립트는 이를 위해 접근자\(accessor\)를 제공한다. 접근자는 이름 그대로 클래스 속성에 대해 접근할 때 실행될 로직을 정의하는 함수다. 멤버 접근은 크게 두 가지로 나뉘고, 대응하는 접근자 역시 두 가지가 존재한다.

* 읽기 접근\(`const value = instance.prop`\): 게터\(getter\)
* 쓰기 접근\(`instance.prop = value`\): 세터\(setter\)

### 읽기 접근을 위한 **게터**

속성 이름의 메소드 정의 앞에 `get` 키워드를 붙여 게터\(getter\), 즉 속성의 값을 읽어올 때 실행할 함수를 정의할 수 있다. 게터는 인자를 받을 수 없으며, 게터의 반환값은 외부에 해당 속성의 값으로 노출된다.

```typescript
class Shape {
  constructor (public vertices: number) { }
  get vertices(): number {
    console.log('Vertices getter called.');
    return 3;
  }
}
const triangle: Shape = new Shape(3);
const vertices = triangle.vertices; // Vertices getter called.
console.log(vertices); // 3
```

### 쓰기 접근을 위한 **세터**

속성 이름의 메소드 정의 앞에 `set` 키워드를 붙여 세터\(setter\), 즉 속성의 값을 쓸 때 실행될 함수를 정의할 수 있다. 세터는 새로 할당되는 값을 인자로 받는다.

```typescript
class Shape {
  private _vertices: number = 3;
  get vertices() {
    console.log('Vertices getter called.');
    return this._vertices;
  }
  set vertices(value) {
    console.log('Vertices setter called.');
    this._vertices = value;
  }
}
const square = new Shape();
square.vertices = 4; // Vertices setter called.
const vertices = square.vertices; // Vertices getter called.
console.log(vertices); // 4
```

위 `Shape` 클래스는 내부적으로만 접근 가능한 `private` 속성\(`_vertices`\)을 유지하며, 외부에서는 `vertices` 게터와 세터를 통한 접근만을 허용한다. 지금으로서는 로그를 찍는 것 외엔 별다른 의미가 없지만, 추후 유효성 검사 등의, 읽기/쓰기 시 실행될 여러 로직을 추가할 수 있을 것이다.

{% hint style="info" %}
만약 게터만 존재하고 세터가 존재하지 않는 멤버의 경우, 읽기만 가능할 뿐 쓸 수단이 없다. 따라서 타입스크립트는 해당 멤버를 `readonly` 로 인식한다.
{% endhint %}

### 

