# Factory  

Template 패턴을 인스턴스 생성에 적용한 패턴  
인스턴스를 만드는 방법을 상위 클래스에서 결정하고, 구체적인 내용은 모두 하위 클래스에서 수행한다.  
인스턴스 생성을 위한 Framework와 인스턴스로 생성한 클래스로 분리해서 생각할 수 있다.

## 예제 1

**ID카드를 만드는 예제**

## 정리

### Product

Framework에 포함되어 이다.  
이 패턴에서 생성되는 인스턴스가 가져야 할 인터페이스(API)를 결정한다.  
구체적인 내용은 ConcreteProduct에서 구현한다.  

### Creator  

Product 역할을 생성하는 추상클래스이다.  
구체적인 내용은 ConcreteCreator 역할이 결정한다.  
실제로 생성하는 ConcreteProduct 역할에 가지고 있는 정보가 없다.  
인스턴스 생성하는 메소드를 호출하면 Product가 생성된다는 정보 뿐이다.  
> new를 사용해서 실제의 인스턴스를 생성하는 대신에 인스턴스 생성을 위한 메소드를 호출해서, 구체적인 클래스 이름에 의한 속박에서 상위 클래스를 자유롭게 만든다.

### ConcreteProduct  

구체적인 제품을 결정한다.  
예제 1의 IDCard 클래스가 해당된다.  

### ConcreteCreator

구체적인 클래스를 만드는 클래스를 결정한다.(new IDCard)  
예제 1에서는 IDCardFactory 클래스가 역할을 수행한다.

## Hint

### Framework와 구체적인 내용

예제 1에 이어서, 동일한 Framework르 사용해서 전혀 다른 '제품'과 '공장'을 만든다고 가정해보자.  
TV의 클래스 Television과 TV공장 TelevisionFactory를 만든다고 할 때, Framework 패키지를 import한 별도의 Television 패키지를 만들게 되는 것이다.  
여기서는 Framework 패키지 내용을 수정하지 않아도 전혀 다른 제품과 공장을 만들 수 있다.
> Framework 패키지의 내용을 수정할 필요는 없다.

Framework 안에서는 idcard 패키지를 import 하지 않는다. 따라서 Product 클래스나 Factory 클래스 안에서는 IDCard 나 IDCardFactory 라는 구체적인 클래스 이름이 없다.  
> 'Framework' 패키지는 idcard 패키지에 의존하지 않는다.

### 인스턴스의 생성 - 메소드의 구현 방법 3가지

1. 추상 메소드로 한다.  
추상 메소드로 할 경우, 하위 클래스는 반드시 상위의 해당 추상 클래스를 구현해야 한다. 구현되어 있지 않을 경우 컴파일시 검출된다. 예제 1에서는 이를 이용하고 있다.

2. 디폴트의 구현을 미리 해둔다.  
디폴트 구현을 미리 해두는 방법이다. 디폴트의 구현을 준비해두고, 하위 클래스에서 구현하지 않았을 경우 이를 사용하도록 한다.  
단, 이 경우에는 Product에 대해 직접 new를 이용하고 있으므로, Product 클래스를 추상 클래스로 둘 수 없다.

3. 에러를 이용한다.  
디폴트의 구현 내용을 에러로 처리해두면, 하위 클래스에서 구현하지 않았을 경우 실행할 때 에러가 발생한다.  

### 패턴 이용과 개발자 간의 의사소통

상/하위 클래스를 이용하는 패턴은 1개의 클래스만으로 동작을 잘 이해할 수 없다.  
상위 클래스에서 동작의 골격을 이해하고, 거기에서 사용되고 있는 추상 메소드가 무엇인지를 확인하고, 그 추상메소드를 구현하고 있는 클래스의 소스코드를 살펴볼 필요가 있기 떄문이다.  
따라서 
> 어떤 클래스를 설계할 때, 클래스를 보수하는 사람에게 설계자가 의도한 디자인 패턴이 무엇인지를 전달할 필요가 있다.  
> 프로그램 주석이나 개발 문서 안에 실제로 사용되고 있는 디자인 패턴의 명칭과 의도를 기술해놓는 것도 좋은 방법이다. 

### 관련 패턴

* Template 패턴  
Factory 패턴은 Template 패턴의 전형적인 응용이다.  
* Singleton 패턴  
Creator 역할을 수행하는 클래스는 대부분 Singleton 패턴으로 만든다.  
* Iterator 패턴  
iterator가 Iterator 인스턴스를 작성할 때, Factory Method 패턴이 사용되는 경우가 있다.

> 한 클래스만 보지 말고, 반드시 클래스나 인터페이스의 상호관계에 관심을 갖도록 해라.