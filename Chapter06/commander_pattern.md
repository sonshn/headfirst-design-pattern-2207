# Chapter 06 Commander Pattern

## Definiton
> **요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화할 수 있는 패턴**

## Feature
- 어떤 작업을 요청하는 객체와 그 작업을 처리하는 객체를 분리할 수 있다.
- 명령으로 객체를 매개변수화 할 수 있다.
- Queue와 Log를 구현하거나 undo 하는 것이 가능하다.
- 매크로 기능도 구현할 수 있다.

---

## '커맨드 객체'
- 일련의 행동들을 특정 receiver와 연결함으로써 특정 객체에 관한 특정 작업 요청을 캡슐화해준다.
- 행동과 receiver를 한 객체에 넣고, ```execute()``` 하나만 외부에 공개하는 방법을 써야 한다.
- ```execute()``` 호출에 따라 receiver에서 일련의 작업을 처리한다.

    <br>

    - **클래스 다이어그램**

    <br>

    ![Commander 패턴 클래스 다이어그램](/Chapter06\commander_pattern_1.jpg)

    <br>
    
    1. **Client**: **ConcreteCommand**를 생성하고 **Receiver**를 설정
    2. **Invoker**: 명령이 들어있으며, ```execute()```를 호출함으로써 커맨드 객체에게 특정 작업을 수행해 달라는 요구를 하게 된다.
    3. **Command**: Command는 모든 커맨드 객체에서 구현해야 하는 인터페이스
        - 모든 명령은 ```execute()``` 호출로 수행되며, 이 메소드는 **Receiver**에 특정 작업을 처리하라는 지시를 전달한다.
    4. **ConcreteCommand**: 특정 행동과 **Receiver**를 연결해준다. Invoker에서 ```execute()``` 호출로 요청하면 **ConcreteCommand** 객체에서 **Receiver**에 있는 메소드를 호출해서 그 작업을 처리한다.
        - ```execute()```에서는 **Receiver**에 있는 메소드를 호출해서 요청된 작업을 수행한다.
    5. **Receiver**: 요구 사항을 수행할 때 어떤 일을 처리해야 하는지 알고 있는 객체

    <br>

    - **커맨드 인터페이스** 구현
    커맨드 객체는 모두 같은 인터페이스를 구현해야 한다.
    ```java
    public interface Command{
        public void execute();
        public void undo();
    }
    ```

    <br>

    - **커맨드 클래스** 구현
    **Light** 클래스에는 **on()** 과 **off()** 2개의 메소드가 있다고 가정한다.
    ```java
    public class LightOnCommand implements Command{
        Light light;

        public LightOnCommand(Light light){
            this.light = light;
        }

        public void execute(){
            light.on();
        }

        public void undo(){
            light.off();
        }
    }
    ```
    1. 생성자에 이 커맨드 객체로 제어할 특정 조명의 정보가 전달된다. 그 객체는 ```light```이라는 인스턴스 변수에 저장이 되고, ```execute()``` 가 호출되면 light 객체가 그 요청의 receiver가 된다. 
    2. ```execute()```는 receiver 객체에 있는 ```on()```을 호출한다.

    <br>

    - **커맨드 객체 사용**
    ```java
    public class SimpleRemoteControl{
        Command slot;

        public SimpleRemoteControl() {}

        public void setCommand(Command command){
            slot = command;
        }

        public void buttonWasPressed(){
            slot.execute();
        }
    }
    ```
    1. ```slot()```: 커맨드를 저장할 슬롯
    2. ```setCommand()```: 슬롯으로 제어할 명령을 설정하며, 리모컨 버튼의 기능을 바꾸고 싶을 때 사용
    3. ```buttonWasPressed()```: 리모컨 버튼을 누르면 호출 (슬롯에 연결된 커맨드 객체의 ```execute()```만 호출하면 됨)

    <br>

    - 커맨드 객체 **테스트 클래스**
    ```java
    // Client
    public class RemoteControlTest{
        public static void main(String[] args){
            SimpleRemoteControl remote = new SimpleRemoteControl();
            Light light = new Light();
            LightOnCommand lightOn = new LightOnCommand(light);

            remote.setCommand(lightOn);
            remote.buttonWasPressed();
        }
    }
    ```
    1. ```remote```: 필요한 작업을 요청할 때 사용할 커맨드 객체를 인자로 전달 받는 invoker
    2. ```light```: 요청을 받아서 처리할 receiver
    3. ```lightOn```: 커맨드 객체 **(receiver를 전달하는 부분)**
    4. ```setCommand()```: 커맨드 객체를 invoker에게 전달하는 부분

    <br>

    - **NULL 객체**: 반환할 객체도 없고 클라이언트가 null을 처리하지 않게 하고 싶을 때 활용하면 좋다.  

    <br>

    - **람다 표현식**
        - 구상 커맨드 객체를 생성하는 단계를 건너뛸 수 있다.
        - 구상 커맨드 객체의 인스턴스를 생성하는 대신 그 자리에 <u>**함수 객체**</u>를 사용할 수 있어 모든 구상 커맨드 클래스를 지울 수 있다.
        - 단, **Command 인터페이스**에 추상 메소드가 <u>**1**</u>개 일 때만 사용할 수 있다.

## Usecase

- 스케줄러, 스레드 풀, 복구 시스템
- **Task Queue**
    - 한쪽 끝은 **커맨드(커맨드 인터페이스를 구현하는 객체)를 Queue에 추가**할 수 있도록 되어 있고, 다른 쪽 끝에는 **커맨드를 처리하는 thread들이 대기**하고 있다.
    - 각 thread는 ```execute()```를 호출하고 호출이 완료되면 커맨드 객체를 버리고 새로운 커맨드 객체를 가져온다.
