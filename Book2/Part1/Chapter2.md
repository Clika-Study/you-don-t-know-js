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
 
#### 하드 바인딩
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
### 4. new 바인딩

## 4가지 규칙의 우선순위

## 바인딩 예외
### this가 무시될 때
### 더 안전한 this 사용하기
### 간접 레퍼런스
### 소프트 바인딩

## 어휘적 this
