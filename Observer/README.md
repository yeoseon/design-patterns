# Observer  

**관찰하는 사람**  

관찰 대상의 상태가 변화하면 관찰자에게 알려준다.  
상태 변화에 따른 처리를 기술할 때 효과적인 패턴  

## 예제 1

**많은 수를 생성하는 오브젝트를 관찰자가 관찰해서 그 값을 표시하는 프로그램**  

표시의 방법은 관찰자에 따라 다르다.  


* Observer 인터페이스
    * ```java.util.Observer```와는 별개의 클래스  
    * ```update``` 추상 메소드를 선언하고 있다.  
        * 이는 수를 생성하는 **NumberGenerator 클래스에서 호출**된다.  
        * NumberGenerator가 Observer에게 **나의 내용이 갱신되었습니다. 표시 쪽도 갱신해 주십시오.** 라고 전달하는 역할을 수행한다. 
* NumberGenerator 클래스
    * 수를 생성하는 추상 클래스
    * ```observers``` 라는 필드를 list로서 가지고 있다. 해당 클래스를 관찰하는 관찰자를 필드로 관리한다.  
    * 그에 따라 ```addObserver```, ```deleteObserver``` 메소드 역시 가지고 있다.  
    * ```notifyObservers``` 메소드를 Observer 리스트 모두에 대하여 **나의 내용이 갱신되었으므로, 당신의 표시를 갱신해 주십시오.** 라고 전하는 역할을 수행한다.  
        * 여기서 observers의 리스트를 모두 돌며 각각의 Observer 클래스의 ```update``` 메소드를 호출하고 있다.  
    * ```getNumber()```, ```execute()``` 추상 메소드를 선언하고 있다. 이는 하위 클래스에서 구현될 것이다.  
* RandemNumberGenerator 클래스  
    * NumberGenerator의 하위 클래스로서, ```getNumber()```, ```execute()``` 메소드를 구현한다.  
* DigitObserver, GraphObserver 클래스
    * Observer 클래스의 하위 클래스로, ```update``` 메소드를 구현한다.  
    * ```update``` 메소드 안에서 인수로 주어진 NumberGenerator의 ```getNumber``` 메소드를 사용해서, 수를 취득하고, 이를 표시한다.  
    ```
    public void update(NumberGenerator generator) {
        System.out.println("DigitObserver:" + generator.getNumber());
        ...
    }
    ```
* Main 클래스  
    * RandomNumberGenerator의 인스턴스를 한 개 만들고, 이를 관찰할 Observer 인스턴스를 2개 만든다.
    * ```addObserver``` 메소드를 통해서 관찰자를 등록한 후, ```generator.execute```를 통해서 수를 생성한다.  
    ```
    NumberGenerator generator = new RandomNumberGenerator();
    Observer observer1 = new DigitObserver();
    Observer observer2 = new GraphObserver();
    generator.addObserver(observer1);
    generator.addObserver(observer2);
    generator.execute();
    ```

## 정리

### Subject(관찰 대상자)   
'관찰되는 대상'을 나타낸다.  
관찰자인 Observer 역할을 등록하고 삭제하는 메소드를 가지고 있다.  
**현재의 상태를 취득하는** 메소드도 선언이 되어 있다.(```notifyObservers```??)  
예제 1의 NumberGenerator 클래스가 해당된다.  

### ConcreteSubject  
상태가 변화하면 그것이 등록되어 있는 Observer 역할에 전달한다.  
예제 1의 RandomNumberGenerator 클래스가 해당된다.  

### Observer
Subject 역할로 부터 **상태가 변했습니다.** 라고 전달받는 역할을 수행한다.  
이를 위한 메소드는 ```update```이다.  
예제 1의 Observer 인터페이스가 해당된다.  

### ConcreteObserver
```update``` 메소드가 호출되면 그 메소드 안에서 Subject 역할의 현재 상태를 취득한다.  
예제 1의 DigitObserver, GraphObserver 클래스가 해당된다.  

## Hint

### 클래스 교환 가능성  

> 디자인 패턴 목적 중의 하나는 클래스를 **재이용 가능한 부품으로 만드는 것**이다.  

RandomNumberGenerator 클래스는 현재 자신을 관찰하고 있는 관찰자가 DigitObserver의 인스턴스인지, GraphObserver의 인스턴스인지, 몰라도 상관없다.  
그러나 observer 필드에 저장되어 있는 인스턴스들이, Observer 인터페잉스를 구현하고 있다는 것은 알고 있다.  
이 인스턴스들은 ```addObserver```에서 추가된 것이므로, 반드시 Observer 인터페이스를 구현하고 있으며, ```update``` 메소드를 호출할 수 있다.  

한편, DigitObserver 클래스를 자신이 관찰하고 있는 것이 RandomNumberGenerator 클래스의 인스턴스인지 신경쓰지 않아도 된다.  
단지, NumberGenerator의 하위 클래스 인스턴스이고, ```getNumber``` 메소드를 가지고 있다는 것만 알고 있다.  

> 추상 클래스나 인터페이스를 사용해서 구상 클래스로부터 추상 메소드를 분리한다.  
> 인수로 인스턴스를 전달할 때, 필드에서 인스턴스를 저장할 때에는 **구상 클래스의 형태로 하지 않고, 추상 클래스나 인터페이스의 형태로 해둔다.**  

