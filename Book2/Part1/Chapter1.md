# CHAPTER 1. this라나 뭐라나

## 1.1 this를 왜?

```jsx
function identify() {
  return this.name.toUpperCase();
}

function speak() {
  var greeting = "Hello, I'm " + identify.call(this);
  console.log(greeting);
}

var me = { name: 'Kyle' };
var you = { name: 'Reader' };

identify.call(me); // Kyle
identify.call(you); // Reader
speak.call(me); // Hello, I'm Kyle
speak.call(me); // Hello, I'm Reader
```

- this를 사용하여 identify() , speak() 두 함수를 개별 함수를 만들지 않고 me, you 객체에서 사용할 수 있다.
- 콘텍스트 객체를 명시하는 것 보단 암시 적인 객체 래퍼런스를 함께 넘기는 this 체계가 API 설계상 조금더 깔끔하고 명확하며 재사용하기가 쉽다.
- 명시적 인자로 콘텍스트 객체를 명시하면 코드가 더 지저분해 진다.

## 1.2 헷갈리는 것들

사람들은 보통 this 의 의미를 아래 두 가지로 해석하는데 사실 둘 다 틀렸다.

### 1.2.1 자기 자신

```jsx
fuction foo(num) {
	console.log("foo: " + num );
	// 'foo' 가 몇 번 호출됬는지 추적한다
	this.count++;
}

foo.count = 0;
var i;
for (i=0;i <1-;i++) {
	if(i > 5) {
		foo(i)
	}
}
//foo: 6
//foo: 7
//foo: 8
//foo: 9
//foo는 몇 번 호출됐을까?
console.log(foo.count) // 0 ==> 엥?
```

위의 코드는 자바스크립트에서 함수는 객체이기에 this(자기 자신을 지칭한다고 생각하여)를 활용하여 프로퍼티를 지정하려는 코드이지만 결과는 예상과 다른 것을 알 수 있다.

- 위와 같은 상황에서 보통의 개발자들은 count 프로퍼티를 다른 객체(함수 외부의 객체)로 옮긴 거는 렉시컬 스코프에 의존하는 우회 방법을 사용하게 된다.
- 아니면 함수 본인의 이름(위의 코드에서의 foo)을 사용하여 프로퍼티를 지정하는 방법이 있다.
  - 하지만 이 우회 방법은 setTimeout() 에 콜백으로 전달하는 경우에는 사용할 수 없고 이 또한 렉시컬 스코프에 의존하는 방법이다.

### 1.2.2 자신의 스코프

```jsx
function foo() {
  var a = 2;
  this.bar();
}

function bar() {
  console.log(this.a);
}
foo(); // 참조 에러 : a는 정의되지 않았습니다.(ReferenceError: a is not defineded
```

- this가 함수의 스코프를 가리 킨다는 것은 흔한 오해이다 .
- 위의 코드를 보면 this가 렉시컬 스코프를 가리킨다 생각하고 작성했다는 것을 알 수 있다.
- 하지만 this는 어떤식으로도 렉시컬 스코프를 참조할 수 없기에 참조에러가 난 것을 확인할 수 있다.

## 1.3 this는 무엇인가

- 함수 선언 위치와 상관 없이 this 바인딩은 어떻게 함수를 호출했느냐에 따라 정해진다.
- 어떤 함수를 호출하면 활성화 레코드, 즉 실행 콘텍스트가 만들어진다. 여기에는 함수가 호출된 근원 ,방법 등 전달된 인자의 정보가 담겨져 있고 this도 그중 하나인 것이다.
- 즉 this가 무엇을 가리킬지는 호출된 함수 코드에 달려있다.
