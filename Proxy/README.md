# Proxy  

**대리인**   
본인이 아니라도 가능한 일을 맡기기 위해 대리인을 세운다.  
대리인이 할 수 있는 일에는 한계가 있어, 한계를 넘을 경우 대리인은 본인에게 와서 상담을 한다.  


## 예제 1

**이름있는 프린터**  

Main 클래스가 대리인의 역할을 하는 PrinterProxy 클래스의 인스턴스를 생성하여 프린터의 기능을 호출한다.(이 때 본인의 역할을 하는 Printer 클래스의 인스턴스를 생성되지 않았다.)  
Main 에서는 Printer 클래스 인스턴스를 생성하는 일 없이 PrinterProxy의 메소드만 호출한다.  
PrinterProxy 인스턴스를 자신이 가능한 일까지는 수행하다가, 불가능한 기능이 호출되면 ```realize```라는 메소드를 통해 본인인 Printer 클래스의 인스턴스를 이제 생성한다.  
이런 구조를 위해 Printer 클래스와 PrinterProxy 클래스는 Printable 인터페이스를 공동으로 구현하고 있다.  

### Printer 

* '본인'을 표시하는 클래스  
* 이 클래스를 생성하는 생성자에서는 **HeavyJob**을 실행하고 있다고 가정하자. (**후에 Hint에서 Proxy 패턴을 사용하는 이유** 참고)  
* ```getPrinterName```과 ```setPrinterName```, ```print``` 메소드를 구현하고 있다.(Printer 인터페이스의 메소드)  

### Printable 인터페이스  

* Printer와 PrinterProxy를 **동일시**하기 위한 인터페이스이다.  
* ```getPrinterName```, ```setPrinterName```, ```print``` 메소드를 선언하고 있다.  

### PrinterProxy  

* '대리인'의 역할을 하는 클래스  
* ```name``` 필드를 통해 이름을 저장하고, ```real``` 필드를 통해 '본인'을 저장한다.   
* 구현하고 있는 ```print``` 기능은 대리인이 수행할 수 있지 않으므로, ```realize```라는 메소드를 사용하여 '본인'을 **이 때 생성한다.**  
    * 후에 ```real.print```를 통해 print 역할을 **본인에게 위임한다.**  
* **Printer 클래스는 PrinterProxy 클래스의 존재를 모른다.**  
* 하지만 PrinterProxy 클래스는 Printer 클래스의 존재를 알아야 한다.  
    * PrinterProxy 클래스는 Printer 클래스와 깊이 관련된 부품이다.  

### Main

* **PrinterProxy를 경유해서 Printer를 이용한다.**  
* 처음에 PrinterProxy를 생성하고, Printer의 클래스는 생성하지 않는다.  
* ```print``` 메소드를 수행할 때 비로소 내부에서 Printer 인스턴스가 생성되는 것이다.  

## 예제 2   

