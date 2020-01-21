# Mediator  

**각 회원들은 모두 중개인에게만 보고를 하고, 중개인만이 회원에게 지시를 내릴 수 있도록 하는 패턴**  
곤란한 일이 생기거나 모임 전체에 영향을 미칠만한 일이 발생하면 중개인에게 알리고, 중개인의 지시대로 실행한다.  
모임의 각 회원이 멋대로 다른 회원과 대화해서 판단하는 것이 아니라 항상 중개인을 통해서 행동하도록 하는 것이다.  
중개인은 모임의 회원으로부터 올라온 보고를 기초로 대국적인 판단을 해서 각 회원에게 지시를 내리는 것.  

## 예제 1

**로그인 다이얼로그 GUI 어플리케이션**  

사용자가 로그인이 되었을 때, Guest 체크박스를 선탯했을 때, 아이디 또는 패스워드가 입력되었을 때 등등에 대해 서로가 서로를 컨트롤 해야하는 상황이다.  
이와 같이 다수의 객체 사이를 조정해야 할 경우 Mediator 파턴을 이용하는 것이다.  
**표시 컨트롤의 로직은 중개인 안에만 기술한다.**


* Mediator 클래스  
    * 중개인을 표현하는 클래스.  
    * 구체적인 메소드 구현은 ConcreteMediator 역할이 담당할 것이다.(LoginFrame)
    * ```createColleagues()```를 통해 메소드에서 필요한 버튼이나 텍스트 필드 등을 작성한다.  
    * ```colleagueChanged()``` 메소드는 각 회원인 Colleague 들에서 호출된다.
        * 라디오 버튼이나 텍스트 필드의 상태가 변할 때 호출된다.  
* Colleague 인터페이스
    * 중개인에게 상담을 의뢰하는 회원을 나타낸다.  
    * ```setMediator()``` 메소드는 ConcreteMediator 역할을 하고 있는 ```LoginFrame()``` 메소드가 최초로 호출한다.
        * '내가 중개인이니까 기억해 두시오' 의 역할을 수행한다. 
        * 이 메소드의 인수로 Mediator 인스턴스를 전달하여, 이는 나중에 상담이 필요해질 때 ```colleagueChanged()```를 호출할 대 사용하게 된다.
    * ```setColleagueEnabled``` 메소드는 중개인이 내리는 '지시'에 해당한다.  
        * true이면 자기자신을 활성화 상태로 둔다.  
        * 이는 **자기자신이 활성화/비활성화 되는 상태를 스스로 판단하는 것이 아니고, 중개인의 판단에 따라서 결정된다.**  
> Mediator 클래스와 Colleague 클래스가 어떤 메소드를 가질 지는 어플리케이션에 따라 변하게 된다.  
* ColleagueButton, ColleagueTextField, ColleagueCheckbox 클래스  
    * ConcreteColleague 의 역할을 수행하여, Colleague 클래스의 메소드를 구현한다.
    * Mediator 를 필드로 가지고, ```setMediator()``` 의 인수를 통해 받은 Mediator 인스턴스를 가지게 된다.  
    * ```setColleagueEnabled()``` 메소드를 통해 해당 회원을 활성화/비활성화 시킨다. 단지 이 로직만 가지고 있지, 판단하는 로직은 없다.  
> Java의 클래스는 복수의 클래스에서 확장(extends) 할 수는 없지만, 복수의 인터페이스를 구현(implements)할 수는 있다.   

