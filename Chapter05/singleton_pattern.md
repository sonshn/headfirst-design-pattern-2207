# Chapter 05 Singleton Pattern

> **"특정 클래스의 객체 인스턴스를 하나'만' 만들자"**
---
## Definition
> **클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공하는 패턴**

## Feature

- **public**으로 지정된 생성자가 **없다**. (**private**으로 저장되어 있다.)

- Singleton 객체가 필요할 때 인스턴스를 달라고 요청하는 정적 메소드 ```getInstance()```가 있다. 

---

## Class Diagram

![Singleton의 클래스 다이어그램](/Chapter05/singleton_pattern1.jpg)
- 클래스 변수에 Singleton의 인스턴스가(**1개**) 저장된다.
- ```getInstance()```가 정적 메소드이기 때문에, ```Singleton.getInstance()```로 언제든지 호출할 수 있다. 
- 변수나 메소드는 자유롭게 추가로 사용하면 된다.

<br>

``` java
public class ChocolateBoiler{
    private boolean empty;
    private boolean boiled;

    private static ChocolateBoiler uniqueInstance;

    private ChocolateBoiler(){
        empty = true;
        boiled = false;
    }

    public static ChocolateBoiler getInstance(){
        if(uniqueInstance == null){
            uniqueInstance = new ChocolateBoiler();
        }

        return uniqueInstance;
    }

    public void fill(){
        if(isEmpty()){
            empty = false;
            boiled = false;
        }
    }
}

```

---

## 전역 변수 vs Singleton Pattern

- **공통점**
    - 전역 변수처럼 <u>**객체 인스턴스를 어디서든지 액세스**</u> 할 수 있다.

- **차이점**
    - **전역 변수**는 게으른 인스턴스 생성을 할 수 없고, 사용 여부와 상관 없이 인스턴스를 가지고 있어야 한다.
    - 반면, **Singleton Pattern**은 <u>**필요할 때만**</u>  객체를 만들 수 있다.
    - **전역 변수**를 사용하다 보면 간단한 객체의 전역 레퍼런스를 자꾸 만들게 되어 네임스페이스 역시 지저분하게 만들어진다.

---

## 장점

- 자원을 불필요하게 낭비할 필요가 없다.
- 결과의 일관성을 유지할 수 있다.
- 한 애플리케이션에 들어있는 객체는 모두 <u>**같은**</u> 자원을 활용할 수 있다.

## 단점

- **Multithreading (동기화 문제)**
: ```getInstance()``` 처리 순서에 따라 서로 다른 두 객체가 반환될 수 있다.
    <br>
    - **해결 방법**

        - ```getInstance()```에  ```synchronized``` 키워드를 추가하면, 한 thread가 메소드 사용을 끝내기 전까지 다른 스레드는 기다려야 하기 때문에 동시에 실행되지 않는다. (성능 저하 문제와 병목으로 작용할 수 있는 문제 때문에 <u>**메소드가 시작될 때만 효율적**이다.</u>)
    
        <br>

        ```java
        public class Singleton{
            ...

            public static synchronized Singleton getInstance(){
                ...
            }
        }
        ```
        - 처음부터 Singleton 인스턴스를 생성하면 된다.
        
        <br>

        ```java
        public class Singleton{
            // Singleton의 인스턴스를 생성하는 static initializer
            private static Singleton uniqueInstance = new Singleton();

            private Singleton() {}

            public static Singleton getInstance(){
                // 인스턴스는 이미 있으니까 리턴만 하면 된다.
                return uniqueInstance;
            }
        }
        ```

        - <u>**DCL(Double-Checked Locking)**</u>을 사용해서 인스턴스가 생성되어 있는지 확인한 다음, 그렇지 않다면 동기화한다.

        <br>

        ```java
        public class Singleton{
            private volatile static Singleton uniqueInstance;

            private Singleton() {}

            public static Singleton getInstance(){
                if(uniqueInstance == null){
                    synchronized (Singleton.class){
                        if(uniqueInstance == null){
                            uniqueInstance = new Singleton();
                        }
                    }
                }

                return uniqueInstance;
            }
        }
        ```

<br>

- 클래스 로더가 여러 개 있으면 Singleton이 제대로 작동하지 않고, 여러 개의 인스턴스가 생길 수 있습니다.

- 리플렉션, 직렬화, 역직렬화의 문제가 있다.

- **느슨한 결합 원칙**에 위배한다.
    - 싱글턴을 바꾸면 연결된 모든 객체도 다 바꿔야 할 가능성이 높다.

---

## ```enum```

- ```enum```으로 싱글턴을 생성하면 클래스 로딩 문제, 리플렉션, 직렬화와 역직렬화 문제를 해결 가능하다.

```java
public enum Singleton{
    UNIQUE_INSTANCE;
    ...
}

public class SingletonClient{
    public static void main(String[] args){
        Singleton singleton = Singleton.UNIQUE_INSTANCE;
        // 싱글턴을 사용하는 위치
    }
}
```

---

## Usecase

- 스레드 풀, 캐시, 대화상자, 사용자 설정, 레지스트리 설정을 처리하는 객체, 로그 기록용 객체, 디바이스 드라이버