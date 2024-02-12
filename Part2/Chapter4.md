# **Chapter 4. 호이스팅**

## 4.1 닭이 먼저냐 달걀이 먼저냐

```jsx
// 예시 1
a = 2; 
var a; 
console.log(a); // result: 2
// 예시 2 
console.log(a); // result: undefined
var a = 2; 
```

예시 1번의 코드의 결과는 2 지만 왜 예시 2번의 코드의 결과는 undefined 일까?



## 4.2 컴파일러는 두 번 공격한

자바스크립트 엔진은 컴파일 레이션 단계 중 선언문을 찾아 적절한 스코프에 연결을 해준다.

따라서 선언문은 코드 실행 전에 스코프 상단에서 실행이 된다. 그래서 예시 1번의 결과는 2가 나오게 된 것이다. 하지만 예시 2번은 왜 undefined일까? 그 이유는 “var a = 2”는 하나의 구문 이아니라

- var a
- a =2

두 구문으로 나뉘게 된다. var a는 선언문이라 상단으로 옮겨 지지만 a=2 대입문은 그대로 그 위치에 있기 때문에 예시 2번에서 변수는 선언되었지만, 값이 할당되지 않은 상태에서 출력하였기에 undefined 가 출력된 것이다.

```jsx
foo() //typeerror
function foo() {
	console.log(a); //undefined
	var a = 2 ;
}
```

위의 코드에서 typeError가 발생하는 이유는 함수 선언문이 상단으로 옮겨지는 것은 맞지만 선언만 되고 함수 대입문은 실행이 되지 않았기에 undefined 값이 foo를 실행하려 하여 typeError가 발생한다.

## 4.3 함수가 먼저다

```jsx
foo()//1
var foo;

function foo() {
	console.log(1);
}
foo = function () {
	console.log(2);
}
```

위의 코드에서 결과 값은 1이다. 그 이유는 함수 선언문의 우선순위가 변수보다 높기 때문이다.

```jsx
foo()//3
var foo;

function foo() {
	console.log(1);
}
foo = function () {
	console.log(2);
}
function foo() {
	console.log(3);
}
```

하지만 그 아래 함수 선언문이 여러 개 있다면 마지막 함수 선언문의 값이 덮어씌워진다. 매우 혼란스럽기에 이러한 중복은 지양해야 한다.