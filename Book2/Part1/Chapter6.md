# Chapter6. 작동 위임

- 앞선 **5장 프로토타입**의 결론을 한 문장으로 말하자면, `[[Progotype]]` 체계는 한 객체가 다른 객체를 참조하기 위한 내부 링크라는 것이다.
  - 자바스크립트의 무한한 가능성을 이끌어낼 가장 중요한 핵심 기능이자 실제적인 체계는 전적으로 **'객체를 다른 객체와 연결하는 것'** 에서 비롯된다.
 
## 6.1 위임 지향 디자인으로 가는 길
### 6.1.1 클래스 이론
- 클래스 디자인 패턴에서는 상속의 진가를 발휘하기 위해 될 수 있으면 **메서드를 오버라이드(다형성)** 할 것을 권장하고 작동 추가뿐 아니라 때에 따라서 오버라이드 이전 원본 메서드를 `super` 키워드로 호출할 수 있게 지원한다. 공통 요소는 추상화하여 부모 클래스의 일반 메서드로 구현하고 자식 클래스는 이를 더 세분화(오버라이드)하여 쓴다.
- 소프트웨어 모델링이 필요한 세 개의 유사한 태스크 `Task` `XYZ` `ABC`가 있다고 하자. 클래스 디자인 패턴으로 이의 의사코드를 작성하면 다음과 같을 것이다:
  ```js
  class Task {
    id;
    Task(ID) {
      id = ID;
    }
    outputTask() {
      output(id);
    }
  }

  class XYZ inherits Task {
    label;
    XYZ(ID, Label) {
      super(ID);
      label = Label;
    }
    outputTask() {
      super();
      output(label);
    }
  }

  class ABC inherits Task {
    // ...
  }
  ```
### 6.1.2 위임 이론
- 클래스 지향(객체 지향)과 대비하여 작동 위임을 이용하여 작성한 코드를 **OLOO (Objects Linked to Other Objects)**라 부른다.
  - 작동 위임 `Behavior Delegation`이란 찾으려는 프로퍼티/메서드 레퍼런스가 객체에 없으면 다른 객체로 수색 작업을 위임하는 것을 의미한다. 객체들이 서로 수평적으로 배열된 상테에서 위임 링크가 체결된 모습을 떠올리기 바란다. 
- 앞선 예제의 `Task` `XYZ` `ABC` 등을 클래스 대신 작동 위임을 이용하여 다음과 같이 새로 작성할 수 있다:
  ```js
  // Task 객체에는 다양한 태스크에서 사용할(= 위임할) 유틸리티 메서드가 포함되어 있다.
  Task = {
    setID: function(ID) { this.id = ID; },
    outputID: function() { console.log(this.id); }
  };
  // 'XYZ'가 'Task'에 위임한다.
  // Task 유틸리티 객체에 연결해 필요할 때 특정 태스크 객체가 Task에 작동을 위임하도록 작성한다.
  XYZ = Object.create(Task);
  XYZ.prepareTask = function () {
    this.setID(ID);
    this.label = Label;
  }
  XYZ.outputTaskDetails = function() {
    this.outputID();
  }
  ABC = Object.create(Task);
  // ... 
  ```
#### OOLO 스타일 코드의 특징은...
1. 예제 코드에서 `id`와 `label`은 `Task`가 아닌 `XYZ`의 직속 프로퍼티다. 일반적으로 `[[Prototype]]` 위임시 상탯값은 위임하는 쪽`XYZ` `ABC`에 두고 위임받는 쪽`Task`에는 두지 않는다.
2. 클래스 디자인 패턴에서는 일부러 부모`Task` 자식`XYZ` 양쪽 메서드 이름을 `outputTask`라고 똑같이 붙여 오버라이드(다형성)를 이용했다. 작동 위임 패턴을 정반대다. 서로 다른 수준의 `[[Prototype]]` 연쇄에서 같은 명칭이 뒤섞이는 일은 될 수 있으면 피해야 한다. 작동 위임 패턴에서는 오버라이드하기 딱 좋은 일반적인 메서드 명칭보다는 각 객체의 작동 방식을 잘 설명하는 서술적인 명칭이 필요하다. 이렇게 해야 메서드의 이름만 봐도 어떤 작동을 하는지 그 의미가 분명해지므로 코드의 가독성과 유지 보수성이 높아진다.
3. `this.setID(ID);`는 일단 `XYZ` 객체 내부에서 `setID()`를 찾지만 `XYZ`에는 이 메서드가 존재하지 않으므로 `[[Prototype]]` 위임 링크가 체결된 `Task`로 이동하여 `setID()`를 발견한다. 즉 `XYZ`가 `Task`에 작동을 위임하므로 `Task`가 가진 일반적인 유틸리티 메서드는 `XYZ`가 얼마든지 이용할 수 있는 셈이다.

