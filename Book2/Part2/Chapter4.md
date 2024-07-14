# CHAPTER 4. 제네레이터
## 4.1 완전-실행을 타파하다

함수는 한번 실행이 되면 다른 코드가 끼어들 수 없는 완전 실행 법칙을 따른다. 하지만 제너레이터 는 완전 실행 법칙을 따르지 않는 특이한 함수 이다. 

```jsx
let x = 0;

function *generator() {
	x++;
	yield;
	x++;
	console.log(x);
}

const it = foo() ; // 함수 실행 안 함 
it.next();  // yield 까지의 코드 실행
x;//1;
x += 1; //2
it.next() // x: 3  // yield 부터의 코드 실행
```

제너레이터 함수를 선언하는 방법은 함수 선언 시 함수명 앞부분에  `*`  기호를 추가해주는 것이다. 

함수 명 앞에 * generator 이런 식으로 붙일 수도 있고 아니면 function 뒤에 붙여 funtion* 과같이 선언이 가능하다. 기능은 똑같기에 코드 스타일의 차이다.

- 코드 동작
    
    제너레이터 를 간단히 설명하자면 코드를 우리가 원하는 만큼만 코드를 실행시킬 수 있는 함수이다.
    
    제너레이터 는 함수의 할당으로 코드가 실행되진 않는다. 
    
    제너레이터 를 실행시키기 위해서는 .next()를 사용해야한다. 실행을 시킨다면 yield 까지의 코드만 실행 하거나 끝까지 실행한다.
    
    꼭 제너레이터 함수를 실행시킨다 해서 끝까지 실행시킬 필요는 없다.
    

### 4.1.1 입력과 출력

제네레이터는 함수 호출 방법이 다르다. 먼저 이터레이터 객체를 생성해 변수 it에 할당하고 it.next() 해야 제네레이터가 다음 yield 또는 끝까지 실행할 수 있다.

```jsx
function *foo(x){
	const y = x + yield 
	return y;
}
const it = foo(6) ;
it.next(); //yield 까지 코드 실행
const res = it.next(7); // yield 의 값이 7이된다
res // 42
```

제너레이터도 함수이기에 인자를 받고 어떤 값을 반환하는 기능은 일반함수와 같다.

