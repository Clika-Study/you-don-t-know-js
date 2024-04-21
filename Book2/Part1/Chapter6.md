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
- 한눈에 봐도 OLOO 스타일에선 다른 객체와의 연결에만 집중하면 된다는 걸 알 수 있다. 
