# Visitor  

**데이터 구조와 처리의 분리**  
데이터 구조 안을 돌아다니는 주체인 '방문자'를 나타내는 클래스를 준비해서, 그 클래스에게 **처리를 위임**한다.  
새로운 처리를 추가하고 싶을 때에는 새로운 '방문자'를 만든다.  
데이터 구조는 문을 두드리는 '방문자'를 받아들인다.  


## 예제 1

**Composite 패턴에서 사용한 파일/디렉터리 구조**  

* Visitor 클래스  
    * '방문자'를 나타내는 **추상** 클래스
    * 방문할 곳의 데이터 구조 (즉, File과 Directory)에 의존한다.  
    * ```visit()```라는 메소드를 Overloading을 통해 2개 생성한다. 각각 File, Directory를 인수로 가진다.  
* Element 인터페이스  
    * 방문자를 받아들이는 인터페이스  
    * ```accept()```라는 메소드를 선언한다. 인수는 방문자인 Visitor 클래스가 된다.
    ```
    public abstract void accept(Visitor v);
    ```
* Entry 클래스  
    * Entry 클래스를 Visitor 패턴에 적용시키기 위해, Element 클래스를 **구현(implements)**하고 있다.  
    * Element의 ```accept()``` 메소드를 실제로 구현하는 것은, Entry의 하위클래스인 File과 Directory 클래스가 될 것이다.  
    * Directory에서만 사용하는 ```add()``` 메소드와 ```iterator()``` 메소드는 Entry 클래스에서는 Exception 처리하고, Directory 클래스 내에서 사용하도록 처리할 것이다. ([Composite 패턴의 add() 처리 참고](../Composite))
* File 클래스
    * Element의 추상 메소드인 ```accept``` 메소드를 구현한다. 
        * 이의 인수는 Visitor 클래스이다.
        * ```accept``` 메소드 안에서 ```v.visit(this);```를 통해 Visitor의 ```visit()``` 메소드를 호출한다. (오버로드 되어있는 두 개의 ```visit()``` 메소드 중, File을 인수로 가지고 있는 메소드가 호출될 것이다.)
        * ```visit()``` 메소드를 호출해서, File의 인스턴스(```this```)를 Visitor에게 가르쳐 준다.
* Directory 클래스 
    * 역시 Element의 추상 메소드 ```accept``` 메소드를 구현한다.  
    * 추가적으로 ```iterator```와 ```add``` 메소드 또한 구현하고 있다.
    * ```iterator``` 메소드를 디렉터리에 포함되어 있는 엔트리(파일이나 디렉터리)의 종류를 얻기 위한 Iterator를 반환한다.   
    ```
    private ArrayList dir = new ArrayList();
    ...
    public Iterator iterator() {
        return dir.iterator();
    }
    ```
    * ```accept``` 메소드에서는 File 클래스에서와 같이 ```this```를 통해 현재 방문한 것은 어떤 Directory 인스턴스 인지 알려주고 있다.  
* ListVisitor 클래스
    * Visitor의 하위클래스이다. (```extends```)
    * 데이터 구조를 돌아다니면서 종류를 표시하기 위한 클래스이다. 
    * Visitor 클래스의 ```visit(File)``` 메소드와 ```visit(Directory)``` 메소드를 구현하고 있다.
    * ```currentdir``` 필드를 가지고 있다. 현재 주목하는 디렉터리의 이름을 저장한다.  
    * ```visit(File)``` 메소드를 File 클래스의 ```accept``` 메소드 내에서 호출되며, 인수 file은 현재 방문하고 있는 File 클래스의 인스턴스이다.
        > ```visit(File)``` 메소드에서는 **File 클래스의 인스턴스에 대해서 실행해야 할 처리**를 기술하고 있다.
    * ```visit(Directory)``` 메소드도 마찬가지로, Directory의 ```accept``` 메소드를 통해 호출되며, 인수 directory는 현재 방문하고 있는 Directory 클래스의 인스턴스이다. 
        > ```visit(Directory)``` 메소드에서는 **Directory 클래스의 인스턴스에 대해서 실행해야 할 처리**를 기술하고 있다.  

        * ```visit(File)```과 동일하게 디렉터리를 표시한 후, 디렉터리의 Iterator를 iterator 메소드에서 취득하고, **그** 디렉터리의 **각** 엔트리에 대해서 ```accept``` 메소드를 호출한다. 디렉터리 내에는 많은 디렉터리 엔트리가 있으므로, 그 각각을 방문하게 될 것이다.  
    * **```accept()``` 메소드를 ```visit``` 메소드를 호출하고, ```visit``` 메소드는 ```accept``` 메소드를 호출한다.**
        > 어떤 메소드가 자기 자신을 호출하는 것은 재귀적인 호출이지만  
        > Visitor 패턴에서는 ```visit``` 메소드와 ```accept``` 메소드가 **서로 상대를 호출**하는 것이다. 
