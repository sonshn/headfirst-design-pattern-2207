# Chapter 04 Factory Pattern

> **"바뀌는 부분을 캡슐화하라"**

---
## 디자인의 유연성

- > **구현보다는 인터페이스에 맞춰서 프로그래밍**
: ```new``` 대신 인터페이스에 맞춰서 코딩하면, 어떤 클래스든 특정 인터페이스만 구현하면 사용할 수 있기 때문에 여러 **변화**에 대응할 수 있다. 

- > **의존성 뒤집기 원칙(Dependency Inversion Principle)**
: **추상화된 것에 의존하게 만들고, 구상 클래스에 의존하지 않게 만든다**.
    - 고수준 구성 요소가 저수준 구성 요소에 의존하면 안 되며, 항상 추상화에 의존하게 만들어야 한다.
    - **Inversion**: 고수준 구성 요소와 저수준 구성 요소가 모두 하나의 추상 클래스에 의존하게 된다.

---
## Factory Pattern

```java
//(factory class) 클라이언트가 받을 피자를 만듦
public class SimplePizzaFactory{
    //클라이언트가 새로운 객체 인스턴스를 만들 때 호출
    public Pizza createPizza(String type){
        ...
    }
}

public class PizzaStore{
    SimplePizzaFactory factory;

    //생성자에 factory 객체가 전달
    public PizzaStore(SimplePizzaFactory factory){
        this.factory = factory;
    }

    public Pizza orderPizza(String type){
        ...
        //type을 전달하기만 하면 팩토리로 객체를 만듦
        pizza = factory.createPizza(type);

        ...
    }
}
```
- **Factory: 객체 생성을 처리하는 클래스**
    - 객체 생성 작업을 팩토리 클래스로 **캡슐화**하면 **구현을 변경할 때 팩토리 클래스 하나만 고치면** 된다.

- static method로 Factory를 정의할 경우
    - 장점: 객체 생성 메소드를 실행하려고 객체의 인스턴스를 만들지 않아도 된다.
    - 단점: 객체 생성 메소드의 행동을 변경할 수 없다.

---
> **"객체 생성을 캡슐화"**
    - 애플리케이션의 결합을 느슨하게 만든다.
    - 특정 구현에 덜 의존하도록 만든다.

## Factory Method Pattern

### Definition
이 패턴에서는 객체를 생성할 때 필요한 인터페이스를 만들고, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다. 이 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 된다.

### Feature

- 클래스의 상속으로 객체를 만든다.
- 클라이언트 코드와 인스턴스를 만들어야 할 구상 클래스를 분리시켜야 할 때 활용한다.
- 어떤 구상 클래스가 필요할 지 미리 알 수 없을 때 유용하다.

```java
public abstract class PizzaStore{
    ...

    public Pizza orderPizza(String type){
        Pizza pizza;

        // PizzaStore의 추상 메소드 createPizza()
        pizza = createPizza(type);

        ...
    }

    // factory 객체 대신 사용하는 factory 메소드 createPizza()
    abstract Pizza createPizza(String type);
}
```

> 실제로 **팩토리 메소드를 구현**하고 **객체 인스턴스 만드는 일**은 **<u>서브클래스에서만 할 수 있다</u>**.

- 피자<u>**(생산되는 개체 인스턴스)의 종류**</u>는 <u>**어떤 서브클래스를 선택(사용)했느냐**</u>에 따라 갈린다.
    - createPizza()가 추상 메소드이고, 따라서 서브클래스에서 각각의 createPizza()를 구현해야 하기 때문이다.
    - Pizza(구상 클래스) 인스턴스를 만드는 일은 factory 메소드 createPizza()에서 처리한다. 

- **<u>객체(구상 형식 인스턴스) 생성을 캡슐화</u>** 할 수 있다.
    - orderPizza() (혹은 **슈퍼클래스**) 에서는 서브클래스에서 무슨 일이 일어나는지 **알 수 없다.**

- 추상 클래스를 만든 다음 그 클래스를 확장해서 구상 클래스를 만든다면, 팩토리 메소드를 오버라이딩해야 한다.