위의 코드에서 특이한 부분이 연산식에 yield가 있다는 것인데 제너레이터에서 [it.next](http://it.next/) 로 yield의 값을 할당 해주는 것이 가능하다.

```jsx
const y = x + yield 2
```

위와 같이 yield의 값을 지정해 줄 수 있다.

it.next()의 결괏값은 함수 반환 값을 value 프로퍼티에 저장한 값이다.

만약 return을 하지 않는다면 암시적으로 return 처리를 한다.

### 4.1.2 다중 이터레이터

같은 제네레이터로 여러 인스턴스를 생성할 수 있고 인스턴스끼리 인터리빙과 같은 상호 작용도 가능하다.

```jsx
function *foo(){
	const y = x + yield;
	z++;
	return y
}
const it = foo()
const it2 = foo();
```

## 4.2 값을 제네레이터링

### 4.2.1 제조기와 이터레이터

- ES6부터는 배열 같은 자바스크립트 내장 자료 구조에도 기본 이터레이터가 장착 되있다.

for … of 루프를 사용하면 배열의 이터레이터를 추출하여 원소를 순회 한다.

- 일반 객체에는 기본 이터레이터가 없다.

### 4.2.2 이터러블

next() 매서드를 사용하여 인터페이스 하는 객체를 이터레이터라고 한다. 그러나 순회할 수 있는 이터레이터를 포괄한 객체 이터러블 이 더 밀접한 용어라고 저자는 얘기한다.

ES6의 심볼값 Symbol.iterator 함수를 호출하여 이터러블에서 이터레이터를 가져올 수 있다.

```jsx
var a = [1,2,3,4,5,6];
var it = a[Symbol.iterator]();
it.next().value //1
it.next().value //2
it.next().value //3
```

### 4.2.3 제너레이터 이터레이터

이터레이터의 맥락에서 제너레이터를 다시 살펴보자면 제너레이터는 이터레이터 인터페이스의 next()를 호출하며 값을 한번에 하나씩 추출한다. 

제너레이터의 이터레이터 인터페이스의 루프 과정에서 return, break, 예상치 못한 에러와 같은  비정상 완료가 제너레이터의 이터레이터를 중지한다. 이럴 때는 try … finally 절을 사용할 수 있다. 

## 4.3 제너레이터를 비동기적으로 순회

비동기 함수를 제너레이터에서 사용하면 어떻게 될까. 

```jsx
function foo() {
	ajax(".....",function(err,data){
		if(err) {
			it.throw(err) //*main으로 에러를 던진다
		}	
		else {
			// 수신한 data롤 *main을 제게 한다. 
			it.next(data);
		}
	})
}

function main*(){
	try {
		const text = yield foo();
		console.log(next);
	}
	catch (err){
		console.log(err)
	}	
}
const it = main();
it.next(); // 비동기 함수의 결과가 출력된다.
```

  위의 코드를 보면 제너레이터 함수 내에서 yield 부분에서 멈추며 foo 함수를 실행하는데 foo함수 내부에서 비동기 작업 이후 결과를 다시 it.next(); 제너레이터를 이어서 실행하는 것을 확인할 수 있다.

이런 식으로 구현하게 되면 비동기 작업을 좀 더 동기 적으로 추상화 하는데 도움이 된다. 

### 4.3.1 동기적 에러 처리

try…catch 문은 비동기 에러를 잡을 수가 없다. 하지만 위의 코드와 같이 비동기 함수에서 에러를 제너레이터에 던지면서 제너레이터가 에러를 잡을 수 있게 잠시 멈추는 게 가능하다.

내부에서의 처리 말고도 외부에서도 처리가 가능하다.

```jsx
function *main(){
//제너레이터
}

const it = main();
try {
	it.next(); //err발생하면 catch절로 이동
}
catch(err){
	console.log(err) //제너레이터 err 내용
}
```

## 4.4 제너레이터 + 프라미스

```jsx
function foo(){
	return request("http.......");
}

function *main() {
	try {
		const text = yield foo();
		console.log(text);
	}
	catch(err){
		console.err(err);
	}
} 

const it =main();
const p = it.next().value // promise를 결과로 받음
// 일반적인 프로미스 처리후 제너레이터 이어서 실행
p.then((text)=>{
	it.next(text); 
},(err)=>it.throw(err))

```

위의 코드와 같이 프라미스를 제너레이터와 함께 사용이 가능하다.

중요한 부분은 프라미스 제너레이터를 최대한 활용하는 방법은 위와 같이 프라미스를 yield 한 다음, 이 프라미스로 제너레이터의 이터레이터를 제어하는 것이다.

만약 여러 비동기 코드가 동시에 실행돼야 할 일이 있으면 yield 로는 동시에 두 곳에서 멈추는 것이 불가능 하기에 좀 더 프라미스 본연의 능력을 사용하여 코드를 작성하는 것이 좋다.


## 4.5 제너레이터 위임
```js
function *foo*() {
  console.log("'*foo()' 시작");
  yield 3;
  yield 4;
  console.log("'*foo()' 끝");
}

function *bar() {
  yield 1;
  yield 2;
  yield *foo(); 'yield'-위임!
  yield 5;
}

var it = bar();
it.next().value; // 1
it.next().value; // 2
it.next().value; // '*foo() 시작'
                 // 3 
it.next().value; // 4
it.next().value; // '*foo() 끝' 
                 // 5
```

두 번째 `it.next()` 호출까지는 `*bar()`를 제어하지만 세 번째 호출은 `*foo()`를 시작하고 `*bar()` 대신 `*foo()`를 제어한다. 
즉 `*bar()`가 자신의 순회 권한을 `*foo()`에게 위임했다고 볼 수 있기 때문에 위임이라는 말이 붙은 거이다. 
`it` 이터레이터로 전체 `*foo()` 이터레이터를 훑고나면 자동으로 제어권은 `*bar()`로 넘어온다.

### 4.5.1 왜 위임을?
`yield`-위임을 하는 목적은 주로 코드를 조직화하고 그렇게 해서 일반 함수 호출과 맞추기 위함이다. 

### 4.5.2 메시지 위임
`yield`-위임은 이터레이터뿐 아니라 양방향 메시징에도 쓰인다. 다음은 예외 처리를 위임하는 예시다.
```js
function *foo() {
  try {
    yield "B";
  } catch (err) {
    console.log("'*foo()'에서 붙잡힌 에러:", err)
  }
  yield "C";
  yield "D";
}

function *bar() {
  yield "A";
  try {
    yield *foo();
  } catch (err) {
    console.log("'*bar()'에서 붙잡힌 에러", err);
  }
  yield "E";
  yield *baz();
  yield "G";
}

function *baz() {
  throw "F";
}

var it = bar();

console.log(it.next().value); // A
console.log(it.next(1).value); // B
console.log(it.throw(2).value);
// *foo()에서 붙잡힌 에러: 2
// C
console.log(it.next(3).value);
// *bar()에서 붙잡힌 에러: D
// E

try {
  console.log(it.next(4).value);
} catch (err) {
  console.log(err);
}
// 외부에서 붙잡힌 에러: F
```
1. `it.throw(2)`하면 에러 메시지 `2`를 `*bar()`에 전하고 이 메시지는 다시 `*foo()`로 위임되어 우아하게 에러를 잡아 처리한다. 이후 `yield "C"`는 `it.throw(2)` 호출의 결괏값 "C"를 보낸다.
2. 그리고 `*foo()` 에서 `*bar()` 방향으로 전파되어 던져진 "D"는 `*bar()`가 잡아 역시 우아하게 처리한다. 그런 다음 `yield "E"`는 `it.next(3)` 호출의 결괏값 "E"를 보낸다.
3. 다음, `*baz()`에서 발생한 예외는 `*bar()`에서 잡히지 않았다. 따라서 `*baz()` `*bar()` 모두 완료 상태가 된다. 이 코드 밑으로 연달아 `next()`를 호출해도 `undefined`를 반환할 뿐 "G"값을 받을 방법은 없다. 

## 4.6 제너레이터 동시성


## 4.7 썽크
- 일반 컴퓨터 Thunk라는, 자바스크립트 이전에 등장한 개념이 있다. 한정된 의미에서 표현하자면 다른 함수를 호출한 운명을 가진 인자가 없는 함수다.
- 어떤 함수 정의부를 또 다른 함수 호출부로 감싸 실행을 지연시키는데, 여기서 감싼 함수가 바로 Thunk다. 따라서 Thunk를 실행하면 결국 원래 함수를 호출하는 것이나 다를 바 없다.
- 일일이 Thunk를 수동으로 코딩할 필요 없이, Thunk를 만드는 함수를 생성할 유틸리티를 작성할 수 있다:
  ```js
  function thunkify(fn) {
    var args = [].slice.call(arguments, 1);
    return function(cb) {
      args.push(cb);
      return fn.apply(null, args);
    };
  }
  var fooThunk = thunkify(foo, 3, 4);

  fooThunk(function (sum) {
    console.log(sum); // 7
  });
  ```
  
### 4.7.1 s/promise/thunk
- Thunk와 Promise는 작동 개념이 동등하지 않아 직접적인 상호 호환성은 없다. 있는 그대로의 Thunk와 비교하면 Promise가 훨씬 더 좋다. 한편 둘 다 어떤 값을 요청하여 비동기적 응답을 받는다는 점은 같다.
- Thunk 자체는 Promise의 믿음성/조합성을 거의 보장하지 못한다. Thunk를 특정한 제너레이터의 비동기 패턴에서 Promise 대용으로 쓸 수는 있지만 Promise가 제공하는 제반 혜택을 생각하면 이상적인 해결책은 아니다.

## 4.8 ES6 이전 제너레이터
### 4.8.1 수동 변환
### 4.8.2 자동 변환

## 4.9 정리하기
  
