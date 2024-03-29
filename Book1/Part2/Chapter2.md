# **Chapter 2. 렉시컬 스코프**

## 2.1 렉스 타임

- 컴파일러는 전통적으로 토크나이징 또는 렉싱을 하는데 렉시컬 스코프는 렉서(Lexer)가 코드를 처리하는 과정인 렉싱 타임(Lexing Time)에 확정된다.
- 스코프 버블은 동시에 다른 두 버블에 존재할 수 없다. 코드 내에서 함수와 같이

    ```jsx
    // scope bubble 1 start
    function foo(a) { // scope bubble 2 start
    	var b = a * 2 ;
    	function bar(c) { // scope bubble 3 start
    		console.log(a,b,c) ;
    	} // scope bubble 3 end
    } // scope bubble 2 end
    foo(2); //2, 4, 12 
    // scope bubble 1 end
    ```


## 2.2 검색

- 엔진은 스코프 버블의 구조와 상대적 위치 사용하여 어디를 검색해야 하는지 안다.
    - 예를 들어 앞의 코드에서 bubble 3의 코드에서 변수 a, b는 상위 스코프 버블에서 가져오고 c는 내부에서 가져올 수 있다
- 검색 순서는 실행 지점부터 상위 스코프를 순서대로 검색한다.
- 검색 중 일치하는 값이 있으면 멈추게 된다.
- 중첩 스코프에서 같은 확인 자명을 사용할 수 있는데 이를 섀도잉(Shadowing)이라 한다.

## 2.3 렉시컬 속이기

런타임 환경에서 렉시컬 스코프를 수정하는 방법이다. 하지만 권장되지 않는 방법이

1. `eval()` 함수 사용
    - eval() 함수는 문자열로 된 코드를 인자로 받아 실행하는데 실행 과정에서 기존의 렉시컬 스코프를 변경할 수 있다.
2. `With` 키워드 사용
    - 기존 With는 객체를 참조하기 위해 사용하지만 with는 속성을 가진 객체를 받아 사실상 새로운 렉시컬 스코프를 생성한다.

하지만 위와 같이 런타임 환경에서 렉시컬 스코프를 변경하게 된다면 엔진은 변경된 사항에 검증을 다시 해야 하기에 기존 최적화 과정에 영향을 끼치기에 성능에 매우 좋지 않다.