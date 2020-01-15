# Decrator  

장식(기능)과 내용물(오브젝트)을 동일시한다.  
오브젝트에 장식을 해 나가는(기능을 더해가는) 디자인 패턴  

## 예제 1

**문자열 주변에 장식을 만들어서 표시하는 것**  

* Display 클래스  
    * 복수 행으로 구성되는 문자열을 표시하기 위한 추상 클래스  
    * ```getColumn()```, ```getRows()```, ```getRowText()``` 의 추상 메소드를 정의한다.  
    * **Template 패턴**을 이용해 위의 추상 클래스를 이용해 모든 행을 표시하는 메소드를 구현한  ```show()``` 메소드도 있다.
* StringDisplay 클래스
    * Display의 하위클래스. 추상 메소드를 구현할 책임이 있다.  
    * 생일 케이크의 중심에 있는 스펀지 케이크와 같은 존재로서 가장 기본 형태의 로직이 구현되어 있다.  
* Border 클래스  
    * Display의 하위클래스. 이 또한 추상 클래스이다.  
    * Display의 추상 메소드들을 구현할 책임은 Border 클래스의 하위 클래스에게 넘긴다.  
    * 상속에 의해 장식(Border)은 내용물(Display)과 동일한 매소드를 갖는다.  
    -> **인터페이스 적으로 장식과 내용물을 동일시할 수 있다.**
    * Display형의 display 필드를 가지고 있다. (**내가 어떤 오브젝트를 꾸밀 것인지를 알고 있다는 뜻**) 이 것이 장식으로 싸여있는 내용물이다.  
    -> 하지만, 이 display가 StringDisplay라고 단정지을 수 없다. 또다른 Border가 될 수 있다.  
* SideBorder, FullBorder 클래스
    * Border의 하위 클래스로, Border가 가지는 Display의 추상 메소드 구현 책임을 맡는다.  
    -> ```getColumn()```, ```getRows()```, ```getRowText()```를 구현한다.
* Main 클래스
    ```
    Display b1 = new StringDisplay("Hello, World!");
    Display b2 = new SideBorder(b1, '#');
    Display b3 = new FullBorder(b2);
    Display b4 = new SideBorder(
                    new FullBorder(
                        new FullBorder(
                            new SideBorder(
                                new FullBorder(
                                    new StringDiaplay("안녕하세요.)
                                ),
                                '*'
                            )
                        )
                    ),
                    '/'
                );
    ```
    다음과 같이 Display 형의 인스턴스를 생성하여 사용한다.

## 정리

### Component

기능을 추가할 때 핵심이 되는 역할이다.  
케이크의 장식하기 전 추상적인 스펀지 케이크의 역할에 해당된다.  
스펀지 케이스의 인터페이스 (API)만을 결정한다.  
예제 1의 Display 클래스가 해당 역할이다.  

### ConcreteComponent

구체적인 스펀지케이크.  
Component 역할의 API를 실제로 구현한다.  
예제 1의 StringDisplay에 해당한다.  

### Decorator (장식자)

Component 역할과 동일한 인터페이스(API)를 가지며, 이 Decorator 역할이 장식할 대상이 되는 Component 역할도 가지고 있다.  
**자신이 장식하고 있는 대상을 알고 있다. (필드로서 가지고 있다.)**  
예제 1의 Border 클래스에 해당한다.  

### ConcreteDecorator (구체적인 장식자)

예제 1의 SideBorder, FullBorder에 해당한다.  

## Hint

### 투과적인 인터페이스(API)
  
Decorator 패턴에서는 **장식과 내용물**을 동일시하고 있다.  
> 즉, 예제 1의 Border와 Display 클래스와 동일한 인터페이스(API)를 가진다.

> 장식을 사용해서 내용물을 감싸도 인터페이스(API)는 전혀 감출 수 없다.  
> 이를 **인터페이스(API)가 투과적** 이라고 한다.

예제 1의 ```getRows```, ```getRowText```, ```show``` 라는 메소드는, 감추어지는 일이 없이 다른 클래스에서 볼 수 있다.  
인터페이스가 투과적이기 때문에, Composite 패턴과 같은 **재귀적인 구조**가 등장한다.  
장식이 둘러싸고 있는 내용물이 실제로는 다른 장식이 되는 구조이다.  

> Composite 패턴과 재귀적인 구조를 취하는 점에서 유사하지만, **목적이 다르다.**  
> **Decorator 패턴은 테두리 장식을 중복해서 기능을 추가해가는 점에 주안점을 둔다.** 

### 내용물을 바꾸지 않고 기능을 추가할 수 있다.

장식과 내용물이 공통적인 인터페이스(API)를 가진다.  
인터페이스는 공통이지만, 장식하면 장식할 수록 기능이 추가된다.  
> 이 때, 장식되는 상대(내용물)은 수정할 필요가 없다.  
> 내용물을 변경하지 않고 기능을 추가할 수 있다.  

