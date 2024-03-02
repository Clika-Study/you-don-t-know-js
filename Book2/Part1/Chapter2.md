# Chapter2. this가 이런 거로군!

## this란 호출부에서 함수를 호출할 때 바인딩된다
- 호출부는 현재 실행중인 함수 직전의 호출 코드 내부에 있다.
- 호출 스택은 현재 실행 지점에 오기까지 호출된 함수의 스택을 말한다.
- 호출 스택과 호출부를 다음 예시로 이해해보자.
  ```js
  function baz() {
    // 호출 스택: baz
    // 호출부는 전역 스택의 내부다
    console.log("baz")
    bar() -> bar의 호출부
  }

  function bar() {
    // 호출 스택: baz -> bar
    // 호출부: baz 내부다
    console.log("bar")
    foo() -> foo의 호출부
  }

  function foo() {
    // 호출 스택: baz -> bar -> foo
    // 호출부: bar 내부다
    console.log('foo')
  }

  baz() -> baz의 호출부
  ```

- 브라우저 디버거 툴을 이용하면 코드 실행에 따라 호출 스택이 차례로 쌓이는 모습과 `this` 바인딩, 변수 스코프 등을 확인할 수 있다.
 
## this가 무엇을 참조할지 결정하는 4가지 규칙 
### 1. 기본 바인딩
- 가장 평범한 함수 호출인 단독 함수 실행에 관한 규칙으로, 이 경우 `this`는 전역 객체를 참조한다.
  ```js
  function foo() {
    console.log(this.a)
  }

  // 키워드 var를 이용하여 전역 스코프에 변수 a 선언
  var a = 2 

  foo() // 2
  ```
- 엄격 모드 `strict mode`에서는 전역 객체가 기본 바인딩 대상에서 제외된다.
  ```js
  function foo() {
    "use strict"
    console.log(this.a)
  }
  
  var a = 2 
  
  foo() // TypeError: 'this' is 'undefined' 
  ```
- **단, 호출부의 엄격 모드는 상관없다**
  ```js
  function foo() {
    console.log(this.a)
  }
  
  var a = 2 
  
  (function() {
    "use strict"
    foo() // 2
  })()
  ```
### 2. 암시적 바인딩
- 호출부에 콘텍스트 객체가 있는지, 즉 객체의 소유/포함 여부를 확인하는 것이다.
  - 함수 레퍼런스에 대한 콘텍스트 객체가 존재할 때 암시적 바인딩 규칙에 따르면 바로 이 콘텍스트 객체가 함수 호출시 `this`에 바인딩 된다.  
    ```js
    function foo() {
      console.log(this.a)
    }
  
    var obj = {
      a: 2,
      foo: foo
    }
  
    obj.foo() // 2
    ```
#### 암시적 소실
- 암시적으로 바인딩 된 함수에서 바인딩이 소실되는 경우가 있다. 이럴 땐, 엄격 모드 여부에 따라 전역 객체나 `undefined` 중 한 가지로 기본 바인딩 된다.
  ```js
  function foo() {
    console.log(this.a)
  }
  
  var obj = {
    a: 2;
    foo: foo;
  }

  var bar = obj.foo
  var a = "엥, 전역이네!"
  bar() // 엥, 전역이네!
  ```
  - `bar`는 `obj`의 `foo`를 참조하는 변수처럼 보이지만, 실은 `foo`를 직접 가리키는 또 다른 레퍼런스다.
### 3. 명시적 바인딩
- 어떤 객체를 `this` 바인딩에 이용하겠다는 의지를 코드에 명확히 밝힐 방도가 없을까?
  - 이럴 때 모든 자바스크립트 함수에 사용할 수 있는 유틸리티가 바로 `apply`와 `call` 메서드다.
  - 두 메서드는 `this`에 바인딩 할 객체를 첫째 인자로 받아 함수 호출 시 이 객체를 `this`로 세팅한다. `this`를 지정한 객체로 직접 바인딩하므로 이를 명시적 바인딩이라 한다.
 
#### 하드 바인딩: Function.prototype.bind
```js
function foo() {
  console.log(this.a)
}

var obj = {
  a: 2
}

var bar = function() {
  foo.call(obj)
}

bar() // 2
bar.call(window) // 2
```
- 함수 `bar`는 내부에서 `foo.call(obj)`로 `foo`를 호출하면서 `obj`를 `this`에 강제로 바인딩하도록 하드 코딩한다.
  - 따라서 `bar`를 어떻게 호출하든 이 함수는 항상 `obj`를 바인딩하여 `foo`를 실행한다. 이런 바인딩은 명시적이고 강력해서 하드 바인딩이라고 한다.
- 하드 바인딩은 매우 자주 쓰는 패턴이어서 ES5 내장 유틸리티 `Function.prototype.bind`가 구현되어 있다.
  ```js
  function foo(something) {
    console.log(this.a, something)
    return this.a + something
  }

  var obj = {
    a: 2
  }

  var bar = foo.bind(obj)
  var b = bar(3) // 2 3
  console.log(b) // 5
  ``` 
