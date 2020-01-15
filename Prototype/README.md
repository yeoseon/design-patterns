# Prototype  

```new```로 인스턴스를 생성할 경우에는 반드시 클래스 이름을 지정해주어야 한다.  
하지만 클래스 이름을 지정하지 않고 인스턴스를 생성하는 것이 필요할 때가 있다.  
이 경우 클래스로부터 인스턴스를 만드는 것이 아니라, 기존에 있는 인스턴스를 복사해서 새로운 인스턴스를 생성한다. (해당 경우는 'Hint'에서 다루도록 한다.)  

> 인스턴스로부터 ```clone```을 통해 별도의 인스턴스를 만드는 패턴을 Prototype 패턴이라고 한다.

## 예제 1

**문자열을 테두리 선으로 감싸거나 밑줄을 표시하는 프로그램**   

### framework 

* Product: 추상 메소드인 ```use```와 ```createClone```이 선언되어 있는 인터페이스.  
 ```java.lang.Cloneable``` 인터페이스를 상속한다. (```extends Cloneable```)
* Manager: ```createClone```을 사용해서 인스턴스를 복제하는 클래스. 

### Concrete

* MessageBox: 문자열을 테두리로 표시하는 클래스. ```use```와 ```createClone```을 실제 구현 (```implements Product```)
* UnderlinePen: 문제열에 밑줄을 표시하는 클래스. ```use```와 ```createClone```을 실제 구현 (```implements Product```)

Product나 Manager 클래스 소스에 Concrete 클래스의 이름이 전혀 나오지 않는 점을 주목한다.  
> 소스 안에 클래스의 이름을 쓰면 그 클래스와 밀접한 관계가 생긴다. 

Java언어의 ```clone``` 메소드는 ```java.lang.Cloneable``` 인터페이스를 구현하고 있는 클래스(혹은 하위 클래스)에서만 사용 가능한다.  
따라서 다른 클래스의 요청으로 복제를 하는 경우에는 ```createClone```과 같은 다른 메소드를 만들어서 ```clone```을 호출하도록 제공해야 한다. 


## 정리

### Prototype

인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정한다.  
예제 1의 Product 클래스가 해당된다.

### ConcretePrototype

인스턴스를 복사해서 새로운 인스턴스를 만드는 메소드를 실제로 구현한다.

### Client

인스턴스를 복사하는 메소드를 이용해서 새로운 인스턴스를 만든다.  
예제 1의 Manager 클래스가 해당된다. 

## Hint

### 왜 사용하는가?  

1. 종류가 많아서 클래스로 정리할 수 없는 경우

2. 클래스로부터 인스턴스 생성이 어려운 경우  

* 사용자가 조작을 해서 만든 도형을 나타내는 인스턴스와 같은 것을 만들고 싶을 때, 클래스를 통해 만드는 것보다 기존의 인스턴스를 복사해서 만드는 것이 더 간단할 것이다.  

3. framework와 생성하는 인스턴스를 분리하고 싶은 경우  

* 예제 1에서는 Manager 클래스(framework)의 ```create``` 메소드에는 클래스 이름 대신에 HashMap을 통해 문자열로 따로 제공하고 있다. 즉 **클래스 이름의 속박으로부터 framework를 분리**할 수 있다.

### 클래스 이름은 정말 속박인가?

디자인 패턴의 **부품으로서의 재이용** 이라는 목적을 생각해볼 필요가 있다.  
소스 내부에 이용할 클래스 이름이 쓰여있으면 그 클래스와 분리해서 재이용할 수 없게 된다.  

> Java에서는 클래스파일(.class)만 가지고 있더라도 그 클래스를 재이용할 수 있는 지가 중요하다.  
> 즉, 소스파일(.java)가 없어도 재사용할 수 있어야 한다. 

### 관련 패턴

* Flyweight 패턴  
  Prototype 패턴에서는 현재의 인스턴스와 같은 상태의 별도의 인스턴스를 만들어 이용한다.  
  Flyweight 패턴은 하나의 인스턴스를 복수의 장소에서 공유하여 이용한다.