* LoginFrame 클래스
    * Mediator 클래스를 구현하고 있다.  
    * ```createColleagues``` 메소드에서는 필요한 Colleague를 생성하고 그것을 필드에 저장하는 역할을 한다. 또한 각 Colleague에 대해서 ```setMediator```를 호출해서, 내가 당신의 중개인이니까, 뭔 일이 있으면 알려주세요. 의 역할을 수행한다.  
    ```
    private ColleagueCheckbox checkGuest;
    private ColleagueCheckbox checkLogin;
    ...

    public void createColleagues() {
        checkGuest = new ColleagueCheckbox("Guest", g, true);
        ...

        checkGuest.setMediator(this);
    }
    ```
    * ```colleagueChanged``` 메소드에서는 **표시의 활성/비활성을 설정하는 복잡한 처리**를 실행하고 있다.  
        * 모든 Colleague 클래스는 ```collegueChanged()``` 메소드를 호출하기만 하면 된다.
        > 즉 모든 Colleague의 상담이 이 메소드에 집결되는 것이다.  
        ```
        public void colleagueChanged() {
            if(checkGuest.getState()) { //Guest 모드일 때
                textUser.setColleagueEnabled(false);
                ...
            } else {                    // Login 모드 일 때
                textUser.setColleagueEnabled(true);
                ...
            }
        }
        ```
    * ```userpassChanged``` 메소드는 내부에서만 사용하는 private 메소드이다. 공통 처리를 위해 사용한다.  
* Main 클래스
    * LoginFrame의 인스턴스를 생성한다.  
    ```
    public class Main {
        static public void main(String args[]) {
            new LoginFrame("Mediator Sample");
        }
    }
    ```

## 정리

### Mediator
Colleague 역할과 통신을 해서 조정을 실행하기 위한 인터페이스를 결정한다.  
예제 1의 Mediator 클래스가 해당된다.  

### ConcreteMediator  
Mediator 역할의 클래스를 실제로 구현해서, 실제의 조정을 실행한다.  
예제 1의 LoginFrame 클래스가 해당된다.  

### Colleague (동료)  
Mediator 역할과 통신을 실행할 인터페이스를 결정한다. 
예제 1의 Colleague 클래스에 해당된다.  

### ConcreteColleague 
Colleague 의 메소드를 실제로 구현한다.  
예제 1의 여러가지 요소 클래스에 해당된다.  

## Hint

### 분산이 화를 부를 때

예제 1의 LoginFrame 에 구현된 ```colleagueChanged()``` 메소드는 다소 복잡하다.  
사양이 변경되면 버그가 발생할텐데, 괜찮을까??  

오히려 문제가 되지 않는다. 버그가 발생한다고 해도  
> 유효/무효에 관한 로직은 여기 외에는 존재하지 않기 때문에, 여기만 디버그하면 된다.  

만약 해당 로직이 ColleagueButton, ColleagueCheckbox 등으로 분산되어 있다면 디버깅 하거나 수정하는 데에 문제가 될 것이다.  

객체 지향에서는 한 곳에 집중되는 것을 피해서 처리를 분산시키는 경우가 많다.  
하지만 현재 예제와 같은 상황에는 분산시키는 것이 현명하지 않다.  

> 각 클래스에 분산시킬 것은 분산시키고, 집중시킬 것은 집중시키지 않으면, 분산이 오히려 화를 부르게 될 수 있다.  


### 통신 경로의 증가
인스턴스의 수가 적을 때에는 경로의 경우의 수가 적지만, 인스턴스를 점점 증가시키면 파국을 초래할 수 있다.  

### 재이용할 수 있는 클래스는?  
> ConcreteColleague 역할은 재이용하기 쉽지만, ConcreteMediator 역할은 재이용하기 어렵다.  

ConcreteColleague 역할 안에는 특정한 상황에 의존하는 코드가 없기 때문이다.  
하지만 예제 1의 LoginFrame 같은 ConcreteMediator 역할은 어플리케이션에 대한 의존도가 높아 재이용성이 낮다.  

### 관련 패턴

* Facade 패턴  
Facade는 일방적인 단방향, Mediator은 쌍방향 (중개자로써 **주고받기**를 수행한다.)
* Observer 패턴  
Mediator 역할과 Colleague 역할의 통신은 Observer 패턴을 통해서 실행되는 경우가 있다.  