#### 상호 위임(허용되지 않음)
- 복수의 객체가 양방향으로 상호 위임하면 발생하는 사이클은 허용되지 않는다. 즉 B → A로 링크된 상태에서 A → B로 링크하려고 시도하면 에러가 난다.

### 6.1.3 멘탈 모델 비교
- OO(객체 지향)와 OLOO(작동 위임) 스타일로 작성된 코드를 비교해보자. 먼저 OO 스타일:
  ```js
  // OO 스타일
  function Foo(who) {
    this.me = who;
  }
  Foo.prototype.identity = funciton() {
    return `I am ${this.me}`;
  };
  function Bar(who) {
    Foo.call(this, who);
  }
  // Bar 클래스는 부모 클래스 Foo를 상속한다.
  Bar.prototype = Object.create(Foo.prototype);
  Bar.prototype.speak = function() {
    alert(`Hello, ${this.identify}`);
  }
  // Bar 클래스를 b1, b2로 인스턴스화한다.
  // b1, b2는 Bar.prototype으로 위임되며 이는 다시 Foo.prototype으로 위임된다.
  var b1 = new Bar("b1");
  var b2 = new Bar("b2");
  b1.speak();
  b2.speak();
  ```        
- 이제 OLOO 스타일:
  ```js
  Foo = {
    init: funciton(who) {
      this.me = who;
    },
    identify: function() {
      return `I am ${this.me}`;
    }
  };
  Bar = Object.create(Foo);
  Bar.speak = function() {
    alert(`Hello, ${this.identify}`);
  };
  // b1 → Bar → Foo로의 위임을 활용한다.
  var b1 = Object.create(Bar);
  b1.init("b1");
  var b2 = Object.create(Bar);
  b1.init("b2");
  b1.speak();
  b2.speak();
  ```
- 한눈에 봐도 OLOO 스타일에선 다른 객체와의 연결에만 집중하면 된다는 걸 알 수 있다. OO 스타일의 코드에서는 생성자, 프로토타입, `new` 호출 등을 하면서 클래스처럼 보이려고 하지만 사실은 헷갈리기만 하는 장치들을 쓰고 있는데, OLOO에는 그런 것을 전혀 쓰지 않고 똑같은 기능을 구현하고 있다.

## 6.2 클래스 vs 객체
- 이번에는 실무에서 사용할 수 있는 좀더 구체적인 코드를 작성하며 클래스와 객체 스타일을 비교해보자. 
### 6.2.1 위젯 클래스
- 모든 위젯 작동의 공통적인 기반이 될 부모 클래스 `Widget`을 작성하고, 그 다음에 유형마다 다른 위젯(여기서는 `Button`을 예시로 작성해본다)을 나타내는 자식 클래스를 작성한다.
  - 이번 예문에서는 ES6 `class` 간편 구문을 사용하여 작성한다: 
  ```js
  // 부모 클래스
  class Widget {
    constructor(width, height) {
      this.width = width || 50;
      this.height = height || 50;
      this.$elem = null;
    }
   render($where) {
      if (this.$elem) {
        this.$elem.css({
          width: `${this.width}px`,
          height: `${this.height}px`,
        }).appendTo($where);
      }
    }
  }
  // 자식 클래스
  class Button extends Widget {
    constructor(width, height, label) {
      super(width, height);
      this.label = label || "Default";
      this.$elem = $("<button>").text(this.label);
    }
    render($where) {
      super($where);
      this.$elem.click(this.onClick.bind(this));
    }
    onClick(evt) {
      console.log(`${this.label} 버튼이 클릭됨!`);
    }
  }
  // 버튼 클래스로 화면에 버튼 UI 표시하기
  $(document).ready(function() {
    var $body = $(document.body);
    var btn1 = new Button(125, 30, "Hello");
    var btn2 = new Button(150, 40, "World");
    btn1.render($body);
    btn2.render($body);
  })
  ```
