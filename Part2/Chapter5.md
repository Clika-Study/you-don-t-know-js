# Chapter5. 스코프 클로저
- 이 단원에서는 클로저에 대해 여러번 설명한다. 클로저란..하는 식으로:
  - 클로저는 렉시컬 스코프에 의존해 코드를 작성한 결과로 그냥 발생한다.
  - 클로저는 함수가 속한 렉시컬 스코프를 기억하여 함수가 렉시컬 스코프 밖에서 실행될 때에도 이 스코프에 접근할 수 있게 하는 기능을 뜻한다.
  - 클로저는 호출된 함수가 원래 선언된 렉시컬 스코프에 계속해서 접근할 수 있도록 허용한다. 어떤 방식이든 함수를 값으로 넘겨 다른 위치에서 호출하는 행위는 모두 클로저가 작용한 예다. 
### 아, 클로저는 내 코드 전반에서 이미 일어나고 있었구나! (이제 난 클로저를 볼 수 있어!)
- 클로저는 렉시컬 스코프에 의존해 코드를 작성한 결과로 그냥 발생한다. 억지로 쓰려고 할 필요도 없다
  <details>
    <summary>렉시컬 스코프?</summary>
    <div markdown="1">
    렉시컬 스코프 `Lexical Scope`란 개발자가 코드를 작성할 때 함수를 어디에 선언하는지에 따라 정의되는 스코프를 말한다.    
    </div>
  </details>
 
### 클로저는 함수가 속한 렉시컬 스코프를 기억하여 함수가 렉시컬 스코프 밖에서 실행될 때에도 이 스코프에 접근할 수 있게 하는 기능을 뜻한다

#### 예시 1. 함수를 값으로 넘겨 다른 위치에서 호출하는 일반적인 경우
```js
function foo() {
  var a = 2;
  function bar() {
    console.log(a);
  }
  return bar
}

var baz = foo();
baz(); // 2
```

- 일반적으로 `foo`가 실행된 후에는 `foo`의 내부 스코프가 사라진다고 생각할 것이다.
  - 엔진이 가비지 콜렉터를 통해 더는 사용하지 않는 메모리를 해제시킨다는 사실 때문에!
  - 더는 `foo`의 내용을 사용하지 않는다면 사라진다고 보는 게 맞다.
- 그러나 사실 `foo`의 내부 스코프는 여전히 '사용 중'이므로 해제되지 않는다.
  - 누가 그 스코프를 사용 중인가? **바로 `bar` 자신**이다
  - 선언된 위치에 따라 `bar`는 `foo` 스코프에 대한 렉시컬 스코프 클로저를 가지고, `foo`는 `bar`가 나중에 참조할 수 있도록 스코프를 살려둔다.
- 즉, `bar`는 여전히 해당 스코프에 대한 참조를 가지는데, 그 참조를 바로 클로저라고 부른다.

#### 예시 2: setTimeout
```js
function wait(message) {
  setTimeout(function timer() {
    console.log(message);
  }, 1000);
}

wait("Hello, closure!");
```
1. `timer` 함수는 `wait` 함수의 스코프에 대한 스코프 클로저를 가지고 있으므로 변수 `message`에 대한 참조를 유지하고 사용할 수 있다.
2. `wait` 실행 1초 후, `wait`의 내부 스코프는 사라져야 할 것 같지만, 익명의 함수가 여전히 해당 스코프에 대한 클로저를 가지고 있다.
  - `setTimeout` 함수에는 `fn` 또는 `func` 정도로 불릴 인자의 참조가 존재한다. 엔진은 해당 함수 참조를 호출하여 내장 함수 `timer`를 호출하므로 `timer`의 렉시컬 스코프는 여전히 남아있게 된다.

#### 예시 3: 반복문과 클로저
```js
for (var i=1; i<=5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```
- 이 코드의 목적은 1, 2, ... , 5를 하나씩 일초마다 출력하는 것이지만, 실제로 코드를 돌리면 일초마다 한번씩 6만 다섯 번 출력된다.
  - `timeout` 함수 콜백은 반복문이 끝나고서야 작동하는데, 그 시점의 `i`값이 6이기 때문이다.
