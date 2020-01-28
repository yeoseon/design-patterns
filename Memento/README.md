# Memento  


**undo**의 기능을 실행하기 위해서는, 인스턴스가 가지고 있는 정보를 저장해둘 필요가 있다.  
단 저장만 해서는 쓸모가 없고, 저장한 정보로부터 인스턴스를 원래의 상태로 되돌려야 한다.  
인스턴스를 복원하기 위해서는, 인스턴스 내부의 정보를 자유롭게 액세스 할 수 있어야 한다.  
그러나 원치 않는 액세스를 허용하면, 클래스 내부 코드가 프로그램에 여기저기로 흝어져 캡슐화의 파괴를 일으킬 가능성이 있다.  

**인스턴스의 상태를 나타내는 역할을 도입해서 **캡슐화의 파괴에 빠지지 않고** 저장과 복원을 실행하는 것**  

Memento 패턴을 이용하면 **undo(실행취소)**, **redo(재실행)**, **history(작업 이력의 작성)**, **snapshot(현재 상태의 저장)** 을 실행할 수 있다.  

## 예제 1

**과일을 모으는 주사위 게임**  

돈이 모였을 때 미래를 위해 Memento 클래스의 인스턴스를 만들고 **현재 상태**를 저장한다.  
계속 져서 돈이 줄어들면 돈이 전부 없어져 종료되기 전에 저장해둔 Memento의 인스턴스를 사용해 **이전의 상태로 복원**한다.  

* Memento 클래스  
    * 주인공(Gamer)의 상태를 표현하는 클래스  
    * Memento 클래스와 Gamer 클래스는 공통 game 패키지에두도록 한다.  
    * ```money```와 ```fruit``` 필드를 갖는다. 현재 가지고 있는 돈과 과일을 나타낸다.  
        * 같은 패키지 내의 Gamer 클래스로부터 필드에 자유롭게 액세스 할 수 있도록 private으로 관리하지 않는다.  
    * Memento 생성자에는 public이 없다. -> Memento의 인스턴스는 같은 패키지에 속해있는 클래스에서만 사용할 수 있다.(wide interface)  
        * Gamer에서 Memento의 인스턴스를 생성한다. 
    * game 패키지의 외부에서는 Memento 내부를 변경할 수 없다. 
        * wide interface와 narrow interface  
* Gamer 클래스 
    * 게임을 실행하는 주인공을 표현한다. 
    * ```money```, ```fruit```, ```random```(난수발생기)를 가지고 있다. 
    * ```bet``` 메소드를 통해 파산하지 않는 한 주사위를 던져 나온 수에 따라 소지금이나 과일의 개수를 변화시킨다.  
    * ```createMemento()``` 메소드를 통해 현재의 상태를 저장한다.(스냅샷을 찍는다.)
        * Memento의 작성을 수행한다. 
        * 현 시점에서의 소지금과 과일을 기초로 Memento의 인스턴스를 한 개 만들고 있다.
        * 이렇게 만들어진 Memento 인스턴스가 **현재의 Gamer 인스턴스의 상태**를 표현하고 있다.  
        * 이것이 ```createMemento```의 반환값이 된다.  
    ```
    public Memento createMemento() {
        Memento m = new Memento(money);
        Iterator it = fruits.iterator();
        while(it.hasNext()) {
            String f = (String)it.next();
            if(f.startsWith("맛있는")) {
                m.addFruit(f);
            }
        }
        return m;
    }
    ```
    * ```restoreMemento```는 ```createMemento``` 메소드의 반대로 undo를 실행한다.  
        * 제공된 Memento 인스턴스를 기초로 자신의 상태를 복원한다.
        * 게임에서 '부활의 주문'을 외치는 것과 같다. 
* Main 클래스
    * Gamer의 인스턴스를 작성하고 이를 이용해 게임을 실행한다.  
    * 변수 memento에는 '어떤 시점의 Gamer의 상태'가 저장되어 있다.
        * 운 좋게 소지금이 증가하면 ```createMemento```를 사용해서 현재의 상태를 저장한다.
        * 소지금이 부족하면 ```restoreMemento```에게 이 memento를 제공해서 소지금을 원래 상태로 되돌린다.  
    ```
    Gamer gamer = new Gamer(100); // 최초의 소지금 100
    Memento memento = gamer.createMemento(); // 최초 상태 저장
    ```

## 정리

### Originator(작성자)  
자신의 현재 상태를 저장하고 싶을 때 Memento 역할을 만든다.  
이전의 Memento 역할을 전달받으면 그 Memento 역할을 만든 시점의 상태로 돌리는 처리를 실행햔다.  
예제 1의 Gamer 클래스가 해당된다.  

