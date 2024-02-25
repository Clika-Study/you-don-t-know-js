## 네이티브

- 네이티브는 특정 환경에 종속되지 않은 ECMAScripte의 내장 함이다.
- 많이 사용되는 네이티브들
  - String ( )
  - Number ( )
  - Boolean ( )
  - Array ( )
  - Object ( )
  - Function ( )
  - RegExp ( )
  - Date ( )
  - Error ( )
- Symbol ( ) - ES6에서 추가
- 네이티브는 생성자처럼 사용할 수 있지만 결과는 원시 값을 감싼 객체 래퍼이다.

    ```jsx
    const a = new String(a) ; 
    typeof a ; // "object" string이 아님
    ```


## 내부  [[Class]]

- typeof 가 “object”인 값에는 [[Class]]라는 내부 프로퍼티가 있다.
- 직접 접근이 안 되기에 Object.prototype.toString () 매서드로 존재를 엿볼 수 있다.
- 대부분 [[Class]] 는 해당 값의 네이티브 생성자를 가리키지만, 아닐 때도 있다.
- null , undefined는 생성자는 없지만 내부 [[Class]] 값은 “Null” , “Undefined”이다.
- 변수 선언 시 단순 원시 값은 해당 객체 래퍼로 자동 박싱된다 (Ex 문자열, 숫자 불리언 … )

## 객체 래퍼

- 원시 값에는 length 나 toString 과 같은 매서드가 없기 때문에 객체 래퍼로 감싸 주어야 한다.
- 자바스크립트는 원시 값을 알아서 박싱을 해주기에 우리가 직접 박싱을 해줄 필요가 없다.
- 우리가 직접 박싱하는 경우는 거의 없고 권장되는 방법이 아니다. 그 이유는 자바스크립트는 스스로 최적화하기에 개발자가 선 최적화를 하게 되면 오히려 더 느려질 수도 있기 때문이다.
- 불리언 값을 래핑 후 조건문을 사용할 때 감싼 객체가 “truthy” 이기 때문에 원시 값과는 상관없는 동작이 발생할 수 있다.

    ```jsx
    const a = new Bolean(false);
    if (a) console.log("성공"); // 성공 
    ```

