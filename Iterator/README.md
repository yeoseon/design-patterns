# Iterator 

iterator : '반복한다'의 의미. '반복자' 라고도 한다.
많이 모여있는 것들을 순서대로 지정하면서 전체를 검색하는 처리를 수행하기 위한 패턴.  
for문의 'i'의 기능을 추상화하여 일반화한 것

## 예제 1

**책장(BookShelf) 안에 책(Book)을 넣고, 그 책의 이름을 차례대로 표시하는 프로그램**


## 정리

**Aggregate 인터페이스**와 **Iterator 인터페이스**(API)를 통해 각 Aggregate와 Interator의 구체적인 클래스인 **ConcreteAggregate**와 **ContreteIterator**를 구현하여 사용한다.

### Aggregate

Iterator 역할을 만들어내는 인터페이스(API)를 결정한다.  
'내가 가지고 있는 요소를 순서대로 검색해주는 사람'을 만들어내는 메소드.

### ConcreteAggregate

Aggregate 역할이 결정한 인터페이스(API)를 실제로 구현하는 일을 한다.  
Aggregate 역할을 하는 인스턴스를 만들어낸다. 예제의 BookShelf  클래스가 이 역할을 담당하며, Iterator 메소드를 구현한다. 

## Hint

### 왜 For문을 사용하지 않고 Iterator 패턴을 사용할까?

추상 클래스와 인터페이스를 사용해 만들기 때문에, 실제 예제를 위한 구체화된 클래스의 구현과 분리되어 사용된다.  
위의 예제1 에서 Book 제목을 출력하기 위해 사용된 것은 BookShelf의 메소드가 아닌 Iterator의 메소드. BookShelf의 구현에는 의존하지 않는다는 말이다.  
BookShelf를 구현한 사람이 해당 구현체를 수정했다고 하더라도, 올바른 Iterator를 뱉어내고 있다면, Book의 제목을 출력하는 로직은 변경하지 않아도 동작할 수 있다는 것이다.

> 다음과 같이, 디자인 패턴은 클래스의 재사용을 촉진한다.  
> 클래스를 부품처럼 사용할 수 있고, 하나의 부품을 수정하더라도 전체 제품에 큰 영향 없이 작은 수정만으로도 끝낼 수 있다는 것을 의미한다.

> 클래스 간의 결합을 약하게 해서 부품으로 재사용하기 쉽도록 추상 클래스나 인터페이스가 도입된다.

이는 디자인패턴 전반에 걸쳐서 나올 개념이기 때문에 잘 기억해두도록 한다.

마찬가지로 BookShelf 의 구현을 모두 변경했을 때에는 BookShelfIterator의 수정도 필요하게 된다. 

> Aggregate와 Iterator 라는 두 개의 인터페이스가 쌍을 이루듯이, ConcreteAggregate와 ConcreteIterator 라는 두 개의 인터페이스도 쌍을 이루고 있다.

### next와 hasNext

next는, 현재의 요소를 반환하면서 다음 위치고 진행한다.

hasNext는, 다음에 next 메소드를 불러도 괜찮은지를 조사하는 것이다.

### 복수의 Iterator

하나씩 나열하는 구조가 Aggregate 역할의 '외부'에 놓여있다.  
따라서 하나의 ConcreteAggregate 역할에 대해 다수의 ConcreteIterator 역할을 만들 수 있다.

### deleteIterator는 필요 없다.

Java에서는 사용되지 않는 인스턴스를 자동으로 삭제하는 [**Garbage Collection**](https://ko.wikipedia.org/wiki/%EC%93%B0%EB%A0%88%EA%B8%B0_%EC%88%98%EC%A7%91_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)) 의 개념을 가지기 때문에, delete 메소드는 필요 없다. 

### 관련패턴

* Visitor 패턴
  예제 1의 Book 요소 하나하나를 꺼내서 세는 것을 Iterator 패턴을 통해 수행했다면, 해당 Book 요소에 대한 처리를 하는 것은 Visitor 패턴을 이용한다.
* Composite 패턴
  재귀적인 구조를 갖는 패턴에서는 Iterator 패턴을 사용하는 것이 어렵다.
* Factory Method 패턴
  Iterator 인스턴스를 작성할 때 Factory Method 패턴이 사용된다. 

