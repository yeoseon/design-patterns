# Interpreter

프로그램이 해결하려는 문제를 간단한 '미니언어'로 표현한다.  
구체적인 문제를 미니 언어로 기술된 '미니 프로그램' 으로 표현한다.  
미니 프로그램은 그 자체만으로는 동작하지 않기 때문에 Java 언어로 **통역** 역할을 하는 프로그램을 만들어둔다.  
통역 프로그램은 미니 언어를 해석하고 미니 프로그램을 해석, 실행한다.  
이 통역 프로그램을 인터프리터라고 부른다.  

**해결해야 할 문제게 변화가 생겼을 때, Java언어로 기술된 프로그램을 수정하는 것이 아니라 미니 프로그램 쪽을 수정해서 대처한다.**  

---

~~추후 사용할 일이 있을 떄 다시 보자~~