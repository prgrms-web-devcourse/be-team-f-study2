---
title: Prototype Pattern
tags:
  - 디자인패턴
  - 생성패턴
---

> 작성자 : 김다희

<!--more-->

<hr>

![image](https://user-images.githubusercontent.com/81504103/129557114-d25a3b0c-28ce-4873-bf62-fe63728d2942.png)

<hr>


### Prototype Pattern ?

생성할 객체들의 타입이 프로토타입인 인스턴스로부터 결정되도록 하는 패턴으로 인스턴스는 새 객체를 만들기 위해 자신을 복제한다.

![image](https://user-images.githubusercontent.com/81504103/129557914-c581c8cd-d336-433f-a336-5016f81bc3ba.png)

-   Prototype : 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정하는 역할을 한다.
-   ConcretePrototype : 인스턴스를 복사해서 새로운 인스턴스를 만드는 메소드를 실제로 구현하는 역할을 한다.
-   Client : 인스턴스 복사 메소드를 사용해서 새로운 인스턴스를 만드는 역할을 한다.

<br>

### 언제 쓰면 좋을까?

객체를 만들 때 new 키워드를 사용해서 만들 수 있지만, new 키워드는 비용이 크다. 그래서 객체 생성비용이 클 경우에 사용하면 좋다. (ex. db 에서 데이터를 가져와서 객체 생성 후 또 똑같은 객체가 필요할 때 , 객체 생산 비용-작업율-이 클 때  등)

<br>

### 어떻게 구현하는데?

clone() 메소드를 선언하는 추상 베이스 클래스를 하나 만들고, 다형적 생성자 기능이 필요한 클래스가 있다면 앞서 만든 클래스를 상속받게 한 후 clone() 메소드 내의 코드를 구현한다.

<br>

### 예시 코드

![image](https://user-images.githubusercontent.com/81504103/129558126-e23a6435-04ed-4c47-80eb-cf9382c3990e.png)

Prototype

```
public class Coffee implements Cloneable{
    private String name;

    public Coffee() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Object clone() {
        try{
        	//new 연산자를 사용하지 않고 clone()으로 Object를 생성한다.
            Coffee copy  = (Coffee)super.clone();
            //Object를 반환한다.
            return copy;
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
            return null;
        }
    }

    @Override
    public String toString() {
        return "Coffee [name = " + this.name + "]";
    }
}
```

ConcretePrototype

```
public class CoconutCoffee extends Coffee{
    public CoconutCoffee() {
    }
}
```

Client

```
public class CoffeeMachine {
    private Coffee coffee;

    public CoffeeMachine(Coffee coffee) {
        this.coffee = coffee;
    }

    public Coffee makeCoffee() {
    	//복제된 객체 반환
        //clone() 반환 타입이 Object라서 캐스팅 해줘야한다.
        return (Coffee)coffee.clone();
    }
}
```

```
public class Main {
    public static void main(String[] args) {
        Coffee coffee = null;
        Coffee prototype = new CoconutCoffee();
        CoffeeMachine coffeeMachine = new CoffeeMachine(prototype);

        for(int i = 0; i < 10; i++) {
            coffee = coffeeMachine.makeCoffee();
            coffee.setName("CoconutCoffee"+i);
            System.out.println(coffee.toString() + ", hashCode : " + coffee.hashCode());
        }
    }
}

//결과
CoconutCoffee
Coffee [name = CoconutCoffee0], hashCode : 366712642
Coffee [name = CoconutCoffee1], hashCode : 1829164700
Coffee [name = CoconutCoffee2], hashCode : 2018699554
Coffee [name = CoconutCoffee3], hashCode : 1311053135
Coffee [name = CoconutCoffee4], hashCode : 118352462
Coffee [name = CoconutCoffee5], hashCode : 1550089733
Coffee [name = CoconutCoffee6], hashCode : 865113938
Coffee [name = CoconutCoffee7], hashCode : 1442407170
Coffee [name = CoconutCoffee8], hashCode : 1028566121
Coffee [name = CoconutCoffee9], hashCode : 1118140819
```

<br>

### 장점

-   객체 생성의 복잡성을 줄인다.
-   객체를 생성해 주기 위한 별도의 객체 생성 클래스가 불필요하다.

<br>

### 단점

-   clone 메서드를 구현해야하는 비용이 발생한다.
-   깊은 복사, 얕은 복사를 주의하면서 clone 메서드를 구현해야한다.

<br>

### 관련 패턴

-   flyweight pattern : 가능한 새 객체를 만들지 않고 기존 객체를 재사용해서 메모리를 줄이는 것으로 구조 패턴에 속한다.