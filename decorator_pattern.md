# Chapter 03 Decorator Pattern
---

### 디자인의 유연성
**목표**: 기존 코드를 건드리지 않고, 확장으로 행동을 추가

> **OCP(Open-Closed Principle)**
: 클래스 확장에는 열려 있어야 하지만 변경에는 닫혀 있어야 한다.

OCP를 준수하기 위해 새로운 단계의 추상화를 하다 보면 코드가 복잡해질 가능성이 있다. 따라서, **가장 바뀔 가능성이 높은 부분 위주로** OCP를 적용하는 것이 좋다.

---

### 상속 대신 구성

서브클래스를 만드는 방식으로 행동을 **상속** 받으면 컴파일 한 후 그 행동을 변경할 수 **없다**. 또 모든 서브클래스에서 **똑같은** 행동을 상속받아야 한다. 

하지만 **구성**으로 객체의 행동을 확장하면 실행 중에 **동적으로** 행동을 설정할 수 있기 때문에, <u>클래스 코드를 건드리지 않고도 객체에 기능을 추가할 수 있다 **(Decorator Pattern**)</u>.

<br>

> **객체를 동적으로 구성할 때 장점**
- 기존 코드를 고치지 않고 새로운 코드를 만들어서 기능을 추가할 수 있다.
- 코드 수정에 따른 버그나 에러를 막을 수 있다.

---

## Decorator 패턴

### Feature

- 데코레이터의 슈퍼클래스는 자신이 장식하고 있는 객체의 슈퍼클래스와 같다.
    - 원래 객체(감싸고 있는 객체)가 들어갈 자리에 데코레이터 객체를 넣어도 상관없다.
    - <u>**원래 객체와 데코레이터 객체의 형식(=인터페이스)은 같다.**</u>
- 한 객체를 여러 개의 데코레이터로 감쌀 수 있다.
- **데코레이터는 자신이 장식하고 있는 객체에게 어떤 행동을 위임하는 일 말고도 추가 작업을 수행할 수 있다.**

### Example

```java
public abstract class Beverage {
	String description = "제목 없음";
    
    public String getDescription() {
    	return description;
    }
    
    public abstract double cost();
}

public abstract class CondimentDecorator extends Beverage {
	Beverage beverage;
    public abstract String getDescription();
}

public class Espresso extends Beverage { ... }

public class HouseBlend extends Beverage { ... }
```
 - **행동**을 상속 받는 것이 아니라 **형식**을 맞추기 위해서 Beverage의 서브클래스를 생성
 - **행동**은 인스턴스 변수로 다른 객체(데코레이터)를 저장하는 식으로 연결
 - **유연성을 잃지 않을 수 있음**
 - Beverage 클래스를 인터페이스로 만들지 않고 추상 클래스로 만든 이유
 	- 기존 코드를 고치는 일을 최대한 피하기 위해 추상 클래스로 생성
<br>

```java
public class Mocha extends CondimentDecorator {
	public Mocha(Beverage beverage) {
    	this.beverage = beverage;
    }
    
    ...
}
```
- Mocha 인스턴스에 들어있는 Beverage의 레퍼런스
	- 감싸고자 하는 음료를 저장하는 인스턴스 변수
    - 인스턴스 변수를 감싸고자 하는 객체로 설정하는 생성자
        - 데코레이터의 생성자에 감싸고자 하는 음료 객체를 전달하는 방식을 사용
<br>

```java
public class StarbuzzCoffee {
    public static void main(String args[]) {
        Beverage beverage = new Espresso();

        Beverage beverage2 = new DarkRoast();
        beverage2 = new Mocha(beverage2);
        beverage2 = new Mocha(beverage2);
        beverage2 = new Whip(beverage2);

        Beverage beverage3 = new HouseBlend();
        beverage3 = new Soy(beverage3);
        beverage3 = new Mocha(beverage3);
        beverage3 = new Whip(beverage3);
    }
}
```

- **구상 구성 요소**로 어떤 작업을 처리하는 코드에 데코레이터 패턴을 적용하면 코드가 제대로 작동하지 않는다.
- **추상 구성 요소**로 돌아가는 코드에는 데코레이터 패턴을 적용해야만 한다.
---

### Advantage

- 디자인을 유연하게 구현할 수 있다.
    - 데코레이터가 어떻게 작동하는지, 클래스가 어떻게 구성되어 있는지를 이해하면 클래스를 데코레이터로 감싸서 원하는 행동을 구현할 수 있다.
    - 객체는 언제든지 감쌀 수 있으므로 실행 중에 필요한 데코레이터를 마음대로 적용할 수 있다. 

- 데코레이터를 끼워 넣어도 클라이언트는 데코레이터를 사용하고 있다는 사실을 전혀 알 수 없다.

- 구성을 활용하면 실행 중에 데코레이터를 마음대로 조합해서 사용할 수 있다.

### Disadvantage

- 클래스들이 너무 많아지는 문제가 발생할 수 있다.

- 특정 형식에 의존하는 클라이언트 코드에 데코레이터를 적용하면 문제가 발생한다.

- 구성 요소를 초기화하는데 필요한 코드가 훨씬 복잡해진다.

---

### Usecase

- ```Java I/O``` 패키지