#### API 호출 콘텍스트
- 많은 라이브러리 함수와 자바스크립트 언어 및 호스트 환경에 내장된 여러 새로운 함수는 대개 콘텍스트라 불리는 선택적인 인자를 제공한다. 이는 `bind`를 써서 콜백 함수의 `this`를 지정할 수 없는 경우를 대비한 일종의 예비책이다.
  ```js
  function foo(el) {
    console.log(el, this.id)
  }

  var obj = {
    id: "고구마"
  }

  // foo 호출 시 obj를 this로 사용한다
  [1, 2, 3].forEach(foo, obj) // 1 고구마 2 고구마 3 고구마
  ```  
### 4. new 바인딩
- (먼저 오해 바로잡기): 자바스크립트 생성자는 앞에 `new` 연산자가 있을 때 호출되는 일반 함수에 불과하다.
  - 예를 들어, 생성자 `Number` 함수에 대한 명세는 다음과 같이 쓰여 있다.
  - `new 표현식의 일부로 호출 시 Number는 생성자이며 새로 만들어진 객체를 초기화한다.`
- 함수 앞에 `new`를 붙여 생성자 호출을 하면 다음과 같은 일들이 저절로 벌어진다.
  1. 새 객체가 툭 만들어진다.
  2. 새로 생성된 객체의 `[[Prototype]]`이 연결된다.
  3. 새로 생성된 객체는 해당 함수 호출 시 `this`로 바인딩된다.
  4. 이 함수가 자신의 또 다른 객체를 반환하지 않는 한 `new`와 함께 호출된 함수는 자동으로 새로 생성된 객체를 반환한다.
- `new`는 함수 호출시 `this`를 새 객체와 바인딩하는 방법이며 이것이 `new` 바인딩이다. 

## 4가지 규칙의 우선순위
- 지금까지 함수를 호출할 때의 4가지 `this` 바인딩 규칙을 알아봤다. 만약에 여러 개의 규칙이 중복으로 해당할 땐 어떻게 할까?
  1. `new`로 함수를 호출했는가? 👉 그렇다면 새로 생성된 객체가 `this`다.
     ```js
     var bar = new foo() 
     ```
  2. `call`과 `apply`로 함수를 호출(명시적 바인딩), 이를테면 `bind`  하드 바인딩 내부에 숨겨진 형태로 호출됐는가? 👉 그렇다면 명시적으로 지정된 객체가 `this`다.
     ```js
     var bar = foo.call(obj2)
     ``` 
  3. 함수를 콘텍스트(암시적 바인딩), 즉 객체를 소유 또는 포함하는 형태로 호출했는가? 👉 그렇다면 바로 이 콘텍스트 객체가 `this`다.
     ```js
     var bar = obj1.foo()
     ```
  4. 그 외의 경우에 `this`는 기본값으로 세팅된다(기본 바인딩).
     ```js
     var bar = foo()
     ```
## 바인딩 예외
- 특정 바인딩을 의도했는데 실제로는 기본 바인딩 규칙이 예외되는 사례들이 있다.
### call, apply, bind 메서드에 첫번째 인자로 null 또는 undefined를 넘기면 this 바인딩이 무시된다.
```js
function foo() {
  console.log(this.a)
}

var a = 2
foo.call(null) // 2
```
- 그런데 `null` 같은 값으로 `this` 바인딩을 할 이유가 뭘까? `apply`는 함수 호출 시 다수의 인자를 배열 값으로 쭉 펼쳐 보내는 용도로 자주 쓰인다. `bind`도 비슷한 방법으로 인자들을 커링하는 메서드로 많이 쓰인다.
```js
function foo(a, b) {
  console.log("a: " + a + ", b: " + b)
}

foo.apply(null, [2, 3]) // a: 2, b: 3

var bar = foo.bind(null, 2)
bar(3) // a: 2, b: 3
```
- 그러나 호출 시 `null`을 전달했는데 마침 그 함수가 내부적으로 `this`를 레퍼런스로 참조하면 기본 바인딩이 적용되어 전역 변수를 참조하거나 최악으로는 예기치 못한 일이 발생할 수 있다. 
### 더 안전한 this 사용하기
- 더 안전하게 가고자 한다면 100% 빈 객체를 `null` 대신 전달할 수 있다.
  - 빈 객체는 `Object.create(null)`로 만들 수 있다. `{}`과 비슷하지만 `Object.prototype`으로 위임하지 않으므로 `{}`보다 더 텅 빈 객체라고 볼 수 있다.
    ```js
    function foo(a, b) {
      console.log("a: " + a + ", b: " + b)
    }

    var pi = Object.create(null)
    // 인자들을 배열 형태로 쭉 펼친다
    foo.apply(pi, [2, 3]) // a: 2, b: 3

    // bind로 커링한다
    var bar = foo.bind(pi, 2)
    bar(3) // a: 2, b: 3
    ```
- 기능적으로 더 안전하다는 의미 외에도 pi로 표기하면 `this`는 텅빈 객체로 하겠다는 의도를 `null`보다 더 확실하게 밝히는 효과가 있다.
### 간접 레퍼런스
### 소프트 바인딩

## 어휘적 this