* Main 클래스
    * 표시를 행하는 방문자인 ListVisitor의 인스턴스를 사용한다.  
    * Composite 패턴에서는 디렉터리를 표시하기 위해 ```printList```를 사용했다. 이 메소드는 Directory 클래스 내에서 구현했다.
        > Visitor 패턴에서는 디렉터리를 표시하는 것도 Visitor 클래스에서 실행한다.  
        디렉터리의 표시도 **데이터 구조 안의 각 요소에 대하여 실행하는 처리**이기 떄문이다.  

* Vistor 쪽과 Element 쪽의 상호 호출 (책의 시퀀스 다이어그램 참고)
    1. Main 클래스가 ListVisitor 인스턴스를 만든다.  
    2. Main은 Directory 인스턴스에 대해서 ```accept``` 메소드를 호출한다.  
    3. Directory의 인스턴스는 인수로 전달된 ListVisitor의 ```visit(Directory)``` 메소드를 호출한다.  
    4. 그러면 ListVisitor의 인스턴스는 그 디렉터리 안을 조사해서 최초의 파일의 ```accept``` 메소드를 호출한다. 인수에는 ```this```, 자기 자신을 전달한다.  
    5. File의 인스턴스는 인수로 전달된 ListVisitor의 ```visit(File)``` 메소드를 호출한다. 이 때, ListVisitor는 ```visit(Directory)```를 실행 중인 점에 유의한다. (멀티스레드가 되고 있다는 것이 아니라, 콜 스택내에 ```visit(Directory)```가 있다는 말.)  
    [콜 스택](https://github.com/yeoseon/tip-archive/issues/27)  
    [멀티 스레드](https://github.com/yeoseon/tip-archive/issues/28)  
    6. ```visit(File)```와 ```accept```에서 반환되면, 이번에는 다른 File의 인스턴스 (동일한 디렉터리의 2번째 파일)의 ```accept``` 메소드를 호출한다. 인수로는 ListVisitor의 인스턴스(```this```)를 전달한다.  
    7. 이전과 동일하게 File 인스턴스를 ```visit(File)``` 메소드를 호출한다. 각 메소드 처리가 끝나면 호출한 곳으로 점점 되돌아가서 마지막에는 Main의 ```accept``` 메소드의 호출로 되돌아온다.  
* 핵심 정리  
    > Directory의 인스턴스나 File의 인스턴스에 대해서는 ```accept``` 메소드가 호출된다.  

    > ```accept``` 메소드는 각 인스턴스에서 한번만 호출된다.  

    > ListVisitor의 인스턴스에 대해서는 ```visit(Directory)```나 ```visit(File)``` 메소드가 호출된다.  

    > ```visit(Directory)```나 ```visit(File)```를 처리하고 있는 것은 하나의 ListVisitor 인스턴스이다.  


## 정리

### Visitor (방문자)

데이터 구조의 구체적인 요소(ConcreteElement)마다 'xxx를 방문했다' 라는 ```visit(xxx)``` 메소드를 선언한다. 이는 **xxx를 처리```하기 위한 메소드이고, 실제 코드는 ConcreteVisitor 역할에 구현되어 있다.  
예제 1의 Visitor 클래스에 해당한다. 

### ConcreteVisitor  

Visitor의 인터페이스를 구현한다.  
```visit(xxx)```라는 형태의 메소드를 구현하고, ```ConcreteElement``` 역할의 처리를 기술한다.  
예제 1의 ListVisitor 클래스에 해당한다.  
ListVisitor 에서 ```currentdir``` 필드값이 계속 변화했 듯이, visit를 처리해 가는 중에 ConcreteVisitor 역할의 내부 상태가 변화하는 일도 있다.  

### Element(요소)

Element는 Visitor 역할의 방문할 곳을 나타내는 역할로, 방문자를 받아들이는 ```accept``` apthemfmf tjsdjsgksek.  
이 메소드 인수에는 Visitor 역할이 전달된다.  
예제 1의 Element 인터페이스에 해당된다.  

### ConcreteElement

Element의 인터페이스를 구현한다.  
예제 1의 File이나 Directory 클래스에 해당된다.  

### ObjectStructure

Element 역할의 집합을 취급한다.  
ConcreteVisitor 역할이 각각 Element 역할을 취급할 수 있는 메소드를 구비하고 있다.  
예제 1의 Directory 클래스에 해당된다. (1인 2역)  
ConcreteVisitor 역할이 각각의 Element 역할을 취급할 수 있느 것처럼, Directory 클래스에는 ```iterator``` 메소드가 준비되어 있다.  

## Hint

### 더블 디스패치

[더블 디스패치] (https://github.com/yeoseon/tip-archive/issues/29)  

Visitor 패턴에서의 메소드 호출  
```
element.accept(Visitor)
```
```
visitor.visit(element)
```
정확히 반대의 관계에 있다. **element는 visitor를 accept 하고, visitor는 element를 visit하고 있다.**

> Visitor 패턴에서는 ConcreteElement의 역할과 ConcreteVisitor의 역할을 하는 **한 쌍**에 의해 실제 처리가 결정된다.  
이를 **더블 디스패치 (이중분리)**라고 한다.  

### 왜 사용하는가?

반복 처리가 필요하면, 데이터 구조 안에 루프를 사용하면 되지 않나??  

> Visitor 패턴의 목적은 **처리를 데이터 구조에서 분리하는 일** 이다.  

데이터 구조는 요소를 집합으로 정리하거나, 요소 사이를 연결해주는 중요한 역할을 수행한다.  
> 그러나 구조를 유지하는 것과, 구조를 기초로 한 **처리**를 기술하는 것과는 별개이다.  

예제 1에서는 '종류 표시'라는 **처리**를 실행하는 ConcreteVisitor의 역할로 ListVisitor 클래스를 만들었다. 또 다른 처리가 필요하면 또 다른 ConcreteVisitor 역할의 클래스를 만들게 된다.  
> ConcreteVisitor 역할의 클래스는 File 클래스나 Directory 클래스와는 독립적으로 개발할 수 있다.  
즉, **Visitor 패턴은 File / Directory 클래스의 "부품으로서의 독립성""을 높일 수 있다.**  

처리 내용을 File / Directory 클래스 내에 직접 구현하면, 새로운 '처리'를 추가해서 기능 확장을 할 때마다 File / Directory 클래스를 수정해야 한다. 

### The Open-Closed Principle :: 확장에 대해서는 열고, 수정에 대해서는 닫는다.

[개방-폐쇄 원칙](https://github.com/yeoseon/tip-archive/issues/30)

> 확장에 대해서는 열려있지만, 수정에 대해서는 닫혀있어야 한다.  

클래스를 설계할 때, 특별한 이유가 없는 한 **확장을 허용**해야 한다.  
그러나 확장을 할 때마다 기존의 클래스를 수정해야 하는 것도 곤란한다. **기존의 클래스는 수정할 필요가 없도록** 해야한다.  

클래스에 대한 요구는 빈번하게 변화한다. 그 요구는 대부분 **기능을 확장하고 싶은** 경우이므로 기능 확장을 할 수 없다면 곤한할 것이다.  
한편으로는 이미 완성되어 테스트까지 마친 클래스를 수정한다면, SW의 품질을 떨어뜨릴 위험이 있을 것이다.  

확장에 대해서는 열려있고, 수정에 대해서는 닫혀 있는 클래스가 **부품으로써 재이용 가치가 높은 클래스이다.**  

> 디자인 패턴의 목적, 객체 지향의 목적이란 바로 **이러한 클래스를 만들 수 있는 구조**를 제공하는 것이다.  

### ConcreteVisitor 역할의 추가는 간단하다. 

데이터 구조에 대한 처리를 추가하고 싶은 경우, ConcreteElement 역할의 수정없이 ConcreteVisitor 역할을 하나 더 추가하면 된다.  

### ConcreteElement 역할의 추가는 곤란하다.  

Entry 클래스에서 하위 클래스로 Device 클래스를 추가한다고 가정하자.  
Device 클래스는 File과 Directory 클래스의 형제에 해당한다.  
이때, Visitor 클래스에서는 ```visit(Device)``` 메소드를 만들 필요성이 생긴다.  
그리고 Visitor 클래스의 하위 클래스 전부에 새로운  ```visit(Device)``` 메소드를 구현해야 한다.  

### Visitor가 처리하기 위해서 필요한 것  

> Visitor 패턴에서는 데이터 구조의 요소에 대한 처리를 따로 분리해서 Visitor 역할에게 맡긴다.  
**Element의 역할을 Visitor 역할에 대해서 충분한 정보를 공개할 필요가 있다.**  

예제 1에서 Directory의 안에 각각의 디렉터리 엔트리에 대해 accept를 실행하고 있다.  
이와 같은 처리를 실현하기 위해서는 Directory가 '각각의 디렉터리 엔트리를 얻기위한' ```iterator``` 메소드를 제공할 필요가 있다.  

방문자는 데이터 구조에서 필요한 정보를 취득해서 동작한다.  
필요한 정보를 얻을 수 없으면 제대로 일할 수 없으며, 공개할 필요 없는 정보까지 공개하면 미래에 데이터 구조를 개량하기 어렵게 된다.  

### 관련 패턴

* Iterator 패턴  
두 패턴 모두 어떤 데이터 구조 상에서 처리를 실행한다.  
Iterator 패턴은 데이터 구조가 지니고 있는 요소를 하나씩 **얻는 데** 사용된다.  
Visitor 패턴은 해당 요소에 **특정한 처리**를 실행하는 데 사용된다.    
* Composite 패턴  
방문처가 되는 데이터 구조는 Composite 패턴이 되는 경우가 있다. 
* Interpreter 패턴
Interpreter 패턴에서 Visitor 패턴이 사용되는 경우가 있다.  
예를 들어, 구문 트리를 만든 후, 구문 트리의 각 노드를 순회하면서 처리를 실행하는 경우이다.  