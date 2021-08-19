---
title: AbstractFactory 패턴
tags:
  - 디자인패턴
  - 생성패턴
---

> 작성자 : 박정미

<!--more-->


___
# Creational Pattern

## 1. Abstract Factory

### 📚 의도

> 상세화된 서브클래스를 정의하지 않고도 서로 관련성이 있거나 독립적인 여러 객체의 집합을 생성하기 위한 인터페이스를 제공합니다.(GoF)


기본이 되는 **Factory가 재료를 공급**하며, 생산된 Product가 정확히 무엇인지 알지 못해도 **Product의 인터페이스만 알면 조작할 수 있다**.


- 예시
  - 연관 있는 제품의 집합: {Monitor + Keyboard + Mouse}
  - 제품 집합의 변형(제품들을 만드는 회사): 
    - Samsung{Monitor + Keyboard + Mouse}
    - LG{Monitor + Keyboard + Mouse}
    - Apple{Monitor + Keyboard + Mouse}




### 🧱 구조

![img](https://media.vlpt.us/images/chrishan/post/85b01a09-3c9e-42b2-8f52-5ce4611d84ab/structure-indexed-2x.png)

1. **Abstract Products** : `ProductA`, `ProductB`
   - 모니터, 키보드, 마우스 interface
2. **Concrete Products** : `ConcreteProductA1`, `ConcreteProductA2` / `ConcreteProductB1`, `ConcreteProductB2`
   - 삼성 모니터, LG 모니터 / 삼성 키보드, LG 키보드
3. **Abstract Factory** : `createProductA():ProductA`, `createProductB():ProductB`
   - 모니터를 생산한다, 키보드를 생산한다, 마우스를 생산한다
4. **Concrete Factories** :`createProductA():ConcreteProductA1`, `createProductB():ConcreteProductB1`
   - 삼성 팩토리: 삼성 모니터를 생산한다, 삼성 키보드를 생산한다, 삼성 마우스를 생산한다.
   - LG 팩토리: LG 모니터를 생산한다, LG 키보드를 생산한다, LG 마우스를 생산한다.
5. **Client** : `Abstract Factory`, `Abstract Products` 사용



### 💻 코드

1. **Abstract Products**

   ```java
   public interface Monitor {
       void turnOn();
   }
   
   public interface Keyboard {
       void type();
   }
   
   public interface Mouse {
       void click();
   }
   ```

2. **Concrete Products**

   ```java
   // Monitor
   public class SamsungMonitor implements Monitor {
       @Override
       public void turnOn() {
           System.out.println("Samsung 모니터");
       }
   }
   
   public class LGMonitor implements Monitor {
       @Override
       public void turnOn() {
           System.out.println("LG 모니터");
       }
   }
   
   public class AppleMonitor implements Monitor {
       @Override
       public void turnOn() {
           System.out.println("Apple 모니터");
       }
   }
   
   // Keyboard
   public class SamsungKeyboard implements Keyboard {
       @Override
       public void type() {
           System.out.println("Samsung 키보드");
       }
   }
   
   public class LGKeyboard implements Keyboard {
       @Override
       public void type() {
           System.out.println("LG 키보드");
       }
   }
   
   public class AppleKeyboard implements Keyboard {
       @Override
       public void type() {
           System.out.println("Apple 키보드");
       }
   }
   
   // Mouse도 동일
   ```

3. **Abstract Factory**

   ```java
   public interface CompanyFactory {
       Monitor createMonitor();
   
       Keyboard createKeyboard();
   
       Mouse createMouse();
   }
   ```

4. **Concrete Factories**

   ```java
   public class SamsungFactory implements CompanyFactory {
       @Override
       public Monitor createMonitor() {
           return new SamsungMonitor();
       }
   
       @Override
       public Keyboard createKeyboard() {
           return new SamsungKeyboard();
       }
   
       @Override
       public Mouse createMouse() {
           return new SamsungMouse();
       }
   }
   
   public class LGFactory implements CompanyFactory {
       @Override
       public Monitor createMonitor() {
           return new LGMonitor();
       }
   
       @Override
       public Keyboard createKeyboard() {
           return new LGKeyboard();
       }
   
       @Override
       public Mouse createMouse() {
           return new LGMouse();
       }
   }
   
   public class AppleFactory implements CompanyFactory {
       @Override
       public Monitor createMonitor() {
           return new AppleMonitor();
       }
   
       @Override
       public Keyboard createKeyboard() {
           return new AppleKeyboard();
       }
   
       @Override
       public Mouse createMouse() {
           return new AppleMouse();
       }
   }
   ```

5. **Client**

   ```java
   public class Client {
       private static final CompanyFactory FACTORY = new Application().createFactory();
   
       public static void main(String[] args) {
           Monitor monitor = FACTORY.createMonitor();
           monitor.turnOn();
   
           Keyboard keyboard = FACTORY.createKeyboard();
           keyboard.type();
   
           Mouse mouse = FACTORY.createMouse();
           mouse.click();
       }
   }
   ```

   **주의**
   - Application이 환경 설정에 맞게 팩토리 타입을 고르고, 초기화 단계에서 concrete factory 객체를 생성해놓아야 한다.



- Console
  ```java
  // 입력
  APPLE
  
  // 출력
  Apple 모니터
  Apple 키보드
  Apple 마우스
  ```





### 🔮 장단점
- **장점**
  1. Open-Closed Principle: 집합에 새 제품이나 제품군을 추가할 때 핵심 코드를 변경할 필요가 없다.
  2. 클라이언트와 팩토리에서 생산되는 제품을 분리시킬 수 있다.
     - 클라이언트의 코드와 구체적인 제품의 결합성을 떨어트린다.
  3. 객체들의 집합을 생성해야 할 때 유용하다
     - 팩토리에서 얻어지는 제품들의 호환성을 확인할 수 있다.


- **단점**
  1. 많은 인터페이스와 클래스 -> 복잡한 코드




### ➰ 어떤 상황에서 사용될까?
- `Collection`의 `iterator`


#### 출처
- https://johngrib.github.io/wiki/abstract-factory-pattern/
- https://velog.io/@chrishan/abstract-factory-pattern
