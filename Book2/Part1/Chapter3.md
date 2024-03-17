# CHAPTER 3. 객체

## 3.1 구문

객체는 선언적(리터럴) 형식과 생성자 형식 두 가지로 정의가 가능하다.

- 선언적(리터럴) 형식
  ```jsx
  const obj = {
    example: true,
  };
  ```
- 생성자 형식
  ```jsx
  const obj = new Object();
  obj.example = true;
  ```

두 형식 모두 결과는 같지만, 유일한 차이점은 리터럴 형식은 한 번에 다수의 프로퍼티를 추가 할 수 있지만 생성자 형식은 한 번에 하나의 프로퍼티만 추가가 가능하다.

## 3.2 타입

- 자바스크립트 객체의 7 주요 타입 (언어 타입)
  1. null
  2. undefined
  3. boolean
  4. number
  5. string
  6. object
  7. symbol
- 단 순 원시타입은 객체가 아니다.
  - typeof null 의 값이 object라는 문자열에서 비롯된 오해이다.
  - 따라서 자바스크립트의 모든 것은 객체로 이루어져 있다는 것은 사실이 아니다.
- 반면 함수와 배열은 특정 기능이 추가 되어있는 객체이다.

### 3.2.1 내장 객체

객체는 내장 객체라는 하위 타입을 가지고 있다.

- 내장 객체
  1. String
  2. Number
  3. Boolean
  4. Object
  5. Function
  6. Array
  7. Date
  8. RegExp
  9. Error

내장 객체는 자바스크립트의 내장 함수 이면 생성자로 사용되며 주어진 하위 타입의 새 객체를 생성한다.

```jsx
const str = 'this is string';
typeof str; // "string"
str instanceof String; //false

const str = new String('this is string');
typeof str2; // "object"
str2 instanceof String; //true

// 객체 하위 타입을 확인한다.
Object.prototype.toString.call(str2); // [object String]
```

위의 코드를 보면 str2 변수가 String 생성자에 의해 만들어진 것을 확인할 수 있다.

“this is string” 라는 위의 원시 값은 객체가 아닌 리터럴이며 불변 값 이다.

하지만 문자열의 길이를 구하는 등의 작업을 하기 위해선 String객체가 필요하다.

다행이도 자바스크립트 엔진이 상황에 맞게 문자열은 String객체로 강제변환을 해주기에 우리가 직접 생성자 함수를 사용하는 일은 별로 없다.

객체래퍼 형식이 없는 null 과 undefined는 그 자체로 유일 값인 반면 Date 값은 맅터럴 형식이 없어 반드시 생성자 형식으로 생성해야 한다.

Object, Array, Functions, RegEXps는 형식과 무관하게 모두 객체이다.

Error 객체는 예외가 던져지면 알아서 생성되어 직접 명시적으로 생성할 일은 드물다.

## 3.3 내용

객체는 프로퍼티로 내용인 채워진다.

그렇다고 프로퍼티가 직접 객체 내부에 저장이 되는 것은 아니고 프로퍼티의 값이 저장된 곳을 가리키는 포인터 역할을 담당하는 프로퍼티명이 담겨 있다.

```jsx
const obj = {
	a = "a",
	b = "b"
}
```

obj 객체에서 a 위치의 값에 접근을 하기 위해서는 `.a` 또는 `[a]` 같은 방법을 사용한다.

`.a` 구문을 프로퍼티 접근 `[a]`구문을 키 접근이라고 한다.

- `.a` 프로퍼티 접근
  - 뒤에 식별자 호환 프로퍼티명이 와야 한다.
- [a] 키 접근
  - 유니코드 호환 문자열이라면 모두 프로퍼티 명으로 쓸 수 있다.

주의 해야 할 점은 객체 프로퍼티명은 언제나 문자열이라는 점이다.

### 3.3.1 계산된 프로퍼티명

```jsx
const obj = {
  ['계산된' + '프로퍼티명']: '계산된프로퍼티명',
};
```

위의 코드와 같이 키 접근 방식을 사용하여 계산식의 형태로 사용할 수 있다.

### 3.3.2 프로퍼티 vs 메서드

```jsx
function foo() {
  console.log();
}
foo();
const obj = {
  foo: foo,
};
obj.foo();
```

위의 코드에서 foo() 와 obj.foo() 두 래퍼런스 모두 똑같은 걸 가리키기에 객체내부의 함수를 매서드라고 부르기에는 애매하다.

### 3.3.3 배열

배열은 일반 객체보다 값을 저장하는 방법과 장소가 더 체계적이다.

인덱스라는 양수로 표기된 위치에 값을 저장한다. 물론 문자열 키로도 값을 저장할 수 는 있지만 배열의 길이가 변하지 않아 지양하는 것이 좋다.

