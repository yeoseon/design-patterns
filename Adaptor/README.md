# Adaptor  

'이미 제공되고 있는 것'과 '필요한 것' 사이의 '차이'를 없애주는 패턴
Wrapper 패턴으로 불리기도 한다.  
다음과 같이 두 종류가 있다.

1. 클래스에 의한 Adaptor 패턴 (상속 사용)
2. 인스턴스에 의한 Adaptor 패턴 (위임 사용)

## 예제 1

**상속을 사용한 Adaptor 패턴**  
**이미 제공되어 있는 Banner 클래스를 이용해 Print <u>인터페이스</u>를 충족시키는 PrintBanner 클래스**  
Banner 클래스를 상속 (extends)해서, 필요로 하는 Print 인터페이스를 구현(implements)한다.  

## 예제 2

**위임을 사용한 Adaptor 패턴**  
**이미 제공되어 있는 Banner 클래스를 이용해 Print <u>클래스</u>를 충족시키는 PrintBanner 클래스**  
Print는 인터페이스가 아니고 클래스이다.  
Java에서는 2개의 클래스를 동시에 상속할 수 없기 때문에, PrintBanner 클래스를 Print와 Banner 모두의 하위 클래스로 정의할 수 없다. (단일상속)  
따라서 PrintBanner 클래스는 Banner 클래스의 인스턴스를 'has'한다.  
즉, PrintBanner 클래스의 생성자에서 Banner 인스턴스를 생성한다. 그리고 Print 클래스를 상속한다.  
다음과 같이 Banner 인스턴스를 has 함으로써 필드를 경유해서 호출하고 있다. 즉, 위임을 하고 있다.  

> 두 예제 모두 Main 함수에서는, PrintBanner의 구현과 관계없이, 단지 Print 인터페이스 또는 클래스 변수로 선언하여 사용한다.  
> Banner 클래스나, PrintBanner 클래스는 소스코드 상에서는 완전히 감추어져 있는 것.  
> 노트북이 직류 12볼트만 제공한다는 사실만 알뿐, 어댑터를 통해 교류 100볼트가 제공되는 것을 바꾸어 주는 것은 모르는 것과 같다.  
> Main 클래스는 PrintBanner 클래스가 어떻게 실현되고 있는지 모른다. 즉, Main 클래스의 변경 없이 PrintBanner 클래스의 구현을 변경할 수 있다는 것

## 정리

**Adaptor** 메소드의 목적 : 기존의 **Adaptee** 메소드를 사용해서 **Target** 메소드를 만족시키기 위한 것이다. **Client** 는 결국 **Target** 메소드만을 사용하게 된다.

### Target

지금 필요한 메소드를 결정한다. 예제 1,2 에서는 Print 인터페이스 또는 클래스가 해당된다.

### Client  

Target 역할의 메소드를 사용해서 일을 한다. 예제에서는 Main 클래스에 해당한다.

### Adaptee  

개조되는 쪽을 말한다. 이미 준비되어 있어서 메소드를 가지고 있는 역할이다. 예제 1,2 에서는 Banner 클래스가 이에 해당한다.  
이 Adaptee 메소드가 Target의 역할과 일치하면, Adapter의 역할은 필요 없는 것이다.

### Adapter

Adaptee 역할의 메소드를 사용하여 어떻게든 Target 역할을 만족시키기 위한 것이다.  
예제 1,2의 경우 PrintBanner 클래스가 이에 해당된다.  

## Hint

### 왜 사용하는가?  

이미 존재하고 있는 클래스를 이용하여 구현을 해야할 때 사용한다.  
기존에 존재하고 있는 클래스가 충분한 테스트를 통해 버그가 없고, 현재까지 실제로 사용된 실적이 있다면 이를 부품으로 재사용하는 것이 좋을 것이다.  

> 이렇게 Adaptor 패턴을 사용 후에 버그가 발생한다고 해도, 기존의 클래스에는 버그가 없으므로, Adapter 역할의 클래스를 중점적으로 조사하면 되므로 검사가 굉장히 쉬워진다.

### 기존 클래스의 소스의 사양만 알면 된다.

기존 클래스의 소스를 수정하는 방식으로 인터페이스를 만든다면, 테스트가 이미 끝난 기존의 클래스를 수정한 후에 다시 한번 테스트를 하는 절차를 거쳐야 한다.

> Adaptor 패턴은 기존의 클래스를 전혀 수정하지 않고 목적한 인터페이스(API)에 맞추는 것이 목적이다.  
> 기존 클래스의 소스가 모두 필요한 것은 아니다. 기존 클래스의 사양만 알면 새로운 클래스를 만들 수 있다.

### 버전업과 호환성

구 버전과 신 버전을 공존시키고, 유지와 보수도 편하게 하기 위해서 Adaptor 패턴을 사용하기도 한다.  
신 버전을 Adaptee 역할로 하고, 구 버전을 Target 역할로 하고, 신 버전의 클래스를 사용해서 구 버전의 메소드를 구현하는 Adapter 역할의 클래스를 만든다.  
물론 Adaptee와 Target의 기능이 지나치게 동떨어져 있을 경우는 Adaptor 패턴을 사용하지 못한다.

### 관련 패턴

* Bridge 패턴
  Adapter 패턴은 인터페이스(API)가 서로 다른 클래스를 연결하는 패턴이라면, Bridge 패턴은 기능의 계층과 구현의 계층을 연결시키는 패턴이다.
* Decorator 패턴
  Adapter 패턴을 인터페이스의 차이를 조정하기 위한 패턴이라면 Decorator 패턴을 인터페이스를 수정하지 않고 기능을 추가하는 패턴이다.
