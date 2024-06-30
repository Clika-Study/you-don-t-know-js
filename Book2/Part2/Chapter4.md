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