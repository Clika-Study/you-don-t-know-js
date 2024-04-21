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
- 이번에는 위의 문제를 클래스 대신 작동 위임을 이용하여 작성해본다:
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