- OO 디자인 패턴에 따르면 부모 클래스에는 기본 `render()`만 선언해두고 자식 클래스가 이를 오버라이드하도록 유도한다. 기본 기능을 완전히 갈아치운다기 보다는 버튼에만 해당하는 작동을 덧붙이는 식으로 기본 기능을 증강 `augmentation`한다.

### 6.2.2 위젯 객체의 위임
- 이번에는 OLOO 스타일의 위임을 적용하여 `Widget/Button`을 작성한다 (훨씬 간단해진다!): 
  ```js
  // 여기서 Widget은 갖가지 유형의 위젯이 위임하여 사용할 수 있는 '유틸리티 창고 역할'을 맡는다
  var Widget = {
    init: funciton(width, height) {
      this.width = width || 50;
      this.height = height || 50;
      this.$elem = $("<button>").text(this.label);
    },
    insert: function($where) {
      if (this.$elem) {
        this.$elem.css({
          width: `${this.width}px`,
          height: `${this.height}px`,
        }).appendTo($where);
      }
    }
  }
  var Button = Object.create(Widget);
  Button.setup = function(width, height, label) {
    // 위임 호출
    this.init(width, height);
    this.label = label || "Default";
    this.$elem = $("<button>").text(this.label);
  }
  Button.build = function($where) {
    // 위임 호출
    this.insert($where);
    this.$elem.click(this.onClick.bind(this));
  }
  Button.onClick = function(evt) {
    console.log(`${this.label} 버튼이 클릭됨!`);
  }

  // 버튼 클래스로 화면에 버튼 UI 표시하기
  $(document).ready(function() {
    var $body = $(document.body);
    var btn1 = new Button(125, 30, "Hello");
    var btn2 = new Button(150, 40, "World");
    btn1.render($body);
    btn2.render($body);
  })
  ```
- OLOO 관점에서는 `Widget`이 부모도, `Button`이 자식도 아니다.  `Widget`은 보통 객체로, 갖가지 유형의 위젯이 위임하여 사용할 수 있는 유틸리티 창고 역할을 맡는다. 그리고 `Button`은 그냥 단독으로 존재하는 객체일 뿐이다. **물론 `Widget`과 위임 링크는 맺어진 상태다!**
- 위임 디자인 패턴에서는 일반적인 이름을 공유하지 않고 각각을 좀 더 서술적으로 명명할 수 있다.
- 클래스 생성자로는 (필수는 아니나 권장하는대로 하자면) 생성과 초기화를 한번에 해야 하지만, OLOO 방식에서는 이 두 단계를 나누어 실행할 수 있다. 이는 '관심사 분리의 원칙 (Principle of Suparation of Concerns)'을 더 잘 반영한 패턴이다.

