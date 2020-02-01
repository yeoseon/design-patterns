# Command  

보통 클래스가 일을 실행할 때, 자신이나 다른 클래스의 **메소드를 호출한다.**  
일은 실행되지만, 일의 이력은 어디에도 남지 않는다.  

이 일을 실행하라는 **명령을 클래스로 표현한다.**  
즉 명령을 하나의 **물건**처럼 표현할 수 있다.  
이력을 관리하고 싶을 때에는 해당 클래스의 인스턴스를 보관해두면 된다.  
저장해두면, 명령을 재실행할 수도 있고, 복수의 명령을 모아 재이용할 수도 있다.  

## 예제 1

**간단한 그림그리기**  

마우스 드래스하면 빨간 점의 열이 그려지고, clear 버튼을 누르면 모두 없어진다.  
마우스를 드래그 할 때마다 DrawCommand라는 이 위치에 점을 그려라 라고 하는 명령 클래스의 인스턴스가 생성된다.  

### Command 인터페이스  

* 명령을 표현하기 위한 인터페이스  
* 단 하나의 ```execute```메소드를 가지낟.  

### MacroCommand 클래스  

* 복수의 명령을 모은 명령 클래스이다.  
* ```commands``` 필드를 가지고 실행한 Command 인터페이스를 모아두기 위한 것이다.  
    * ```pop``` 을 통해 undo에 대한 쉬운 구현을 위해 ```java.util.Stack```으로 선언한다. ([java.util.Stack](https://github.com/yeoseon/tip-archive/issues/71) 참고)  
* Command 인터페이스의 ```execute``` 메소드에서는 ```commands``` 필드에 저장되어 있는 각 인스터스의 ```execute``` 메소드를 수행하면 된다.  
    * 이는 또다른 MacroCommand 클래스의 ```execute```가 될 수 있다.  
* ```append```를 통해 명령을 수행할 때마다 ```commands``` 필드에 다른 Command 인스턴스를 추가한다.  
    * 자신 인스턴스를 넣으면 무한루프가 발생하기에, if 문으로 막아주고 있다.  
* ```undo``` 메소드를 통해 ```commands```의 마지막 명령을 삭제한다.  
    * ```java.util.Stack```의 ```pop```을 이용한다.  

### DrawCommand 클래스   

* 점 그리기 명령을 표현한 클래스이다.  
* ```drawable``` 필드와 ```position``` 이라는 필드를 가지고 있다.  
    * 각각 그리기를 실행하는 대상과, 그림을 그릴 위치를 나타내고 있다.  
* 생성자에서는 Drawable 인터페이스를 구현한 클래스의 인스턴스와 Point 클래스의 인스턴스를 인수로 전달해서 필드에 대입한다.(명령을 만든다.) ([java.awt.*](https://github.com/yeoseon/tip-archive/issues/72) 참고)
* ```execute``` 메소드에서는 ```drawable``` 필드의 ```draw``` 메소드를 호출하고 있다. (명령을 실행하고 있다.)

### Drawable 인터페이스  

* 그림 그리는 대상을 표현한다.  

### DrawCanvas 클래스  

* Drawable 클래스를 구현한다.  
* [java.awt.Canvas](https://github.com/yeoseon/tip-archive/issues/72)의 하위 클래스이다.  

### Main 클래스  

* MacroCommand 클래스 형으로 선언한 ```history``` 필드를 통해 그림 그리기의 이력을 저장한다.  

## 정리

### Command  

명령의 인터페이스를 정의한다.  
예제 1의 Command 인터페이스가 해당된다.  

### ConcreteCommand 

예제 1의 MacroCommand와 DrawCommand가 해당된다.  

### Receiver  

Command 역할이 명령을 실행할 때 대상이 되는 역할  
명령을 받아들이는 수신가좌 같다.  
예제 1의 DrawCanvas에 해당된다.  

### Client 

ConcreteCommand 역할을 생성하고, 그 사이에 Receiver 역할을 할당한다.  
예제 1의 Main 클래스가 해당된다.  

### Invoker


명령의 행동을 개시한다.  
Command 역할에서 정의되는 인터페이스를 호출하는 역할이 된다.  
예제 1의 Main 클래스와 DrawCanvas 클래스가 이 역할을 수행한다.  
이 두개의 클래스가 Command 인터페이스의 ```execute``` 메소드를 호출하고 있다.  
Main 클래스는 Client 역할과 Invoker 역할 둘 모두를 수행한다.  

## Hint

### 명령이 가지고 있어야 할 정보는?  

예제 1의 DrawCommand가 점의 위치뿐만 아니라 '이벤트가 발생한 시간' 이라는 정보를 가지고 있다면 사용자의 마우스 동작의 완급 기능도 구현할 수 있을 지 모른다.  

그림을 그리는 대상의 필드(```drawable```)도 가지고 있다.  
그림 그리기 대상이 여러 개 존재하는 프로그램을 구현할 경우에는, 자신이 Receiver를 알고 있기 때문에 ConcreteCommand 역할을 누가 관리하고 누가 가지고 있어도 execute 할 수 있다.  

### 이력의 저장  

MacroCommand의 인스턴스를 통해 그림 그리기의 이력을 가지고 있다.  
이것은 이 인스턴스를 파일로 잘 저장하면 그림그리기의 이력이 저장된다는 의미이다.  

### 어댑터  

프로그래밍을 간결하게 하기 위해 **어댑터**라는 클래스들이 ```java.awt.event``` 패키지에 준비되어 있다.  
이 어댑터 클래스의 메소드를 사용하기 위해서는, 실제로 사용하지 않을 메소드까지 오버라이드 해두고 로직 없이 냅두어야 한다. 즉 거짓으로 기술해야한다.  

[Java의 익명 내부 클래스](https://github.com/yeoseon/tip-archive/issues/73)를 이용한다면 내가 사용할 메소드만 오버라이드해서 깔끔하게 구현할 수 있다.  

### 관련 패턴

* Composite 패턴  
Macro Command를 실형하기 위해 Composite 패턴이 이용되기도 한다.  

* Memento 패턴  
Command 역할의 이력을 저장하는 경우에 Memento 패턴이 이용되기도 한다.  

* Prototype 패턴  
발생한 이벤트를 복제하는 경우에 Prototype 패턴이 이용되기도 한다.  
