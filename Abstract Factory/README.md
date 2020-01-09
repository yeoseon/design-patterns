# Abstract Factory  

**추상적인 부품**을 조합하여 **추상적인 제품**을 만드는 **추상적인 공장**  
부품의 구체적인 구현에는 주목하지 않고, 인터페이스에 주목한다. 인터페이스만을 사용해서 부품을 조립하고 제품으로 완성한다.

## 예제 1

**계층구조를 가진 Link 페이지를 HTML 파일로 만들기**

* 추상적인 부품에 해당하는 ```Item```(```Link```, ```Tray``` 모두를 받을 수 있는 객체) 클래스와 ```Link```, ```Tray``` 클래스를 생성한다. ```Item``` 클래스에는 ```makeHTML()``` 메소드가 추상 메소드로 선언이 되어있다.  
    * 이 ```Item``` 클래스를 ```extends``` 하는 하위 클래스 ```Link```, ```Tray``` 클래스 역시 추상 클래스 ```Item``` 클래스의 하위 클래스이므로, 추상 메소드를 가지고 있지 않아도 추상 클래스에 해당된다. 
    * ```Tray``` 클래스는 ```Link```나 또다른 ```Tray``` 클래스를 가질 수 있다. 따라서 ```add(Item item)``` 메소드를 가지고 모아서 합치는 역할을 수행한다.

* 추상적인 **제품**에 해당하는 ```Page``` 추상 클래스를 선언한다. 이 클래스 역시 ```add(Item item)``` 를 통해 ArrayList로 선언된 ```content``` 변수에 ```Item```을 조립한다.
    * Page 출력을 위한 ```output()``` 메소드가 선언이 되어 있으며, 이는 Template 형태로 선언이 되어있다.
    * ```Page``` 클래스 역시 ```makeHTML()``` 메소드가 추상메소드로 선언되어, 해당 Page를 HTML로 변환해주는 역할을 하위 클래스에서 구현하여 사용할 것이다.

* 추상적인 **공장**에 해당하는 ```Factory``` 클래스를 선언한다.  
    * ```getFactory```라는 함수 내에서, 입력받은 class명을 통해 해당 클래스를 동적으로 읽는다. 그리고 ```newInstance()``` 메소드를 이용해서, 그 클래스의 인스턴스를 한 개 작성한다. 이는 반환값이 된다.
    * 또한 ```createLink```, ```createTray```, ```createPage``` 추상 메소드를 호출한다. 이는 추상적 공장에서 추상적인 부품을 조립할 때 이용하는 메소드이다. 이는 Factory 하위 클래스에서 구현할 것이다.  
*  Main 함수에서는 단지 추상 패키지인 factory 패키지만 import 하여 사용한다. 즉, 구체적인 부품, 제품, 공장을 전혀 이용하지 않는다.
    * 인수로 구체적인 공장의 클래스 명을 입력하여, 위의 ```Factory``` 메소드에서 동적으로 읽어 추상적인 공장만을 이용해서 구체적인 공장 인스턴스를 생성하게 된다.
* 구체적인 공장 ```ListFactory```에서는 위의 추상적인 공장이 가지고 있는 ```createLink```, ```createTray```, ```createPage```를 구체적으로 구현하게 된다. 
* 구체적인 부품인 ```ListLink```, ```ListTray```를 구체적으로 구현한다. 여기서는 각 부품의 ```makeHTML()``` 메소드를 실제로 구현하게 된다. 
    * 특히 ```Item```을 이용해 부품을 모으는 역할을 하는 ```Tray```의 구체적인 클래스에는 단지 해당 Item이 Tray인지 Link인지에 관계없이 단지 ```Item.makeHTML()``` 함수만 이용하면 된다. 여기에 Item이 무엇이냐에 따라 달라지는 로직을 추가한다면 굉장히 비객체지향적인 코드가 되므로 절대 작성하지 않는다.
* 구체적인 제품인 ```ListPage```에는 역시 ```Page```에 선언된 추상 클래스 ```makeHTML()```을 구체적으로 구현하게 된다. 이 구현체 안에는 각 부품들의 ```makeHTML()```을 이용하게 될 것이다.


> 하나의 공장만을 구현을 한다면 이런 Abstract Factory 패턴은 필요 없을 것이다. 하지만 같은 구조의  ```TableFactory```을 하나 더 구현을 한다면, 이 패턴을 사용하기에 알맞을 것이다. 

## 정리

### AbstractProduct

AbstractFactory 역할에 의해 만들어지는 추상적인 부품이나 제품의 인터페이스를 결정한다.  
예제 1의 ```Tray```, ```Link```, ```Page``` 클래스가 해당된다.

### AbstractFactory    

AsbtractProduct를 만들어내기 위한 인터페이스를 정의한다.  
예제 1의 ```Factory``` 클래스가 해당된다.

### ConcreteProduct

AbstractProduct 역할의 인터페이스를 구현한다.  
예제 1의 listfactory, tablefactory 패키지 내의 제품 클래스가 해당된다.

### ConcreteFactory
AbstractFactory 역할의 인터페이스를 구체적으로 구현한다.  
예제 1의 ```ListFactory```, ```TableFactory``` 클래스가 해당된다.

## Hint

### 구체적인 공장을 새로 추가하는 것은 간단하다. 

Abstract Factory 패턴을 이용하면 구체적인 새로운 공장을 추가하는 것이 간단해진다. 

### 부품을 새로 추가하는 것은 어려워진다.

새로운 부품을 추가하게 되면, 기존의 구체적인 공장 전부에 해당 부품을 추가해줘야 한다.  
API가 정해져 있기에, 부품을 추가하는 것은 기존의 공장이 많으면 많을수록 어려워질 것이다. 

### 관련 패턴

* Builder 패턴  
Abstract Factory 패턴은 인터페이스가 정해져 있는 추상적인 부품을 조합하며 ```복잡합 구조```를 가진 인스턴스를 만든다. Builder 패턴은 ```단계적으로``` 큰 인스턴스를 만들어 나간다.  
* Factory Method 패턴  
제품/부품을 만드는 부분에 Factory Method가 사용될 수 있다.  
* Composite 패턴  
Abstract Factory 패턴을 통해 만들어진 제품이 Composite 패턴으로 되는 경우가 있다.  
* Singleton 패턴
Abstract Factory 패턴의 구체적인 공장이 Singleton 패턴으로 되는 경우가 있다. 

### 보강 : 인스턴스를 만드는 다양한 방법

1. new 를 이용한다.

```
Something obj = new Something();
```

이 경우 클래스 이름을 기술할 필요가 있다.  

2. clone  

Prototype 패턴에 등장했던 clone 메소드를 사용하면, 기존에 존재하는 인스턴스를 복제하는 방법으로 인스턴스를 생성할 수 있다. 이 때는 클래스 이름이 없어도 된다.

```
obj = (Something)clone();
```

3. newInstance  

java.lang.Class 클래스의 newInstance 메소드를 사용하면, Class의 인스턴스를 기초로 그 Class가 표시하고 있는 클래스의 인스턴스를 만들 수 있다.

```
someobj.getClass().newInstance();
```

실제로 newInstance 메소드는 ```InstantiationException``` 이나 ```IllegalAccessException``` 을 예외로 제공하기 때문에 ```try ... catch```로 묶든지, ```throws``` 를 선언해야 한다. 