## 6.3 더 간단한 디자인: 탈클래스화
- (1) 로그인 페이지의 입력 폼을 처리하는 객체, (2) 서버와 직접 인증(통신)을 수행하는 객체 이렇게 두 가지 컨트롤러 객체가 있다고 하자.
  - 전형적인 클래스 디자인 패턴에 의하면 `Controller` 클래스에 기본적인 기능을 담아두고 이를 상속받은 `LoginController`와 `AuthController` 두 자식 클래스가 각자 상황에 맞게 구체적인 작동 로직을 구현하는 식으로 구성된다.
  ```js
  // 부모 클래스
  function Controller() {
    this.errors = [];
  }
  Controller.prototype.showDialog = function(title, msg) { /* 사용자에게 다이얼로그 창으로 타이틀과 메시를 표시 */ };
  Controller.prototype.success = function(msg) { this.showDialog("Success", msg) };
  Controller.prototype.failure = function(err) {
    this.errors.push(err);
    this.showDialog("Error", err);
  };
  // 자식 클래스 LoginController
  function LoginController() { Controller.call(this); }
  // 자식 클래스를 부모 클래스에 연결한다.
  LoginController.prototype = Object.create(Controller.prototype);
  LoginController.prototype.getUser = function() {
    return document.getElementById("login_username").value;
  }
  LoginController.prototype.validateEntity = function(user) {
    user = user || this.getUser();
    if (!user) {
      return this.failure("사용자명을 입력하세요.")
    }
    else if (user.length < 8) {
      return this.failure("사용자명은 8자 이상이어야 합니다.")
    }
    return true
  };
  LoginController.prototype.failure = function(err) {
    Controller.prototype.failure.call(this, `로그인 실패: ${err}`);
  };
  // 자식 클래스 AuthController
  function AuthController(login) {
    Controller.call(this);
    // 상속 + 구성
    this.login = login;
  }
  // 자식 클래스를 부모 클래스에 연결한다.
  AuthController.prototype = Object.create(Controller.prototype);
  AuthController.prototype.server = function(url, data) {
    return $ajax({
      url: url,
      data: data,
    });
  }
  AuthController.prototype.checkAuth = function() {
    var user = this.login.getUser();
    if (this.login.validateEntity(user)) {
      this.server("/check-user", {
        user: user,
        pw: pw
      }).fail(this.failure.bind(this));
    }
  }
  AuthController.prototype.failure = function(err) {
    Controller.prototype.failure.call(this, `인증 실패: ${err}`);
  };
  
  var auth = new AuthController(
    new LoginController()
  );
  auth.checkAuth();
  ```

- 위의 예문을 OLOO 스타일의 작동 위임을 최대한 활용하여 더 간단한 형태로 디자인해보면 다음과 같다:
```js
var LoginController = {
  errors: [],
  getUser: function() {
    return document.getElementById("login_username").value;
  },
  validateEntity: function(user) {
    user = user || this.getUser();
    if (!user) {
      return this.failure("사용자명을 입력하세요.")
    }
    else if (user.length < 8) {
      return this.failure("사용자명은 8자 이상이어야 합니다.")
    }
    return true
  }.
  showDialog: function(title, msg) { /* 사용자에게 다이얼로그 창으로 타이틀과 메시를 표시 */ },
  failure: function(err) {
    this.errors.push(err);
    this.showDialog("에러", `로그인 실패: ${err}`);
  }
}

// AuthController가 LoginController에 위임하도록 연결한다.
var AuthController = Object.create(LoginController);
AuthController.errors = [];
AuthController.checkAuth = function() {
  var user = this.login.getUser();
  if (this.login.validateEntity(user)) {
    this.server("/check-user", {
      user: user,
      pw: pw
    }).fail(this.failure.bind(this));
  }
};
AuthController.server = function(url, data) {
  return $ajax({
    url: url,
    data: data,
  });
};

// AuthController는 평범한 객체이므로 어떤 작업을 시키기 위해 new 연산자를 이용하여 인스턴스화할 필요가 없다.
AuthController.checkAuth();
// 위임 연쇄에 하나 또는 그 이상의 객체를 추가로 생성해야 할 경우는 다음과 같이 하면 된다. 역시 클래스 인스턴스화는 필요없다.
var controller1 = Object.create(AuthController);
var controller2 = Object.create(AuthController);
```

## 6.4 더 멋진 구문: function 키워드가 필요없는 단축 메서드
- ES6 class가 시선을 잡아끄는 매력 중 하나는 클래스 메서드를 짧은 구문으로 선언할 수 있다는 점이다. 즉 선언시 `function` 키워드를 쓰지 않아도 된다.
  ```js
  class Foo {
    methodName() { /* ... */ }
  }
  ```  
- ES6부터는 객체 리터럴에 단축 메서드 선언 (Concise Method Declaration)이 가능하여 다음과 같이 OLOO 스타일 객체를 선언할 수 있다. 클래스 구문과의 유일한 차이라면 객체 리터럴에서는 여전히 콤마`,`로 원소를 구분지어야 한다는 것이다.
  ```js
  var LoginController = {
    getUser() { /* ... */ },
    getPassword() { /* ... */ },
  }
  ```
