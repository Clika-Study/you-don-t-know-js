## 5.1 [[Prototype]]

자바스크립트의 객체는 다른 객체를 참조하는 래퍼런스로 사용되는 [[Prototype]]이라는 내부 프로퍼티를 가지고 있고 거의 모든 객체가 이 프로퍼티에 null이 아닌 값이 생성될 때 같이 생성된다.

그럼 이 [[Prototype]] 레퍼런스가 어디서 사용되냐 하면 [[Get]] 을 사용할 때 프로퍼티가 존재하는 확인을 하는데 그 과정에서 프로퍼티를 찾지 못하면 그 이후 연쇄 검색 과정에서 사용이 되는것이 [[Prototype]] 레퍼런스이다. ([[Get]]이 [[Prototype]] 레퍼런스에서도 발견을 하지 못하게 되면 undefined를 반환하게 된다.)

### 5.1.1 Object.prototype

[[Prototype]] 의 연쇄가 끝나는 지점은 Object.prototype 이다.

모든 객체는 Object.prototype의 자손이기에 다수의 공용 유틸리티들이 이 객체에 포함되어 있다.

### 5.1.2 프로퍼티 세팅과 가려짐

```jsx
const myObj.foo = "bar";
```

- 프로퍼티 값을 새로 할당하거나 변경하는 과정에서 foo 가 myObj의 직속 프로퍼티가 아니라면 [[Prototype]] 연쇄를 순환하고 그래도 발견을 못하면 그때 foo 프로퍼티를 새로 할당하게된다.
- 하지만 연쇄 과정에서 foo가 상위 연쇄에 존재하게 된다면 여러개의 foo가 있을 경우 몇 상위 연쇄의 foo는 가려지게 된다 이것을 가려짐이라고 한다. (상위 가 가려지는 경우는 하위부터 검색하기 때문이다.)
- 가려지는 foo가 있는 경우 [myobj.foo](http://myobj.foo) = “bar” 할당 문을 실행시 다음 세가지 경우의 수가 따른다.
  1. [[Prototype]] 연쇄 상위 수준의 foo가 읽기 전용이 아니면 myObj에 새로운 foo 프로퍼티가 추가되어 “가려짐 프로퍼티”가 된다.
  2. [[Prototype]] 연쇄 상위 수준의 foo가 읽기 전용이면 가려짐 프로퍼티가 생성되지 않고 엄격모드에서는 에러가 난다.
  3. [[Prototype]] 연쇄 상위 수준의 foo가 세터일 경우 항상 이세터가 호출되면 가려짐 프로퍼티를 생성하지 않고 새터를 재정의 하지 않는다.
- 위의 2,3 번에서 foo를 가리려면 = 대신 Object.defineProperty() 메서드를 상용할 수 있다.

## 5.2 클래스

- 이책의 저자는 자바스크립트는 클래스가 없이 객체를 지정할 수 있어 객체지향 이란 이름표가 가장 어울리는 언어라고 한다
- 자바스크립트의 클래스는 객체의 작동을 서술하지 않는다. 객체 자신이 직접 정의하기 때문이다.

### 5.2.1 클래스 함수

```jsx
function Foo() {
  //...
}
const a = new Foo();
Object.getPrototypeOf(a) === Foo.prototype; // true
```

- 위의 코드에서 알 수 있듯이 new Foo() 코드로 새로운 객체 생성 시 두 객체의 [[Prototype]] 링크로 연결되어 있다는 것을 알 수 있다. (사실 링크가 연결되는 것은 우발적인 new Foo()읜 부수효과이다)
- 다른 언어의 클래스는 인스턴스를 생성할 때 “클래스 작동 계획을 실제 객체로 복사하는 것”이므로 인스턴스마다 복사가 일어나지만 자바스크립트는 이런 과정이 없고 클래스에서 여러 인스턴스를 생성할 수 도 없다.
- 자바스크립트에서는 객체(클래스)에서 다른 객체(인스턴스)로 복사하는 것이 아닌 연결을 하는 것이다.
- [[Prototype]] 체계를 다른말로 프로토타입 상속이라고 하는데 저자는 상속은 복사를 동반하기에 그렇지 않은 자바스크립트에서는 위임이라는 단어가 좀 더 어울린다고 한다.

### 5.2.2 생성자

위의 코드를 보면 Foo함수를 생성자 처럼 사용하고 있다. 그러면 자바스크립트의 모든 함수는 생성자일까?

아니다. 생성자 처럼 사용할 수 있었던이유는 new 때문이다.

new 는 함수 호출 시 가로채어 객체 생성이라는 부수 작업을 더한다. 따라서 new 와 함깨사용하면 생성자 라고 볼 수 있다.

### 5.2.3 체계

- Foo 함수로 a,b 인스턴스를 생성했다고 가정하고 Foo함수가 myName 이라는 프로퍼티를 가지고 있는데 a,b에서 myName에 접근하려고 하면 a,b가 인스턴스가 생성 되는 과정에서 같이 myName도 복사 했다고 생각하기 쉬운데 사실은 a,b객체의 직속 레퍼런스에 myName프로퍼티가 존재하지 않아 [[Prototype]] 레퍼런스에 링크되어 Foo.prototype에 링크된다. 따라소 myName을 위임을 통해 Foo.prototype에서 찾게 된다.
- .constructor 를 실제 기본함수를 가리킨다고 오해하기 쉬운데 사실은 Foo함수의 인스턴스 a가 있다면 기본적으로는 a.constructor === Foo 이겠지만 중간 과정에서 Foo.prototype이 변경되기 라도 하게 된다면 a.constructor === Foo 가 아니라 다른 값이 될 것이다. 만약 constructor 가 존재하지 않으면 연쇄 되어 결국엔 [[Prototype]] 연쇄의 끝인 object.constructor를 가리키게 될 것이다.

## 5.3 프로토타입 상속

- prototype을 연결하는 방법
  1. Es6 이전 - Object.create( );

     ```jsx
     // 기존 기본 Bar.prototype 을 던져 버린다.
     Bar.prototype = Object.create(Foo.prototype);
     ```

  2. Es6 이후 - Object.setPrototypeOf();

     ```jsx
     // 기존 Bar.prototype을 수정한다.
     Object.setPrototypOf(Bar.prototype, Foo.prototype);
     ```
- 그냥 할당 문으로 연결을 하게 되면 생성 자를 호출하는 과정에서 부수 효과로 예상치 못한 동작이 일어날 확률이 높다.
  ```jsx
  const Bar.prototype = new Foo();
  ```

### 5.3.1 클래스 관계조사

- 인스턴스의 상속계통을 살표보는 것을 인트로스펙션(리플렉션) 이라고 한다
- instanceof 로 주어진 객체의 계통만 살펴볼 수는 있지만 클래스와 instanceof의 의미를 혼돈하면 안된다
  ```jsx
  a instanceof F; //true or false
  ```
- isPrototypeOf로 객체의 관계를 확인할 수 있다.
  ```jsx
  b.isPrototypeOf(a); //true or false
  ```
- Object.getPrototypeOf() 나 .**proto**로 객체의 [[Prototype]]을 조회할 수 있다.
  ```jsx
  Object.getPrototypeOf(a);
  a.__proto__;
  ```
- .**proto**는 Object.prototyp에 존재하며 프로퍼티처럼 보이지만 게터 세터에 가깝다.
- 객체 [[Prototype]] 의 링크 정보는 읽기 전용으로 하는 것이 혼란을 방지할 수 있는 최선이다.

## 5.4 객체 링크

[[Prototype]]에 연결된 객체 사이의 링크를 프로토타입 연쇄라고 한다.

### 5.4.1 링크 생성

- Object.create() 새로운 객체 생성후 주어진 객체와 연결하는 것을 간단하게 도와준다.
- Es5이전 환경에서는 Object.create()가 없기에 대신 사용할 폴리필이 필요하다.

### 5.4.2 링크는 대비책

- [[Prototype]]을 프로퍼티를 못 찾았을 경우의 대비책으로 오해하기 쉬운데 그런 식으로 코드를 작성하게 된다면 유지 보수에서 상당한 어려움을 느끼게 될 것이다.
- 그러면 어디에 써야 하나? 대신 사용하는 것보다 위임을 하여 사용하는 것이 좋다.
  ```jsx
  const a = {
  	b: ()=>...,
  }
  const c = Object.create(a);
  c.d = ()=>this.b(); //위임
  c.d();
  ```