### 3.3.4 객체 복사

js에는 객체를 복사하는 두 가지 선택지가 있다 얕은 복사와 깊은 복사이다.

- 깊은 복사
  깊은 복사를 그냥 하게 되면 복사 대상 내부의 객체의 내부 까지 모두 복사를 해버리는 문제가 발생한다. 이와 같은 문제에 대해 이 책에선 JSON을 사용하는 것이 한 가지 방법이 된다고 한다.
- 얕은 복사
  ```jsx
  var new Obj = Object.assign({},myObject);
  ```
  얕은 복사는 위와 같이 Object.assign 매서드로 할 수 있다.

### 3.3.5 프로퍼티 서술자

Object.getOwnPropertyDescription 매서드로 프로퍼티 서술자를 확인할 수 있다.

Object.defineProperty 매서드로는 프로퍼티 서술자를 정의할 수 있다.

```jsx
var obj = {
	a: 2 ;
}
Object.getOwnPropertyDescription(obj,"a");
//{
//value:2,
//writable:true,
//enumerable:true,
//configurable:true,
//}

```

- 쓰기 기능
  writable이 false가 되면 값 수정인 안된다.  

- 수정 기능
  configurable이 false가 되면 Object.defineProperty 매서드로 수정이 불가능 하다.-
- 열거 가능성
  enumerable이 false가 되면 루프 구문에서 프로퍼티가 숨겨지게 된다.

### 3.3.6 불변성

- 객체 상수
  프로퍼티 서술자의 writable 과 configurable을 false로 하여 상수로 사용할 수 있다.
- 확장 금지
  `Object.preventExtensions` 매서드로 프로퍼티 추가를 막을 수 있다.
- 봉인
  `Object.seal` 매서드는 봉인된 객체를 생성한다. `Object.preventExtensions` 실행하고 서술자의 configurable을 false 로 변경한다.
- 동결
  `Object.freeze` 는 객체를 얼린다 . `Object.seal` 을 실행 후 writable을 false로 변경한다.

### 3.3.7 [[Get]]

[[Get]] 은 객체에서 프로퍼티를 값을 찾을 때 연산을 한다. 값이 있으면 값을 반환하고 없으면 undefined를 반환 한다.

### 3.3.8 [[Put]]

[[Put]]의 간략한 확인 절차

1. 프로퍼티가 접근 서술자인가? 맞으면 세터를 호출
2. 프로퍼티가 writable:false인 데이터 서술자인가? 맞으면 비엄격 모드에서 조용히 실패하고 업격모드는 TypeError가 발생한다
3. 이외에는 프로퍼티에 해당 값을 세팅한다.

좀 더 자세한 내용은 5장에서

### 3.3.9 게터와 세터

프로퍼티의 게터와 세터를 직접 오버라이딩을 할 수 있다.

```jsx
const obj {
	get a() { //a의 게터 지정
		return this._a_;
	}
	set a(val) {//a의 세터 지정
		this._a_ = val * 2;
	}
}
```

### 3.3.10 존재 확인

- in 연산자
  어떤 프로퍼티가 해당 객체에 존재하는지 아니면 [[Prototype]] 연쇄를 따라 갔을 때 상위 관계에 존재 하는 지 확인한다.
- hasOwnProperty
  단지 프로퍼티가 객체에 있는지 확인하고 [[Prototype]] 연쇄는 찾지 않는다.
- 열거 가능성
  열거가 가능하다는 것은 프로퍼티가 객체 순회 리스트에 포함이 되어있단 것이다.
- PropertyIsEnumerable 매서드로 열거가 가능한지 확인할 수 있다.

## 3.4 순회

- for…in
  열거 가능한 객체 프로퍼티를 순회한다.
- 배열 순회 헬퍼
  - forEach
    배열 값 전체를 순회하지만 콜백함수의 반환은 무시한다.
  - every
    배열 끝까지 또는 콜백합수가 false를 반환할 때 까지
  - some은 every와 반대로 true를 반환할 때 까지

for…in을 사용한 객체 순회는 실제로 열거 가능한 프로퍼티만 순회하고 그 값을 얻으려면 일일이 프로퍼티에 접근해야 하므로 간접적인 추출이다.

- for…of
  순회할 원소의 순회자 객체가 있어야 하고 순회당 한 번씩 순회자 객체의 next 메서드를 호출하여 연속적으로 반환 값을 순회한다.
  ```jsx
  var arr = [1, 2, 3];
  var it = arr[Symbol.iterator]();
  int.next(); // {value:1, done:false}
  int.next(); // {value:2, done:false}
  int.next(); // {value:3, done:false}
  int.next(); // {done:true}
  ```
