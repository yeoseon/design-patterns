# Bridge  

**기능**의 클래스 계층과 **구현**의 클래스 계층을 연결하는 패턴

> 클래스를 만들 때, 기능을 추가하려고 하는가? 구현을 수행하려고 하는가?를 고민한다.  

기능과 구현의 클래스 계층이 하나의 계층안에 혼재하게 되면, 하위 클래스를 만드려고 할 때 어디에 만들면 좋을지 헤맬 수 있다.  
-> 독립된 클래스 계층으로 분리하자.  
-> 계층 사이 연결 필요  
-> Bridge 패턴

* **기능의 클래스 계층**  
어떤 클래스 Something이 있을 때, 이 클래스의 새로운 기능을 추가하고 싶을 경우, 목적과 가까운 클래스를 찾아 그 하위 클래스를 만드는 것 (너무 깊게 하지 않는 것이 좋다.)  
-> Something의 자식클래스 SomethingGood을 생성한다.  
-> SomethingGood에 기능을 추가하고 싶을 때, 역시 SomethingBetter을 추가한다.
```
Something
    ㄴ SomethingGood
        ㄴ SomethingBetter
```
* **구현의 클래스 계층**  
AbstractClass의 추상메소드를 구현하는 하위 클래스 ConcreteClass  
-> 상위 클래스는 추상 메소드에 의해 인터페이스(API)를 **규정**한다.  
-> 하위 클래스는 구상 메소드에 의해 인터페이스(API)를 **구현**한다.  
```
AbstractClass
    ㄴ ConcreteClass
    ㄴ Another ConcreteClass
```

## 예제 1

**Display 클래스**

* 기능의 클래스 계층  
    * Display : Display하는 기본 기능을 가진 클래스  
        * ```private DisplayImpl impl; ``` 을 통해 구현 인스턴스를 필드로 갖는다.
        * 생성자에는 해당 인스턴스를 전달한다. <- **이 필드가 두 계층의 다리 역할을 수행한다.**
        * 각 기본 메소드들은 DisplayImpl 인스턴스의 메소드를 이용하여 구현되어 있다. -> **Display의 인터페이스가, DisplayImpl의 인터페이스로 변환된다.**
    * CountDisplay : '지정 횟수만큼 Display한다.'는 기능을 추가하는 클래스 (메소드 추가)
        * ```extends Display```를 통해 추가 기능을 구현한다.  
        * 상속받은 메소드를 통해, 새로운 메소드를 추가하고 있다.
        ```
        public CountDisplay(DisplayImpl impl) {
            super(impl);
        }
        ```
* 구현의 클래스 계층
    * DisplayImpl : Display하는 추상 메소드를 정의한 추상 클래스
        * 추상클래스로, Display하기 위한 기능을 추상 메소드로 정의한다
    * StringDisplayImpl : '문자열을 사용해서 Display한다.'는 클래스 (DisplayImpl의 메소드 구현)  
        * DisplayImpl 클래스를 실제 구현한다.

* Main 클래스  
    * 기능의 클래스 계층에 구현된 클래스를 통해 인스턴스를 생성한다. 
    * 인수로 ConcreteImpl 클래스를 전달한다.
    * **Main 함수 내에서만 ConcreteImplement 클래스를 알면 되며, 각 기능 계층 클래스 내에서는 단지 인수로 받은 Impl 클래스를 필드로 가지고 사용한다.**
    * 구현 계층 클래스를 변경하고 싶을 경우, 단지 Main에서만 갈아끼우면 된다. Display 클래스를 수정할 필요가 없다.
``` 
    Display d1 = new Display(new StringDisplayImpl("Hello, Korea"));
    Display d2 = new CountDisplay(new StringDisplayImpl("Hello, world"));
    CountDisplay d3 = new CountDisplay(new String displayImpl("Hello, Universe."));

    d1.display();
    d2.display();
    d3.display();
    d3.multiDisplay(5);
```

## 정리

### Abstraction(추상화)의 역할
기능의 클래스 계층의 최상위 클래스이다.  
Implementor 역할의 메소드를 사용해서 기본적인 기능만이 기술되어 있는 클래스이다.  
이 인스턴스를 Implementor 인스턴스를 필드로 가지고 있다.  
예제 1의 Display 클래스가 이 역할을 수행한다.

### RefinedAbstraction(개선된 추상화)의 역할
Abstraction 역할에 대해 기능을 추가한 역할이다.  
예제 1의 CountDisplay가 이 역할을 수행한다.

### Implementor(구현자)의 역할
구현의 클래스 계층의 최상위 클래스이다.  
Abstraction 역할의 인터페이스를 구현하기 위한 메소드를 **규정**한다.  
예제 1의 DisplayImpl이 이 역할을 수행한다.  

### Concrete Implementor (구체적인 구현자)의 역할
Implementor 역할의 인터페이스를 구체적으로 구현한다.  
예제 1의 StringDisplayImpl이 이 역할을 수행한다.

## Hint

### 왜 사용하는가?  

> 분리해두면 확장이 편해진다.  

기능과 구현의 계층을 분리하면, 각각의 계층을 독립적으로 확장할 수 있다.  
기능을 추가하고 싶으면 기능 계층에 클래스를 추가하고, 구현 계층은 전혀 수정할 필요 없다.  
**새로 추가한 기능은 '모든 구현'에서 이용할 수 있다.**  

EX. 어떤 프로그램이 운영체제의 의존하는 부분이 있어, 윈도우판, 맥킨토시판, 유닉스판으로 분리될 때  
-> 운영체제에 의존하는 부분을 Bridge 패턴의 구현 클래스 계층으로 표현할 수 있다.  
-> 운영체제 **공통**의 API를 정해서 Implementor 역할을 하고, ConcreteImplementor 클래스 내에서 각각의 운영체제의 로직을 구현한다.  
-> 기능 클래스 계층에 아무리 기능을 추가한다 해도 대응이 가능한다.  

### 상속은 견고한 연결이고, 위임은 느슨한 연결이다. 

[상속과 위임](../../../../tip-archive/issues/25) 참고  

```extends Something```을 통해 **상속**을 사용하면, Something 클래스의 하위 클래스가 된다. 이 관계는 소스코드를 고쳐 쓰지 않는 한, 바꿀 수 없는 매우 견고한 연결이다.
>  클래스 간의 관계를 척척 바꾸고 싶을 때 상속을 사용하지 않고 위임을 사용한다.  

예제 1의 Display 클래스 내에서는 impl 필드를 가지고 있어서, 각 기본 메소드 안에 ```impl.rawOpen();```을 이용해 impl의 메소드를 사용하고 있다.  
이렇게 **떠넘기기**를 하고 있는데, 이것이 위임이다.  

**위임은 인스턴스를 만드는 단계에서 인수로 전달되어 연결되지 깨문에, 인수를 바꾸기만 하면 클래스 관계를 쉽게 바꿀 수 있다.**  
StringDisplayImpl을 인수로 넘기지 않고, AnothorDisplayImpl을 넘기기만 하면 되는 것이다.  


### 관련 패턴

* Template 패턴  
  Template 패턴에서는 **구현**의 클래스 계층을 이용한다.
* Abstract Factory 패턴  
Bridge 패턴에 등장하는 ConcreteImplementor 역할을 환경에 맞춰 적절히 구축하기 위해 해당 패던이 이용되는 경우가 있다.  
* Adapter 패턴  
Bridge 패턴은 기능과 구현의 계층을 확실히 분리 후 결합시키는 패턴이다.  
Adapter 패턴도 비슷하지만, 인터페이스가 다른 클래스를 결합시키는 패턴이다.