[Spring에서 AOP를 지원하기 위해 사용하는 패턴](https://github.com/yeoseon/spring-study#%EB%8B%A4%EC%96%91%ED%95%9C-aop-%EA%B5%AC%ED%98%84-%EB%B0%A9%EB%B2%95)  


## 정리

### Subject

Proxy 역할과 RealSubject 역할을 **동일시**하기 위한 인터페이스.  
이 역할이 있는 덕분에 Client는 Proxy와 RealSubject 의 차이를 전혀 몰라도 된다.  
예제 1의 Printable 인터페이스가 해당된다.  

### Proxy

Client 역할의 요구를 **할 수 있는 만큼** 처리한다.  
자신만으로 처리할 수 없으면 RealSubject 역할에게 처리를 **위임한다.**  
즉, 정말로 필요해질 때 RealSubject 인스턴스를 새엇ㅇ한다.  
예제 1의 PrinterProxy 클래스가 해당된다.  

### RealSubject  

대리인이 감당할 수 없는 일이 발생할 때 생성되는 '본인'의 역할이다.  
예제 1의 Printer 클래스가 해당된다.  

## Hint

### 왜 사용하는가? (대리인을 사용해서 **속도 올리기**)  

예제 1 에서 HeavyJob을 Printer의 생성자에 구현한 이유이다.  
Proxy 역할을 사용해서 실제로 print 할 떄까지의 무거운 처리를 지연시킬 수 있었던 것이다.  

**초기화에 시간이 많이 걸리는 기능이 존재하는 대규모 시스템에 적합하다.**  
기동 시점에 이용하지 않는 기능까지 전부 초기화하면, 기동에 시간이 많이 걸린다.  
실제로 그 기능을 사용할 단계가 되어 처음으로 초기화를 하는 것이 스트레스를 줄여주는 방법이 될 것이다.  

### 대리인와 본인을 분리할 필요가 있는가?  

맨날 얘기하잖아. 
> **분할해서 통치하라 ! ! !**  

PrintProxy를 분리함으로써, 대리인이 처리할 수 있는 기능의 한계를 수정하고 싶을 떄, Printer 클래스의 변경 없이 PrinterProxy 클래스만 변경하면 된다.  
지연평가를 실행하고 싶지 않으면, 단지 Main 클래스에서 PrinterProxy를 생성하지 말고 바로 Printer를 생성하면 된다.  
Printable 인터페이스를 구현하고 있기에 가능한 일이다.  

### 대리와 위임  

대리인이 처리할 수 없을 때는 본인에게 '위임'한다.  
현실세계에서는 보통 본인이 대리인에게 책임을 위임하다고 생각하기 때문에 디자인 패턴과 반대라는 것을 느낄 수 있다.  

### 투과적이다.  

예제 1의 PrinterProxy 클래스와 Printer 클래스는 같은 Printable 인터페이스를 구현하고 있다.  
Main 클래스는 Printer를 직접 이용해도, 중간에 PrinterProxy가 들어와도 문제 없이 사용할 수 있다.  

이 경우 PrinterProxy 클래스를 **투과적**이라고 할 수 이싿.  

그림 사이에 투명한 유리판이 놓여있어도 그림을 볼 수 있는 것처럼  
> Main 클래스와 Printer클래스 사이에 PrinterProxy 클래스가 놓여있어도 문제가 되지 않는다.  

### HTTP Proxy  

HTTP 서버(웹 서버)와 HTTP 클라이언트(웹 브라우저) 사이에서 웹 페이지의 캐싱을 실행하는 소프트웨어. 이것도 Proxy 패턴을 적용해서 생각할 수 있다.  

웹 브라우저가 웹 페이지를 표시할 때, 일일이 원격지에 있는 웹 서버에 액세스 해서 페이지를 취득하는 것이 아니고, HTTP Proxy가 중간에 위치해 [캐싱](https://github.com/yeoseon/tip-archive/issues/50)을 해서 어떤 페이지를 **대신** 취득한다.  
최신 정보가 필요하거나 페이지의 유효기간이 지났을 때 웹 서버에 웹 페이지를 가지러 간다.  

여기에서는 웹 브라우저가 Client 역할, HTTP Proxy가 Proxy 역할, 웹 서버가 RealSubject 역할을 수행한다.  

### Proxy 종류  

Proxy 패턴에는 다양한 종류가 있다.  

* Virtual Proxy(가상 프록시)  
이 장에서 소개한 Proxy 패턴이다.  
인스턴스가 필요한 시점에 생성, 초기화를 실행한다.  

* Remote Proxy(원격 프록시)  
RealSubject 역할이 네트워크의 **상대 쪽에 있음에도 불구하고** 마치 자신의 옆에 있는 것처럼(**투과적으로**) 메소드를 호출할 수 있다.  
[Java의 RMI(원격 메소드 호출)](https://github.com/yeoseon/tip-archive/issues/70) 등이 여기에 해당한다.

* Access Proxy  
RealSubject 역할의 기능에 대해서 액세스 제한을 설정한 것이다.  
정해진 사용자이면 메소드 호출을 허가하지만, 그 외에는 에러로 처리하는 Proxy  


### 관련 패턴

* Adapter 패턴  
Adapter패턴은 인터페이스가 서로 다른 오프젝트의 사이를 이어주는 역할을 한다.  
그러나 Proxy 패턴에서 Proxy 클래스와 RealSubject 역할의 인터페이스는 서로 다르지 않다.  

* Decorator 패턴  
구현은 비슷하지만 목적이 다르다.  
Decorator 패턴의 목적은 **새로운 기능을 추가하는 것**이지만, Proxy 패턴의 목적은 새로운 기능을 추가하는 것이기 보다는 본인의 작업을 대리인에게 위임해서 **본인에 대한 액세스**를 줄여주는 것이다.  