- 그러면 원래 의도대로 작동하도록 하려면 어떻게 바꿔야 할까?
  - 반복문 안 총 다섯개의 함수들은 반복마다 따로 정의됐음에도 불구하고 모두 같은 글로벌 스코프를 공유해 단 하나의 `i`에 대한 참조를 공유하게 된다. 따라서 반복마다 각각의 `i` 복제본을 '잡아'두는 것이 필요하다.
  - 그러니까 필요한 것은 더 많은 **닫힌 스코프**다. 즉 반복마다 하나의 새로운 닫힌 스코프가 필요하다.
```js
// 의도대로 동작하게 하기 1: IIFE 이용하기
for (var i=1; i<=5; i++) {
  (function() {
    var j = i;
    setTimeout(function timer() {
      console.log(j);
    }, j*1000);
  })();
}

// 의도대로 동작하게 하기 2: IIFE를 이용하는 또 다른 버전
for (var i=1; i<=5; i++) {
  (function(j) {
    setTimeout(function timer() {
      console.log(j);
    }, j*1000);
  })(i);
}

// 의도대로 동작하게 하기 3: var 대신 let 사용하기
for (var i=1; i<=5; i++) {
  let j = i; // 키워드 let은 본질적으로 하나의 블록을 닫을 수 있는 스코프로 바꾼다
  setTimeout(function timer() {
    console.log(j);
  }, j*1000);
}

// 의도대로 동작하게 하기 4: var 대신 let 사용하기
// let 선언문이 for 반복문 안에서 사용되면,
// let으로 선언된 변수는 한번만 선언되는 것이 아니라 반복할 때마다 선언된다
for (let i=1; i<=5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i*1000);
}
```

### 모듈
- 모듈이란 다음과 같은 자바스크립트 패턴을 말한다.
```js
function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];

  function doSomething() {
    console.log(something);
  }

  function doAnother() {
    console.log(another.join(" ! "));
  }

  return {
    doSomething: doSomething,
    doAnother: doAnother
  }
}

var foo = CoolModule();
foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```
1. `CoolModule`은 모듈 인스턴스를 생성하려면 반드시 호출해야 한다. **최외곽 함수가 실행되지 않으면 내부 스코프와 클로저는 생성되지 않는다.**
2. `CoolModule`은 객체를 반환하는데, 이 객체는 내장 함수들에 대한 참조를 가지지만 내장 데이터 변수에 대한 참조를 가지지는 않는다. **내장 데이터 변수는 비공개로 숨겨져 있다.**

- 위의 코드는 독립된 모듈 생성자 `CoolModule`을 가지고, 생성자는 몇 번이든 호출할 수 있으며 그럴 때마다 새로운 모듈 인스턴스를 생성한다.
- 오직 하나의 인스턴스, `Singleton`만 생성하는 모듈을 만들어보자. 이를 위해 `IIFE`를 사용할 수 있다.

```js
var foo = (function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];

  function doSomething() {
    console.log(something);
  }

  function doAnother() {
    console.log(another.join(" ! "));
  }

  return {
    doSomething: doSomething,
    doAnother: doAnother
  }
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

- 공개 API 객체에 대한 내부 참조를 모듈 인스턴스 내부에 유지하면, 모듈 인스턴스를 내부에서부터 메서드와 속성을 추가 또는 삭제하거나 값을 변경하는 식으로 수정할 수 있다.

#### 미래의 모듈
- 모듈 시스템을 불러올 때 `ES6`는 파일을 개별 모듈로 처리한다.
  - 함수 기반 모듈은 런타임 전까지 해석되지 않는다. 즉 실제로 모듈의 API를 런타임에 수정할 수 있다는 말이다.
  - 반면, `ES6` 모듈 API는 정적이다. 따라서 컴파일러는 컴파일레이션 중에 불러온 모듈의 API 멤버 참조가 실제로 존재하는지 확인할 수 있다. API 참조가 존재하지 않으면, 컴파일러는 컴파일 시 초기 오류를 발생시킨다.
- 키워드 `import`는 모듈 API에서 하나 이상의 멤버를 불러와 특정 변수에 묶어 현재 스코프에 저장한다.
- 키워드 `export`는 확인자를 현재 모듈의 공개 API로 내보낸다.

### 정리
- 클로저는 함수를 렉시컬 스코프 밖에서 호출해도 함수는 자신의 렉시컬 스코프를 기억하고 접근할 수 있는 특성을 의미한다.