### Memento(기념품)
Originator 역할의 내부 정보를 정리한다.  
Originator의 내부 정보를 가지고 있지만, 그 정보를 누구에게도 공개하지 않는다.  
* wide interface - 넓은 인터페이스  
오브젝트의 상태를 원래로 되돌리기 위해 필요한 정보룰 **모두** 얻을 수 있는 집합. Memento 역할의 내부 상태를 들어내기 때문에 이것을 사용하는 것은 Originator 역할 뿐이다.  
* narrow interface - 좁은 인터페이스  
외부의 Caretaker 역할에게 보여주는 것  
이 인터페이스로 할 수 있는 일에는 한계가 있고, 내부 상태가 외부에 공개되는 것을 방지한다.  

이 두 종류의 API를 잘 구분해서 사용하면 **오브젝트 캡슐과 파괴를 방지**할 수 있다.  
예제 1의 Memento 클래스가 해당된다.  

### Caretaker(관리인)
현재의 Originator의 상태를 저장하고 싶을 대, 그것을 Originator에 전한다.  
Originator는 그것을 받아서 Memento 역할을 만들어 Caretaker에게 전달한다.  
Caretaker 역할은 미래의 필요에 대비해서 Memento 역할을 저장한다.  
예제 1의 Main 클래스가 해당된다.  

Caretaker 역할은 Memento의 narrow interface만 사용할 수 있어서, Memento 내부 정보에 액세스 할 수 없다.  
단 만들어준 Memento를 한 덩어리의 블랙박스로서 통째로 저장해둘 뿐이다.  

> Originator 와 Memento 는 강하게 연결되어 있지만,  
> Caretaker 와 Memento 는 유연하게 연결되어 있다.  
> Memento 는 Caretaker 에 대해서 정보은폐 기능을 하고 있다.  

## Hint

### 두 개의 Interface와 액세스 제어  

Java의 액세스 제어  
* public : 모든 클래스에서 보인다.  
* protected : 그 패키지 및 하위 클래스에서 보인다.  
* 없음 : 그 패키지에서만 보인다. 
* private : 그 클래스에서만 보인다.  

narrow interface : 내부 상태를 조작할 수 있는 정보다 **적다**는 뜻  

Main 클래스에서 다음과 같이 Memento 생성자를 사용하면 컴파일 오류가 난다.  
```
new Memento(100)
```
즉 Memento의 인스턴스를 만들 수 없다는 의미  

**Main 클래스는 Memento의 인스턴스가 필요할 때 ```createMemento()```를 호출해서 '현재의 상태를 저장하고 싶으니까 Memento 인스턴스를 만들어줘' 라고 Gamer 클래스에게 부탁하는 것이다.**  

> 액세스 제어를 이용하면 **이 클래스에서는 이 메소드가 보이지만, 저 클래스에서는 보이지 않는다**는 것을 표현할 수 있다.  

### 복수의 Memento 

Main 클래스가 배열을 사용해서 Memento 인스턴스를 여러 개 갖도록 하면 **여러 시점**에서의 상태를 저장할 수 있다.  

### Memento의 유효기간  

예제 1과 같이 메모리상에서만 Memento를 저장해둘 경우네느 문제가 없지만, **파일**로 저장할 경우에는 **유효기간**이 문제가 된다.  

어느 시점의 Memento를 파일로 저장해두더라도, 그 후 프로그램이 버전 업되면 파일로서 저장되어 있는 Memento와 **모순이 발생할 우려가 있다.** 

### Caretaker와 Originator를 분리하는 이유  

> undo 기능을 만들고 싶으면, Originator에 구현하면 되지 왜 일부러 이 패턴을 사용할까?  

Caretaker는 **어느 시점에서 스냅샷을 찍을지 결정**하고 **언제 undo할 지를 결정**하고 Memento 역할을 저장한다.  
Originator 역할은 Memento 역할을 **만드는 일**과 제공된 Memento를 사용해서 **자신의 상태를 원래의 상태로 되돌리는** 역할을 수행한다.  

이와 같이 역할 분담을 수행하도록 만들면 

> 여러 단계의 undo를 실행하도록 변경하고 싶다.   
> undo 기능 뿐만 아니라, 현재 상태를 파일에 저장하고 싶다.  

라는 수정을 할 때에도 Originator 역할을 변경할 필요가 없다.  


### 관련 패턴

* Command 패턴  
Command 패턴을 사용해서 명령을 처리하는 경우, Memento 패턴을 사용해서 undo나 redo를 실행할 수 있다.  
* Prototype 패턴  
Memento 패턴에서는 현재의 인스턴스 상태를 저장한다.  
저장을 하는 것은 복원을 위해 필요한 정보뿐이다.  
Prototype 패턴에서는 현재의 인스턴스와 동일한 상태의 다른 인스턴스를 만들고, 인스턴스의 내용은 전부 복사된다.  
* State 패턴  
Memento 패턴에서는 **인스턴스**가 **상태**를 표현한다.  
State 패턴에서는 **클래스**가 **상태**를 표현한다.  