### Syntax
```java
abstract Product factoryMethod(String type)
```
- **특정 객체를 리턴하며, 그 객체는 보통 슈퍼클래스가 정의한 메소드 내에서 쓰인다.** 
- 클라이언트에서 실제로 생성되는 구상 객체가 무엇인지 알 수 없게 만드는 역할도 한다.
- 단, 위와 같이 매개변수를 사용하면 **type-safety 문제가 생겨** '런타임 오류'가 발생할 수 있다.
    - 매개변수 형식을 나타내는 객체를 만들 수도 있고, 정적 상수를 쓸 수도 있고, enum을 사용할 수도 있다.

### 장점

- 객체 생성 코드를 전부 **한 객체 또는 메소드**에 넣으면 **코드에서 중복되는 내용을 제거**할 수 있고, 나중에 **관리**할 때도 **한 군데에만** 신경을 쓰면 된다.
- 객체 인스턴스를 만들 때 인터페이스만 있으면 된다.
- **유연성과 확장성을 높일 수 있다**.

---
### Simple Factory vs Factory Method Pattern

- Simple Factory의 경우 팩토리가 PizzaStore 안에 포함되는 별개의 객체였지만, Factory Method Pattern의 경우 createPizza() 추상 메소드가 정의되어 있는 추상 클래스를 확장해서 만들었다.

- 전자는 일회용 처방이지만, 후자는 여러 번 재사용이 가능한 프레임워크를 만들 수 있다. 두 방법 모두 객체 생성을 캡슐화하는 방법을 사용하긴 하지만 **생성하는 제품을 마음대로 변경할 수 없기 때문에** 후자가 전자에 비해 **더 유연하다**.

---
## Abstract Factory Pattern

### Definition

이 패턴은 구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공합니다. 구상 클래스는 서브클래스에서 만듭니다.

### Feature

- 팩토리 메소드 패턴과 마찬가지로 애플리케이션을 특정 구현으로 분리할 수 있다.
- 객체 구성으로 객체를 만들 수 있다.

```java
public interface PizzaIngredientFactory {
    public Dough createDough();
    public Sauce createSauce();
    public Cheese createCheese();
    public Veggies[] createVeggies();
    public Pepperoni createPepperoni();
    public Clams createClam();
}
```
- 팩토리용 인터페이스에 각 재료별 생성 메소드를 정의한다.
- 재료마다 하나씩 클래스를 만들어야 한다.
- 서브클래스에서 제품이 생산되는 방법을 정의해야 한다.
- 재료를 추가함으로써 관련된 제품을 확대해야 한다면 모든 서브클래스의 인터페이스를 바꿔야 한다.

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory{
    
    // 인터페이스에서 정의한 재료군에 들어있는 재료를 뉴욕 지점에 알맞게 만든다.
    public Dough createDough(){
        return new ThinCrustDough();
    }

    ...
}
```
<br>

```java
public abstract class Pizza{
    String name;

    Dough dough;
    Sauce sauce;
    Veggies veggies[];
    Cheese cheese;
    Pepperoni pepperoni;
    Clams clam;

    abstract void prepare();

    ...
}
```
- prepare()는 추상 메소드이며, <u>**원재료 팩토리에서 원재료들을 가져오는 부분**</u>이다.

```java
public class CheesePizza extends Pizza{
    PizzaIngredientFactory ingredientFactory;

    public CheesePizza(PizzaIngredientFactory ingredientFactory){
        this.ingredientFactory = ingredientFactory;
    }

    void prepare(){
        dough = ingredientFactory.createDough();
        ...
    }
}
```
- 각 피자 클래스는 생성자로부터 팩토리를 전달받고, 인스턴스 변수에 저장한다.
- **prepare()** 에서 **치즈 피자를 만드는 각 단계를 처리**하며, 재료가 필요할 때마다 팩토리에 있는 메소드를 호출해서 만든다.

### Syntax
```java
sauce = ingredientFactory.createSauce();
```
- **prepare()** 의 **ingredientFactory**는 원재료 팩토리이며, **Pizza** 클래스는 원재료 팩토리가 맞지만 하면 어떤 팩토리를 쓰든 상관하지 않는다.