### 6.4.1 비어휘적 식별자
- 단축 메서드는 익명 함수 표현식이 된다. 익명 함수에 이름 식별자가 없으면 다음과 같은 단점이 있다:
  1. 스택 추적을 통해 디버깅하기가 곤란해진다.
  2. 재귀, 이벤트 등에서 자기 참조가 어려워진다.
  3. 코드 가독성이 조금 더 나빠진다.   

## 6.5 인트로스펙션
- 타입 인트로스펙션 Type Introspection이란 인스턴스를 조사해 객체 유형을 거꾸로 유추하는 걸 말한다.
  - 클래스 인스턴스에서 타입 인트로스펙션은 주로 인스턴스가 생성된 소스 객체의 구조와 기능을 추론하는 용도로 쓰인다.
  ```js
  function Foo() { /* ... */ }
  Foo.prototype...
  
  function Bar() { /* ... */ }
  Bar.prototype = Object.creatE(Foo.prototype);
  
  var b1 = new Bar("b1")
  
  // instanceof 연산자와 .prototype을 이용하여 타입 인트로스펙션을 실시한다.
  // Foo와 Bar의 관계 대조하기
  Bar.prototype instanceof Foo; // true
  Object.getPrototypeOf(Bar.prototype) === Foo.prototype; // true
  Foo.prototype.isPrototypeOf(Bar.prototype); // true
  
  // b1과 Foo, Bar의 관계를 대조하기
  b1 instanceof Foo; // true
  b1 instanceof Bar; // true
  Object.getPrototypeOf(b1) === Bar.prototype; // true
  Foo.prototype.isPrototypeOf(b1); // true
  Bar.prototype.isPrototypeOf(b1); // true
  ```
### 덕 타이핑
- 덕 타이핑 Duck Typing 이라고 하여 많은 개발자가 instancof보다 선호하는 또 다른 타입 인트로스펙션 방법이 있다.
  - 이는 "오리처럼 보이는 종물이 오리 소리(꽥꽥)를 낸다면 오리가 분명하다"라는 옛 속담에서 유래된 용어다.
  - 예를 들면, 위임 가능한 `something()` 함수를 가진 객체와 `a1`의 관계를 애써 조사하는 대신 `a1.something`을 테스트, 즉 `a1`에 직속된 메서드인지, 다른 객체에 위임되어 발견된 메서드인지 상관없이 `a1`이 `something()`을 호출할 자격이 있는지 판단하는 것이다.
  ```js
  if (a1.something) {
    a1.something();
  }
  ```
- 가장 유명한 덕 타이핑의 실례가 바로 ES6 프라미스 `Promise`다.
  - 어떤 임의의 객체 레퍼런스가 프라미스인지 판단하기 위해 해당 객체가 `then()` 함수를 가졌는지 조사하는 식으로 테스트한다. 즉 어떤 객체에 `then()` 메서드가 있으면 ES6 프라미스는 무조건 이 객체는 `Thenable` (then 메서드를 사용할 수 있는) 하다고 판단한다. 따라서 어떤 이유에서건 우연히 `then`이라는 이름의 메서드를 가진 Non-Promise 객체가 있다면 가능한 한 ES6 프라미스 체계와는 멀리 떨어진 곳으로 분리해 그릇된 오해가 없도록 하는 게 좋다.
 
## 6.6 정리하기
- 클래스와 상속은 소프트웨어 아키텍처를 설계할 때 선택을 해도, 안해도 되는 디자인 패턴의 하나다.
- 작동 위임 패턴은 객체를 부모/자식 관계가 아닌 동등한 입장으로 연결한다.
- 객체만으로 구성된 코드를 구성한다면 사용 구문도 단순해질 뿐만 아니라, 실제로 코드 아키텍쳐 또한 더 간단하게 가져갈 수 있다.
- 즉 OLOO는 클래스라는 추상화 장치 없이도 직접 객체를 생성 및 연계한다.

## 느낀점
- 무엇인지도 모르고 잘만 사용하던 것들에 이름붙이기(라벨링)를 하고, 그의 미처 몰랐던 쓰임새를 조금 더 알게 된다. 만약에 오늘 공부한 내용에 나오는 여러 용법을 사용해본 경험이 없었다면, 이걸 도대체 어디에 어떻게 사용하라는 것인지 전혀 감을 잡지 못했을 거다.
