# CHAPTER.1 타입, 그 실체를 이해하다

## 내용

1. 자바스크립트는 **동적 언어** 이지만 그렇다고 타입 없는 것은 아니다.
2. 타입이란 자바스크립트 엔진과 개발자 모두 어떤 값을 다른 값과 분별하기 위한 고유한 내부 특성의 집합이다.
3. 자바스크립트의 내장 타입 종류
    - `null`
    - `undefined`
    - `boolean`
    - `number`
    - `string`
    - `object`
    - `symbol` (ES6부터 추가)
4. 자바스크립트에서 `typeof` 연산자로 값의 타입을 확인할 수 있다.
    - typeof 의 반환 값은 문자열이다.
    - typeof 의 반환 값은 내장 타입과 1:1로 모두 일치하지 않는다.
    - null 값

        ```jsx
        typeof null === "object"; // true
        
        ```

      null 값의 타입은 null이 맞지만 고쳐지지 않은 버그이다. 따라서 null 값을 체크하기 위해서는 null 은 falsy 한 object 값이기 때문에 null 인지 확인하기 위해서는 다음과 같이 해야 한다.

        ```jsx
        const a = null ;
        !a && typeof a === "object" //true
        
        ```

    - function 값

        ```jsx
        typeof function(){/*...*/} === "function" //true
        
        ```

      반환 값을 보면 최상위 내장 타입 같지만, 함수는 호출이 가능한 객체이기에 object의 하위 타입이다. 함수는 객체이기에 length 프로퍼티를 이용하여 인자의 개수를 알 수 있다.

    - array 값

        ```jsx
        typeof [1,2,3] === "object" //true
        ```

      다른 언어들과는 다르게 배열의 타입은 객체이고 문자 키를 가진 기본 객체와 다르게 숫자 인덱스를 가진 length 프로버티가 자동으로 관리되는 배열의 하위 타입이다.

5. 값에는 타입이 있지만 변수에는 타입이 없다. 즉 자바스크립트는 타입 강제를 하지 않는다.

   typeof 연산자는 변수의 타입을 확인하는 것이 아닌 변수의 값의 타입을 확인하는 것 이였다.

6. `undefined` 와 `undeclared` 는 다르다.

   **undefined** 는 값이 없는 것을 뜻하고 **undeclared** 는 선언이 안된 것을 뜻한다.

    ```jsx
    // undefined
    let a // 값 = undefined
    typeof a === "undefined" //true
    a; //undefined
    
    // undeclared
    // 변수 b 선언 안함
    typeof b === "undeclared" //false
    typeof b === "undefined" //true
    b; // ReferenceError: b가 정의 되지 않았습니다.
    
    ```

   위의 코드를 보면 undefined 의 typeof 연산자의 반환은 “undefined” 이지만 undeclared 의 typeof 연산자 반환 값은 “undeclared” 가 아닌 “undefined” 이다. 이점을 주의 해야 한다. 하지만 이점이 typeof 의 안전 가드 이기도 하다.

   예를 들어 변수가 선언되어있는지 안되어있는지 확인할 때 사용할 수 있다.

   하지만 선언 되지 않은 변수와 달리 객체의 프로퍼티에 접근할 때 프로퍼티가 선언이 안되어있어도 에러가 뜨지 않는다.

7. typeof 의 사용

   브라우저가 자바스크립트 코드를 처리하는 과정에서 전역 네임스페이스를 공유할 때 변수가 선언이 되었는지 검사를 해야 하는 경우가 있다. 그러한 상황에서 아래와 같이 typeof를 유용하게 사용할 수 있다.

    ```jsx
    // Test 변수 선언이 안되어있을때
    if(Test) {
    	console.log("Test Start");
    } // ReferenceError
    if(typeof Test !== "undefined"){
    	console.log("Test Start");
    } // Test Start
    ```

   하지만 typeof 로 만 검사가 가능한 것은 아니다. js 에서 선언이 되지 안은 변수는 에러가 나지만 선언이 되지 않은 객체의 프로퍼티는 에러가 나지 않기 때문에 전역 객체인 `window`를 사용하여서도 검사가 가능하다.

    ```jsx
    // Test 변수 선언이 안되어있을때
    if(Test) {
    	console.log("Test Start");
    } // ReferenceError
    if(window.Test){
    	console.log("Test Start");
    } // Test Start
    ```

   하지만 위와 같은 방법은 브라우저 환경의 자바스크립트에서만 가능하고 node.js 같은 다중 자바스크립트 환경에서는 사용할 수 없기 때문에 가급적 삼가하는 것이 좋다.

   typeof는 위와 같은 상황 외에도 특성을 활용하여 다방면에 사용이 될 수 있다.


## 추가 조사

- `Namespace`란?

   네임스페이스는 식별자에 범위를 제공하여 식별자 간의 충돌을 방지하는 프로그래밍 패러다임이다.

    ```jsx
    var car = { 
        startEngine: function () { 
            console.log("Car started");              
        }         
    } 
      
    var bike = { 
        startEngine: function () { 
            console.log("Bike started"); 
        } 
    } 
      
    car.startEngine(); 
    bike.startEngine();
    ```

  위에서 startEngine 이라는  동일한 이름의 함수가 2개 있지만 car와 bike 객체로 범위를 지정해두어 서로 충돌이 일어나지 않게 사용할 수 있다.

- `symbol`타입

    ```jsx
    const symbol = Symbol("key");
    ```

  symbol 타입은 위와 같이 `Symbol` 함수를 호출 함으로써 생성할 수 있다. 이렇게 생성된 심볼은 어디에 사용될까? 아래 예시와 같이 심볼은 일반적으로 객체의 프로퍼티 키로 사용된다.

    ```jsx
    const symbol = Symbol("key");
    const obj = {};
    obj[symbol]= "symbol";
    console.log(obj[symbol]) // symbol
    ```


## 느낀 점

javascript에 symbol이라는 타입이 있다는 것을 처음 알았는데 symbol에 관해 좀 더 찾아봐야 할 것 같고 선언 되지 않은 타입은 undeclared라고 부른다는 것을 깨달을 수 있었다. 그리고 공부를 하면서 파이썬도 자바스크립트같이 동적 언어라고 알고 있는데 파이썬의 타입은 어떤 식으로 작동하는지 찾아봐야겠다.