이와 같이 해 두면, 구상 클래스의 부분을 쉽게 교체할 수 있다.  

### Observer의 순서  

Subject 역할에는 복수의 Object가 등록되어 있다.  
일반적으로 ConcreteObserver 역할의 클래스를 설계할 경우, ```update``` 메소드가 호출되는 순서가 변해도 문제가 일어나지 않도록 설계해야 한다.  

> 원래 각 클래스의 독립성이 보장되면 의존성의 혼란은 발생하지 않게 된다.  

하지만 다음과 같은 상황에서 주의해야 한다.  

### Observer의 행위가 Subject에 영향을 미칠 때  

예제 1의 RandomNumberGenerator는 자신 안에서 데이터를 생성하고 ```update``` 메소드를 호출했기에 문제가 없다.  
그러나 일반적인 Observer 패턴에서는, Subject 역할이 다른 클래스로부터 ```update``` 메소드의 호출을 요청받는 경우도 있다.(GUI에서 버튼을 눌러 요청하면 ```update``` 메소드가 호출되는 경우)  
그런데 Subject 역할이 ```update``` 메소드를 호출할 때, Observer 역할이 호출을 요청하는 경우가 있다.  

이 경우 조심하지 않으면 무한루프를 돌 가능성이 있다.
```
Subject 상태 변화 -> Observer에게 전달 -> Observer가 Subject의 메소드 호출 -> Subject 상태가 변화 -> Observer에게 전달 -> ... 
```

이를 방지하기 위해 Observer 역할에게 '현재 Subject 역할로부터 전달받고 있는 중인지 아닌지'를 나타내는 플래그 변수를 한 개 갖도록 하는 것이 좋다.  

### 갱신을 위한 힌트 정보의 취득  

예제 1의 NumberGenerator 클래스는 ```update``` 메소드를 사용해서, '갱신되었다' 라고 Observer에게 통지하고 있다. 이때 ```update``` 메소드로 주어지는 인수는 NumberGenerator의 인스턴스 뿐이다.  
**Observer는 ```update``` 메소드 안에서, ```getNumber```를 호출해서 필요한 정보를 얻어야 한다.**  

하지만 다음과 같은 방식으로 인수를 전달할 수 있다.  
```
void update (NumberGenerator generator, int number); ... (1)
```
```
void update (int number); ... (2)
```

(1)의 경우는, Observer가 Subject로부터 정보를 얻는 시간을 줄일 수 있다.  
이처럼 힌트 정보를 제공한다는 것은 **Subject 역할이 Observer 역할의 처리 내용을 의식하고 있다는 것이다.**  

(2)의 경우는 더욱 간략화해서 전달한다.  
하나의 Observer 역할이 복수의 Subject 역할을 관찰하는 경우에는 부적절하다.  
전달된 수가 어떤 Subject 역할의 정보인지 모르기 때문이다.  

예제 1보다 복잡한 프로그램의 경우, 정말 Observer에게 필요한 정보는 무엇인지, Subject 역할에게 알리는 것은 어려운 문제이다.  
어느 정도의 힌트 정보를 ```update``` 메소드에게 제공할지느 프로그램의 복잡성을 고려해서 판단해야 한다.  

### **관찰**하기보다 **전달**받기 기다린다.  

Observer는 능동적으로 관찰하는 것이 아니고, Subject로부터 **전달되는 것을 수동적으로 기다리고 있다.**  
이를 Publish-Subscribe 패턴이라고도 한다.(발행과 구독)  

### MVC 패턴  
Model과 View의 관계가 Observer 패턴의 Subject 역할과 Observer 역할의 관계에 대응한다.  
Model: 표시 형식에 의존하지 않는 내부 모델을 조작하는 부분이다.  
View: Model이 어떻게 보일 것인지를 관리하고 있는 부분이다.  
> 일반적으로 하나의 Model에 복수의 View가 대응한다.  

### ```java.util.Observer``` 인터페이스

Java 클래스 라이브러리에 등장하는 ```java.util.Observer``` 인터페이스와 ```java.util.Observable``` 클래스는 Observer 패턴의 일종이다.  
인터페이스는 다음과 같은 메소드를 가지고 있다.  
``` 
public void update(Observable obj, Object arg)
```
인수로 Subject의 역할로서의 Observable 클래스의 인스턴스와, 부가 정보로서의 Object 클래스의 인스턴스를 전달하고 있다.  

이 클래스와 인터페이스는 사용하기 쉽지 않다.  

> Java의 클래스는 단일 상속이다.  

Subject 역할로 만들려는 클래스가 이미 무엇인가의 하위 클래스로 되어있는 경우에는 ```java.util.Observable``` 클래스의 하위 클래스로 할 수 없기 때문이다.  

### 관련 패턴 

* Mediator 패턴  
Mediator 패턴에서 Mediator 역할과 Colleague 역할의 통신에 Observer 패턴을 사용하는 경우가 있다.  
두 패턴 모두 **상태 변화를 알린다**는 점에서 비슷하지만, 패턴의 목적이나 시점이 다르다.  
Mediator 패턴에서도 상태변화를 알리지만, Colleague 역할의 조정이라는 목적으로 동작하는 Mediator 패턴의 일부에 지나지 않는다.  
Observer 패턴에서는 Subject 역할의 상태변화를 Observer역할(들)에게 알리는 일, 알려서 동기화를 이루는 일에 주안점을 두고 있다.  