* Memento 패턴  
Prototype 패턴에서는 현재의 인스턴스와 같은 상태의 별도의 인스턴스를 만든다.  
Memento 패턴에서는 스냅샷과 undo를 실행하기 위해 현재의 인스턴스 상태를 저장한다.  
* Composite, Decorator 패턴  
Composite 패턴이나 Decorator 패턴을 많이 이용하면, 복잡한 구조의 인스턴스가 동적으로 만들어 지는 경우가 있다. 이때 Prototype 패턴을 이용하면 편리하다.
* Command 패턴  
Command 패턴에 등장하는 명령을 복제하고 싶을 때, Prototype 패턴을 많이 이용한다. 

## 보강 :: ```clone``` 메소드와 ```java.lang.Cloneable``` 인터페이스

### Java의 ```clone```
```clone``` 메소드를 실행할 경우, 복사 대상이 되는 클래스는 ```java.lang.Cloneable``` 인터페이스를 구현할 필요가 있다.  
복사 대상이 되는 클래스가 직접 구현하거나, 하위 클래스 어딘가에서 구현해도 상관 없다. 예제 1 에서는 Product라는 상위 클래스를 구현하고, Product 인터페이스는 Cloneable 인터페이스의 하위 인터페이스로 구성되어 있다.

```clone``` 메소드를 호출하면 복사되어 만들어진 인스턴스를 반환한다.  
원래의 인스턴스와 같은 크기의 메모리를 확보해서, 그 인스턴스의 필드 내용을 복사한다.

만약 Cloneable 인터페이스를 구현하지 않는 클래스의 인스턴스가 ```clone``` 메소드를 호출하면 예외 ```CloneNotSupportedException``` 이 발생한다.

### clone 메소드는 어디에서 정의하는가?

```java.lang.Object``` 클래스에서 정의되어 있다. Object 클래스는 Java의 클래스 계층의 기본 클래스이기 때문에 어느 클래스에서도 ```clone``` 메소드를 상속하게 되어있다.

### Cloneable이 요구하는 메소드는?

Cloneable 인터페이스네는 메소드가 하나도 선언되어 있지 않다. 단지  "```clone```에 의해 복사할 수 있다"는 표시로서 사용되고 있다. 이와 같은 표시를 하는 인터페이스를 [maker interface](https://woovictory.github.io/2019/01/04/Java-What-is-Marker-interface/) 라고 한다.

[참고 :: 정의하려는 것이 타입이라면 마커 인터페이스를 이용하라.](https://jaehun2841.github.io/2019/02/04/effective-java-item41/#%EC%84%9C%EB%A1%A0)
### clone 메소드는 피상적인 복사를 실행한다.

[mutable, immutable 객체 -> Shallow copy와 Deep copy](../../../../tip-archive/issues/17)  

```clone``` 메소드에 의해 이루어지는 것은 필드의 내용을 그대로 복사한다는 동작이다.  
필드의 앞에 있는 인스턴스의 내용까지는 고려하지 않는다는 말이다.  
예를 들어 필드 앞에 배열이 있다고 가정했을 때, ```clone```을 이용해서 복사를 한 경우 그 배열에 대한 참조만 복사될 뿐이고, 배열의 요소 하나하나가 복사되는 것은 아니다.

이와 같은 필드 대 필드 복사(field-for-field copy)를 피상적인 복사(Shallow copy)라고 한다.  

```clone``` 메소드가 실행하는 것은 '피상적인 복사'이다.  
만약 피상적인 복사만으로 곤란한 경우에는 ```clone``` 메소드를 오버라이드해서 자신이 필요한 '복사'를 구현할 수 있다. 이때, ```super.clone()```을 사용한 상위 클래스의 호출을 잊지 말아야 한다.  

[참고 :: Shallow copy와 Deep copy](https://blueshw.github.io/2016/01/20/shallow-copy-deep-copy/)  
[참고 :: Java에서의 Shallow copy와 Deep copy](https://geunhokim.wordpress.com/2013/06/15/deep-copy-shallow-copy/)  
[참고 :: Is clone() in java shallow copy?](https://stackoverflow.com/questions/5279256/is-clone-in-java-shallow-copy)

```clone```은 복사를 하는 것뿐이며, 생성자는 호출하는 것이 아니다.  
인스턴스 생성시 무언가 특별한 초기화를 필요로 하는 클래스에서는 ```clone```메소드 안에 처리를 기술할 필요가 있다.

[참고 :: java.lang.Object 클래스의 ```clone``` 메소드](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#clone())  
[참고 :: java.lang.Cloneable 인터페이스](https://docs.oracle.com/javase/7/docs/api/java/lang/Cloneable.html)