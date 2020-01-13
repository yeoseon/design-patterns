# Strategy  

알고리즘을 빈틈없이 교체해서 같은 문제를 다른 방법으로도 쉽게 해결할 수 있도록 도와주는 패턴  

## 예제 1

**가위바위보**  

전략 1. WinningStrategy (이기면 다음에도 같은 손을 낸다.)  
전략 2. ProbStratege (직전에 냈던 손에서 다음 낼 손을 확률적으로 계산한다.)  

* Hand 클래스  
    - 주먹, 가위, 보를 int 형 인스턴스 3개로 구성하여 배열에 저장.  
클래스 메소드 ```getHand()```를 사용해서 인스턴스를 얻을 수 있음.  
-> **Singleton 패턴**
    - ```isStrongerThan```, ```isWeakerThan``` 메소드를 선언하여 ```fight()```를 통해 값을 반환한다.
* Strategy 인터페이스
    - 가위바위보의 전략을 위한 **추상 메소드의 집합**
    -  ```nextHand()```와 ```study()``` 메소드를 정의한다.
        - ```nextHand()``` : 다음의 낼 손을 결정  
        - ```study()``` : 직전에 낸 손으로 이겼는지, 졌는지 학습
* Strategy 구현 클래스 (WinningStrategy, ProbStrategy)
    - Strategy를 ```implements``` 받아 구현한 클래스
    - ```nextHand()```와 ```study()```메소드를 구현한다.
    - 알고리즘을 갖는다.
* Player 클래스  
    - 가위바위보를 하는 사람을 표현
    - **이름**과 **전략**이 할당되어 인스턴스를 만든다. 
    - **전략**의 ```nextHand()``` 메소드의 반환값이 그대로 Player의 ```nextHand()```의 반환값이 된다.  
    > **Strategy** 에게 **위임**을 하고 있다.
* Main 클래스
    - Player를 2개 선언하여, 해당 선언시 인수에 **이름**과 **전략** 클래스를 연결한다.  
    ```
    Player player1 = new Player("두리", new WinningStrategy(seed1));
    ```

## 정리

### Strategy
전략을 이용하기 위한 인터페이스 정의  
예제 1의 Strategy 클래스가 해당된다.

### ConcreteStrategy
Strategy의 인터페이스를 실제로 구현한다. 여기에 알고리즘이 들어간다.  
예제 1의 WinningStrategy와 ProbStrategy가 해당된다.

### Context  
Strategy를 이용하는 역할을 한다. ConcreteStrategy의 인스턴스를 가지고 있으며, 필요에 따라 이것을 이용한다.  
예제 1에서는 Player가 해당된다.  

## Hint

### 왜 사용하는가?

알고리즘의 부분을 다른 부분과 의식적으로 분리해서, 일고리즘의 인터페이스 부분만을 규정한다. 그리고 프로그램에서 위임에 의해 알고리즘을 이용한다.  

알고리즘을 개량해서 더 빠르게 하고 싶다고 가정했을 때, Strategy 역할의 인터페이스를 변경하지 않고, ConcreteStrategy의 역할만 수정하면 된다.  

> **위임**이라는 느슨한 연결을 사용하고 있으므로, 알고리즘을 용이하게 교환할 수 있다.  

장기 게임을 하는 프로그램에서 사용자의 선택에 맞추어서 사고 루틴의 레벨을 간단하게 교체할 수 있다. 

### 실행 중에 교체도 가능하다. 

프로그램 동작 중에, ConcreteStrategy를 교체할 수 있다.  
예 1. 메모리가 적은 환경에서 SlowButLessMemoryStrategy (속도는 느리지만 메로리를 절약하는 전략)을 사용하고, 메모리가 많은 환경에서는 FastButMoreMemoryStrategy (속도는 빠르지만 메모리를 많이 사용하는 전략)을 사용하는 것  

예 2. 한쪽의 알고리즘은 다른 쪽 알고리즘의 **검산**에 이용할 수도 있다.  
'버그가 있을 지도 모르는 고속의 알고리즘'을 '저속이지만 호가실한 계산을 실행하는 알고리즘'을 준비하며, 전자의 검산을 후자로 실행시키는 것.


### 관련 패턴

* Flyweight 패턴  
ConcreteStrategy 역할은 Flyweight 패턴을 사용해서 복수의 장소에서 공유 가능하다.
* Abstract Factory 패턴 
Strategy 패턴에서는 알고리즘을 모두 교체할 수 있다. Abstract Factory 패턴에서는 구체적인 공장, 부품, 제품을 모두 교체할 수 있다.  
* State 패턴  
둘 다 위임하는 곳을 교환하는 패턴이고, 클래스 간의 관계도 매우 비슷하지만, 목적이 다르다.  
Strategy 패턴은 '알고리즘'을 표현하느 클래스를 작성하여, 그것을 ConcreteStrategy의 역할의 클래스로 한다. 클래스를 교체할 수 있지만, 필요하지 않으면 안해도 된다.  
State 패턴에서는 '상태'를 표현하는 클래스를 작성해서, ConcreteStrategy 역할의 클래스로 한다. State 패턴에서는 상태가 변화할 때마다 위임하느 곳의 클래스가 반드시 교체된다. 