- null , undefined는 생성자는 없지만 내부
- 수동으로 원시 값을 박싱하려면 Object() 함수를 이용하자 (new는 붙일 필요가 없다

    ```jsx
    const a = "abc";
    const b = new String(a);
    const c = Object(a);
    
    typeof a //string
    typeof b //object
    typeof c //object
    
    b instanceof String // true
    c instanceof String // true
    
    Object.prototype.toString.call(b); // "[object String]" 
    Object.prototype.toString.call(c); // "[object String]"
    ```

- valueOf () 매소드를 사용하여 언박싱을 할 수 있다 (원시 값을 얻을 수 있음 )

    ```jsx
    const a = new String("abc");
    a.valueOf() ; //"abc"
    ```


## Array

- Array 생성자에서 인자로 숫자 하나를 받으면 배열의 길이를 정할 수 있다. 하지만 브라우저별 출력이 헷갈리고 구멍 난 배열은 코드 작성을 더욱 어렵게 하므로 삼가는 것이 좋다. 예를 들어 배열에 map() 과 같은 매서드를 사용할 때 빈공간은 오류를 발생시키거나 예상과는 다른 동작이 발생할 것이다.

## Object

- new Object() 는 사용할 일이 딱히 없다. 리터럴 형태로 한번에 여러 프로퍼티를 지정이 가능하기 때문이다.

## Function

- 함수의 인자나 내용을 동적으로 정의 해야 하는 매우 드문 경우에 한해 유용하다.
- Function()을 eval() 의 대용품으로 착각하면 안된다.
  - eval()은 인자로 전달된 문자열 코드를 실행하는 함수이다.

## RegExp

- 정규 표현식은 이전 생성자들과는 다르게 정규 표현식을 동적으로 정의할 때 유용하다.
- 필자는 정규 표현식은 리터럴 형식을 (/^a*b+/g) 로 정의할 것을 권장하고 있다. 그 이유는 구문이 쉽고 성능상 이점이 있다고 한다.

## Date

- Date()는 리터럴 형식이 없어 다른 네이티브에 비해 유용하다.
- date 객체는 new Date()로 생성이 가능하고 Date 생성자는 날짜/시각을 인자로 받는다. (생략 시 현재를 기준으로 대신한다
- date 객체는 유닉스 타임스탬프 값을 얻는 용도로 많이 사용된다.

    ```jsx
    const date = new Date();
    date.getTime() // 유닉스 타입스탬프 값을 얻는법
    Date.now //ES5 이후로 더쉬운 방법이 생겼다
    ```


## Error

- Array 와 마찬가지로 new를 붙이지 않아도 된다.
- Error () 생성자는 error 객체를 반환한다.
- error 객체는 현재의 실행 스택 콘텍스트를 포착하여 객체에 담는 것이다. 실행 콘텍스트는 디버깅에 도움이 될 만한 정보들을 담고 있다.
- error 객체는 throw 연산자와 함께 사용 된다.

    ```jsx
    if ("bug") {
    	throw new Error("error 발생");
    }
    ```

- Error 객체 인스턴스에는 적어도 message 프로퍼티는 들어 있고 type 등 다를 프로 퍼티가 들어있을 수도 있다.
- 사람이 읽기 편해지려면 stack 프로퍼티 대신 error 객체의 toString() 프로퍼티를 호출 하는 것이 좋다.
- Error 네이티브 이 왜 에도  구체적인 에러 타입에 특화된 네이티브들이 있다. 하지만 코드에서 실제로 에러가 발생하면 자동으로 던지기에 직접 던지는 경우는 적다.

## Symbol

Symbol은 ES6에서 선보인 원시 타입이고 충돌 염려 없이 객체 프로퍼티로 사용 가능한 특별한 “유일 값”이다. 하지만 절대적으로 유일함이 보장되진 않는다고 한다.

- 심벌은 프로퍼티명으로 사용할 수 있다. 하지만 개발 과정이나 콘솔창에서 값을 보거나 접근하는 것이 불가능하다.
- ES6에는 심벌 몇 개가 미리 정해져 있고 아래와 같이 사용이 가능하다.

    ```jsx
    obj[Symbol.interator] = function(){}
    ```

- 직전 정의하려면 Symbol 네이티브를 사용한다 하지만 new를 붙이게 되면 에러가 나는 유일한 네이티브 생성자 이다.

    ```jsx
    const testSymbol = Symbol("test symbol");
    testSymbol ; // Symbol(test symbol)
    testSymbol.toString() ; // Symbol(test symbol)
    typeof testSymbol; // "symbol"
    let a = {}
    a[testSymbol] = 123;
    Object.getOwnPropertySymbols(a); //[Symbol(test symbol)] 
    ```

- 심벌은 공용 프로퍼티이다.
- 심벌은 객체가 아닌 단순 스칼라 원시 값이다.

## 네이티브 프로토타입

내장 네이티브 생성자는  각자의 .prototype 객체를 가진다. (Array.prototype, String.prototype … )

- .prototype 객체에는 해당 객체의 하위타입별로 고유한 로직이 담겨 있다.
- .prototype 객체에는 우리가 자주 사용하는 .map() .forEach() .replace() 와 같은 매서드들이 정의 되어있다.
- 네이티브 프로토타입의 프로퍼티는 변경이 가능하지만 권장되진 않은 방법이다.
- 프로토타입에 프로퍼티를 추가하는 것은 괜찮은 것 같다.
- 프로토타입은 디폴트이다.
  - 예를 들어 변수에 적절한 타입의 값이 할당되지 않은 상태에서 Array.prototype은 빈 배열이고 Function.prototype은 빈함수인것 처럼 모두 기본값이다.