Decorator 패턴에서는 **위임**을 사용하고 있다.   
'장식'에 대한 요구(메소드의 호출)은 '내용물'에 **위임**된다.  
예제 1의 SideBorder의 ```getColumns``` 메소드 안에서 ```display.getColumns()```를 호출하며, ```getRows``` 메소드에서는 ```display.getRows()```를 호출하고 있다.  

### 동적인 기능을 추가할 수 있다.    

**위임**은 클래스 사이를 느슨하게 결합한다.  
따라서 framework의 소스를 변경하지 않고, 오브젝트의 관계를 변경한 새로운 오브젝트를 만들 수 있다.  

### 단순한 장식이라도 다양한 기능을 추가할 수 있다.  
구체적인 장식(ConcreteDecorator)만 많이 준비해주면, 그것을 자유롭게 조합해서 새로운 오브젝트를 만들 수 있다.  
> 장식은 단순해도 상관 없다.  

손님이 주문할지 안할지도 모르는 장식이 올려진 아이스크림을 만들어두는 것보다, 아이스크림과 장식을 여러개 준비해두면 된다.  

### java.io 에 구현된 Decorator 패턴의 예  
파일을 읽어들인다.  
``` 
Reader reader = new FileReader("datafile.txt");
```
파일을 읽어들일 때 버퍼링이 실행된다.  
```
Reader reader = new BufferReader(
                    new FileReader("datafile.txt")
                );
```
파일을 읽어들일 때, 버퍼링이 실행되는 기능에 행번호를 관리하는 기능을 추가하고 싶다.  
```
Reader reader = new LineNumberReader (
                    new BufferedReader (
                        new FileReader("datafile.txt")
                    )
                );
```
행번호는 관리하지만, 버퍼링은 실행하지 않고 싶다.  
```
Reader reader = new LineNumberReader(
                    new FileReader("datefile.txt")
                );
```
행번호를 관리하며 버퍼링을 실행하지만, 파일을 네트워크를 통해 읽어들이고 싶다.  
``` 
java.net.Socket socker = new Socker(hostname, portnumber);
...
Reader reader = new LineNumberReader(
                    new BufferReader(
                        new InputStreamReader (
                            socker.getInputStream()
                        )
                    )
                );
```
> 여기서 사용되는 InputStreamReader 클래스는 getInputStream의 반환값으로 얻어지는 InputStream 클래스의 인스턴스를 기초로 Reader 클래스의 인터페이스(API)를 제공하기 위한 것이다.  
> 이것은 **Adapter 패턴**에 해당한다.

### 단점 : 작은 클래스가 증가한다.  
매우 유사한 성질의 작은 클래스가 많이 만들어지는 결점이 존재한다.  

### 관련 패턴

* Adaptor 패턴  
Decorator 패턴은 내용물의 인터페이스를 변경하지 않고 장식을 만든다. (투과)  
Adaptor 패턴은 다른 두 개의 인터페이스를 연결하기 위해 사용한다.  
* Strategy 패턴  
Decorator 패턴은 장식을 교체하거나 테두리 장식을 겹쳐서 기능을 **추가**한다.  
Strategy 패턴은 알고리즘을 교체해서 기능을 **변경**한다.  

### 추가 :: 상속의 동일시와 위임의 동일시

[상속과 위임](../../../../tip-archive/issues/25) 참고  

1. 상속 - 하위클래스와 상위클래스를 동일시 한다. 
``` 
class Parent {
    ...
    void parentMethod() {
        ...
    }
}
```
```
class Cuild extends Parent {
    ...
    void childMethod() {
        ...
    }
}
```

이 때, Child의 인스턴스를 Parent 형의 변수 형에 **그대로 대입할 수 있다.**  
그리고 Parent에서 상속하는 메소드를 그대로 호출할 수 있다.  
```
Parent obj = new Child();
obj.parentMethod();
```
즉, Child의 인스턴스를 마치 Paren의 인스턴스처럼 취급하고 있다. 이는 **하위클래스를 상위 클래스로 간주하고 있는 것이다.**  

위와 반대로, **상위 클래스를 하위클래스로 간주**하기 위해서는, Cast가 필요하다.  
``` 
Parent obj = new Child();
((Child)obj).childMethod();
```

2. 위임 : 자신과 위임할 곳을 동일시 하기  
위임을 사용해서 인터페이스가 **투과**적으로 되어 있을 경우에는 자신과 위임할 곳을 동일시한다.  
```
class Rose {
    Violet obj = ...
    void method() {
        obj.method();
    }
}
```
```
class Violet {
    void method() {
        ...
    }
}
```

Rose와 Violet은 동일한 method를 가지고 있으며, Rose는 Violet에게 위임하고 있다.  
이 클래스는 공통 메소드인 ```method()```를 가지고 있지만 '공통'이라는 정보가 없다.  
**다음과 같이 공통의 추상 클래스가 있으면 결속이 강해진다.**
```
abstract Class Flower {
    abstract void method();
}
```
(위의 두 메소드에는 ```extends Flower```가 추가된다.)  

혹은 Interface일 수도 있다.
```
Interface Flower {
    abstract void mathod();
}
```
(위의 두 메소드에는 ```implements Flower```가 추가된다.)  

