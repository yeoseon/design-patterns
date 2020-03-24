# Strategy  

알고리즘을 빈틈없이 교체해서 같은 문제를 다른 방법으로도 쉽게 해결할 수 있도록 도와주는 패턴  
객체들이 할 수 있는 행위 가각에 대해 전략 클래스를 생성하고, 유사한 행위들을 캡슐화하는 인터페이스를 정의  
객체의 행위를 동적으로 바꾸고 싶은 경우 직접 행위를 수정하지 않고 전략을 바꿔주기만 함으로써 행위를 유연하게 확장하는 방법  
**객체가 할 수 있는 행위 각각을 전략으로 만들어 놓고, 동적으로 행위의 수정이 필요한 경우 전략을 바꾸는 것만으로 행위의 수정이 가능하도록 만든 패턴**  

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


## 예제 2  

자동차 경주 게임 (https://github.com/next-step/java-racingcar/pull/611)  
- 자동차별로 랜덤 숫자를 뽑아 해당 랜덤 숫자가 4보다 크면 전진하는 것이 규칙이다.  

### GameRule 클래스 Refactoring 전  

```
public class GameRule {

    private static final int NUMBER_CAN_MOVE = 4;

    public boolean canMove() {
        return isNumberCanMove(RandomNumberGenerator.generateRandomValue());
    }

    public static boolean isNumberCanMove(int randomValue) {
        return randomValue >= NUMBER_CAN_MOVE;
    }

    public static int getMaxPosition(List<Integer> finalPositionList) {
        return Collections.max(finalPositionList);
    }

}
```
* 다음과 같이 GameRule을 분리하여, 여기서 게임 규칙을 관리해서 테스트코드를 잘 작성할 수 있도록 했다.  
* 하지만 **자동차가 움직이는 규칙이 랜덤 숫자 외에 또 추가된다면?**  
* 이 때 전략패턴을 이용해볼 수 있을 것이다.  

### GameRule 클래스 Refactoring 후  

MoveRule이라는 추상 인터페이스용을 하나 만들어, canMove() 인터페이스를 선언헀다.  
이를 구현한 RandomMoveRule이라는 구현체를 하나 만들어 이 안에서 구현했다. 
Main 에서는 RacingGame의 규칙을 set 해주는 메소드를 만들어 set해주었다. 

#### MoveRule  
```
public interface MoveRule {

    boolean canMove();
}
```

#### RandomMoveRule  
```
public class RandomMoveRule implements MoveRule {

    public static final int NUMBER_CAN_MOVE = 4;

    public boolean canMove() {
        return isNumberCanMove(RandomNumberGenerator.generateRandomValue());
    }

    public static boolean isNumberCanMove(int randomValue) {
        return randomValue >= NUMBER_CAN_MOVE;
    }
}

```

#### RacingGame  
```
public class RacingGame {
    private MoveRule moveRule;
    ...
    
    public void setMoveRule(MoveRule moveRule) {
        this.moveRule = moveRule;
    }
}
```

#### Main   
```
        RacingGame racingGame = new RacingGame(roundTime, carNameList);
        racingGame.setMoveRule(new RandomMoveRule());
        racingGame.start();

```

## 예제 3  

https://victorydntmd.tistory.com/292 참고  
기차(Train), 버스(Bus) 클래스가 있고, 이 두 클래스는 Movable 인터페이스를 구현한다.  
Train과 Bus 객체를 이용하는 Client도 있다.  

![image](https://user-images.githubusercontent.com/54384004/77371922-c9278d80-6da7-11ea-9d76-79b895bdae2f.png)  

코드로 표현하면 다음과 같다.  

```
public interface Movable {
    public void move();
}
```
```
public class Train implements Movable{
    public void move(){
        System.out.println("선로를 통해 이동");
    }
}
```
```
public class Bus implements Movable{
    public void move(){
        System.out.println("도로를 통해 이동");
    }
}
```
```
public class Client {
    public static void main(String args[]){
        Movable train = new Train();
        Movable bus = new Bus();

        train.move();
        bus.move();
    }
}
```
* 기차는 선로를 따라 이동, 버스는 도로를 따라 이동한다.  
* **선로를 따라 움직이는 버스가 개발되었다고 가정해보자.**  
* Bus의 move() 메서드를 다음과 같이 바꿔주기만 하면 된다.  

```
public void move(){
    System.out.println("선로를 따라 이동");
}
```
* 하지만 이 방식은 SOLID 원칙 중 **[OCP(Open-Closed Principle)](https://github.com/yeoseon/tip-archive/issues/159)에 위배된다.**  
* OCP에 의하면 기존의 move()를 수정하지 않으면서 행위가 수정되어야 하지만 현재 move()를 수정하도록 되어있다.  
* 또한 시스템이 확장이 되었을 경우 유지보수를 어렵게 한다. (택시, 자가용, 고속버스, 오토바이 등이 추가되고 나서 해당 탈 것들의 선로 / 도로 여부가 또 생긴다면 그 모든 것들의 move를 일일히 또 바꿔줘야 한다.)  
* 같은 메서드를 여러 클래스에서 똑같이 정의하고 있으므로 중복이 발생한다.  

### 전략 패턴을 이용한 해결  

#### 전략을 생성하는 방법  

현재의 운송수단은 선로를 따라 움직이든지, 도로를 따라 움직이든지 두 가지의 방식이 있다.  
**움직이는 두 가지의 방식에 대해 Strategy 클래스를 생성하도록 한다.(RailLoadStrategy, LoadStrategy)**  
이 두 클래스는 move() 메서드를 구현하여 어떤 경로로 움직이는 지에 대해 구현한다.  

이 두 전략 클래스를 캡슐화 하기 위해 MoveableStrategy 인터페이스를 생성한다.  
이는 운송수단에 대한 전략 뿐만 아니라 **다른 전략들이 추가적으로 확장되는 경우를 고려한 설계이다.**  

![image](https://user-images.githubusercontent.com/54384004/77372567-3c7dcf00-6da9-11ea-9214-6b87f63c648c.png)

이는 다음과 같은 코드로 표현된다.  
```
public interface MovableStrategy {
    public void move();
}
```
```
public class RailLoadStrategy implements MovableStrategy{
    public void move(){
        System.out.println("선로를 통해 이동");
    }
}
```
```
public class LoadStrategy implements MovableStrategy{
    public void move() {
        System.out.println("도로를 통해 이동");
    }
}
```

#### 운송수단에 대한 클래스 정의하기  

기차와 버스같은 운송수단은 move() 메서드를 통해 움직일 수 있다.  
이 이동방식을 직접 메서드로 구현하지 않고, 어떻게 움직일 것인지에 대한 전략을 설정하여 그 전략의 움직임 방식을 **사용**하여 움직이도록 한다.  
**그래서 전략을 설정하는 메서드인 setMovavleStrategy()가 존재한다.**  

![image](https://user-images.githubusercontent.com/54384004/77372657-7cdd4d00-6da9-11ea-942e-d481b884cd35.png)

이를 코드로 표현하면 다음과 같다.  
```
public class Moving {
    private MovableStrategy movableStrategy;

    public void move(){
        movableStrategy.move();
    }

    public void setMovableStrategy(MovableStrategy movableStrategy){
        this.movableStrategy = movableStrategy;
    }
}
```
```
public class Bus extends Moving{

}
```
```
public class Train extends Moving{

}
```

#### Train과 Bus 객체를 사용하는 Client를 구현  

Train 과 Bus 객체를 생성한 후에, 각 운송 수단이 어떤 방식으로 움직이는 지를 설정하기 위해 setMovableStrategy()를 호출한다.  
얼마나 유연한지 살펴보기 위해 선로를 따라 움직이는 버스가 개발되었다는 상황을 만들어, 버스의 이동 방식 전략을 수정해보자.  

```
public class Client {
    public static void main(String args[]){
        Moving train = new Train();
        Moving bus = new Bus();

        /*
            기존의 기차와 버스의 이동 방식
            1) 기차 - 선로
            2) 버스 - 도로
         */
        train.setMovableStrategy(new RailLoadStrategy());
        bus.setMovableStrategy(new LoadStrategy());

        train.move();
        bus.move();

        /*
            선로를 따라 움직이는 버스가 개발
         */
        bus.setMovableStrategy(new RailLoadStrategy());
        bus.move();
    }
}
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

### Reference   
* https://victorydntmd.tistory.com/292  
