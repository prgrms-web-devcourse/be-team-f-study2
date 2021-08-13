---
title: Chain of Responsibility 패턴
tags:
  - 디자인패턴
  - 행위패턴
---

> 작성자 : 김동섭

<!--more-->

<hr>

원본자료 - [Refactoring Guru](https://refactoring.guru/design-patterns/template-method)

# Chain of Responsibility
- a.k.a CoR, Chain of Command


## 설명
- request들을 연속된 핸들러들에게 전달해주고, 각각의 핸들러는 전달받은 리퀘스트를 통해 정해진 작업을 수행할 지, 다음 핸들러로 넘겨줄지 결정한다. 

## 예제
![image](https://user-images.githubusercontent.com/24801719/129297084-7cf99617-5a38-44c9-bb19-c740e61ba29a.png)
- 온라인 주문 시스템에서 유저들이 올바른 정보로 로그인 했을 때만 주문을 가능하고, admin 권한을 가진 유저에게는 모든 권한을 주는 설계로 작성.
- 추후 데이터 validation이나, caching, ip checking 같은 기능들이 추가 되면서 주문을 하려는 request부터 실제 주문 시스템에 들어가기 전에 실행 해야할 check들이 너무나 많아지고 복잡해진다.
- Chain of Responsibility는 이러한 체크들을 각각의 독립적인 handler들로 분리한다. 
- 각각의 handler는 받은 리퀘스트에 대해 작업을 수행하고, 다음 핸들러로 넘겨줄지, 아니면 리퀘스트의 진행을 멈출 지 결정할 수 있다.
- CoR은 크게 두가지 형태를 가지는데, 하나는 체인형태, 또 다른 하나는 트리형태이다.
- 각각의 핸들러들은 받은 리퀘스트가 알맞은 데이터를 가지고 있다고 가정하고, 모든 핸들러들은 각각의 정해진 행동(로그인 정보 확인, 캐싱 등)을 진행한다. 성공시 다음 핸들러로 넘겨주고, 실패시 진행을 멈춘다.
![image](https://user-images.githubusercontent.com/24801719/129297091-33bebe71-3c74-4f61-a7cb-3af731dde056.png)
- 그러나 이렇게 일자로 연결되는 것보다는 트리 형태로 연결되는 것이 실무에서 더욱 자주 사용된다. 각각의 핸들러는 해당 리퀘스트가 자신이 처리할 수 있는 리퀘스트인지 확인하고, 만약 처리할 수 있다면 자신이 처리하고, 아닐 경우 다음 핸들러로 넘겨준다.
![image](https://user-images.githubusercontent.com/24801719/129297098-74965faa-26fb-43f9-8c31-8f20cf243cbb.png)
- CoR에서 중요한 점은 모든 핸들러들이 같은 interface를 구현해야 한다는 점이다. 그래야 구상체 핸들러들과 결합도를 높이지 않고 런타임 중에 chain들을 원하는 대로 조합이 가능해진다.

## 구조
![image](https://user-images.githubusercontent.com/24801719/129297108-d4d461d1-f591-416f-87be-a98ab083c4bd.png)
1. 핸들러 인터페이스는 모든 구상체 핸들러들이 공유하고, 주로 리퀘스트를 처리하는 하나의 메소드만 가지고 있지만, 때때로 다음 핸들러를 정해주는 setter 메소드를 가지고 있기도 한다.
2. 베이스 핸들러는 optional class로 모든 핸들러에 공통적으로 사용하고 싶은 코드가 있을 때 작성해 핸들러들이 상속하게 한다. 
	- 주로 베이스 핸들러 클래스는 다음 핸들러를 가르키는 필드 값을 정의한다. 클라이언트는 setter를 쓰거나 생성자에 다음 핸들러를 넘겨줘서 핸들러 채인을 만든다. 베이스 핸들러는 기본 동작을 구현할 수도 있다: 다음 핸들러가 존재하는 지 확인 한 후에 전달받은 리퀘스트를 넘겨준다. 
3. 구상체 핸들러는 리퀘스트를 처리하는 코드를 가진다. 리퀘스트를 받으면, 각 구상체 핸들러는 리퀘스트를 처리할지, 그리고 다음 핸들러로 넘겨줄 지 결정한다.
	- 핸들러들은 주로 독립적이고 불변적으로, 생성될 때 생성자로부터 필요한 모든 데이터를 받는다.
4. 클라이언트는 로직에 따라 chain들을 한번만 조합할 수도 있고, 다이나믹하게 조합할 수 있다. 
	- 리퀘스트는 chain의 어느 핸들러에게나 보내질 수 있다; 첫번째 핸들러가 아니어도 된다.

## 활용도
- 프로그램이 다양한 종류의 리퀘스트들을 여러가지 방법으로 처리해야 하지만, 리퀘스트들의 정확한 타입과 처리 방법들을 사전에 알 수 없을 때 사용
	- 여러 핸들러를 묶어 체인으로 만들고, 리퀘스트를 받았을 때 핸들러 체인에 보내면, 각각의 핸들러들은 리퀘스트를 처리할 기회를 얻는다.
- 몇몇 핸들러들을 정해진 순서대로 진행하는 것이 중요할 때 사용
	- 체인으로 묶으면 정해진 순서대로 핸들러들 진행할 수 있다.
- 핸들러 집합들과 그들의 순서가 런타임에 변할 때 사용
	- setter메소드를 핸들러에 구현하면, 런타임에 원하는 대로 핸들러들의 순서를 조정할 수 있다.

## 구현 방법
1. 리퀘스트를 처리하는 메소드를 가진 핸들러 인터페이스를 작성한다.
	- 클라이언트가 리퀘스트 데이터를 핸들러에 어떻게 보낼 지 결정한다. 가장 유연한 방법은 리퀘스트를 오브젝트로 바꿔서 핸들러에게 인자로 보내는 방법이다.
2. 중복 코드를 없애기 위해 abstract base handler class를 정의하는 것이 좋을 수 있다.
	- 이 클래스는 다음 핸들러를 가르키는 필드를 가지고 있어야 한다. 클래스를 immutable하게 만드는 것도 고려해볼 수 있다. 하지만 런타임에 체인을 바꿔야 한다면, setter를 추가해 다음 핸들러 필드를 바꿀 수 있게 한다.
	- 필요하다면 리퀘스트 처리에 받은 리퀘스트를 다음 핸들러로 넘겨주는 기본 동작을 넣을 수 있다.
3. 구상체 핸들러 클래스를 하나씩 만든다. 각각의 핸들러는 리퀘스트를 받을 때 두가지를 처리해야 한다.
	- 리퀘스트를 처리할 지 말지와 다음 핸들러로 리퀘스트를 보낼 지 말지
4. 클라이언트는 체인을 직접 조합하거나, 미리 만들어져 있는 체인을 사용할 수 있다. 만약 후자라면, 값이나 설정에 따라 체인을 생성해주는 팩토리 클래스들이 필요하다.
5. 클라이언트는 체인에 속한 어느 핸들러든 실행 시킬 수 있다. 리퀘스트는 중간에 핸들러에게 거부당하거나 체인의 끝까지 전달된다.
6. 클라이언트는 다음과 같은 시나리오를 상정해야 한다.
	- 체인은 하나의 링크로 구성되어 있을 수 있다.
	- 어떤 리퀘스트들은 체인의 끝에 도달하지 못할 수 있다.
	- 어떤 리퀘스트들은 체인의 끝에 처리되지 않은 상태로 도달할 수 있다.

## 장/단점
- 장점
	- 리퀘스트를 처리하는 순서를 조정할 수 있다.
	- SRP: 리퀘스트를 처리하는 행동들을 그러한 행동을 실행하는 클래스로부터 분리해준다.
	- OCP: 새로운 핸들러를 추가할 때 기존의 클라이언트 코드를 고장낼 일이 없다.
- 단점
	- 어떤 리퀘스트들은 어떠한 처리도 되지 않을 수 있다.


## 다른 패턴과의 관계
- CoR, Command, Mediator, Observer는 모두 리퀘스트의 sender와 receiver를 연결하는 다양한 것에 대해 다양한 방법을 제시한다
	- CoR은 리퀘스트를 처리될 때까지 순차적으로 잠재적 receiver(handler)들에 보낸다.
	- Command는 sender들과 receiver들 사이에 단방향 connection들을 만든다.
	- Mediator는 sender들과 receiver들 사이에 직접적 연결을 끊고, 중재자를 통해 통신하도록 한다.
	- Observer는 receiver들이 리퀘스트를 받는 것을 동적으로 구독하거나 구독 취소할 수 있게 한다.
- CoR은 주로 Composite과 같이 쓰인다. 이런 경우, leaf component가 리퀘스트를 받았을 경우, 체인을 따라 트리의 root까지 ㅇ전달 될 수 있다.
- CoR의 핸들러들은 Commands로 구현될 수 있다. 이런 경우, 다양한 작업들을 같은 context object(리퀘스트)에 실행할 수 있다. 
	- 다른 방식으로는, 리퀘스트 자체가 Command object인 경우다. 같은 작업을 different contexts의 시리즈(chain)에 실행할 수 있다.
- CoR과 Decorator는 비슷한 클래스 구조를 가지고 있다. 두 패턴 모두 재귀적 composition에 의존해 실행된지만, 중요한 차이점들이 있다.
	- CoR의 핸들러들은 각자 독립적으로 작업을 실행할 수 있다. 또한 리퀘스트를 다음에 언제라도 넘겨주지 않을 수 있다.
	- Decorator들은 기본적인 인터페이스를 지키며 객체의 행동을 확장할 수 있지만, 중간에 작업의 연속을 끊을 수 없다.


## 예제
middleware/Middleware.java: Basic validation interface
```java
package blog.dskim.designPatterns.chainOfResponsibility.middleware;

// Base middleware class.
public abstract class Middleware {
    private Middleware next;

    // Builds chains of middleware objects.
    public Middleware linkWith(Middleware next) {
        this.next = next;
        return next;
    }

    // subclasses will implement this method with concrete checks.
    public abstract boolean check(String email, String password);

    // Runs check on the next object in chain or ends traversing if we're in
    // last object in chain
    protected boolean checkNext(String email, String password) {
        if (next == null) {
            return true;
        }
        return next.check(email, password);
    }
}
```
---
middleware/ThrottlingMiddleware.java: Check request amount limit
```java
package blog.dskim.designPatterns.chainOfResponsibility.middleware;

// ConcreteHandler. Checks whether there are too many failed login requests.
public class ThrottlingMiddleware extends Middleware{
    private int requestPerMinute;
    private int request;
    private long currentTime;

    public ThrottlingMiddleware(int requestPerMinute) {
        this.requestPerMinute = requestPerMinute;
        this.currentTime = System.currentTimeMillis();
    }

    // checkNext() call can be inserted both in the beginning
    // of this method and in the end.
    // This gives more flexibility than a simple loop over all
    // middleware objects. For instance, an element of a chain can change
    // the order of checks by running its check after all other checks.
    @Override
    public boolean check(String email, String password) {
        if (System.currentTimeMillis() > currentTime + 60_000) {
            request = 0;
            currentTime = System.currentTimeMillis();
        }

        request++;

        if (request > requestPerMinute) {
            System.out.println("Request limit exceeded!");
            Thread.currentThread().stop();
        }
        return checkNext(email, password);
    }
}
```
---
middleware/UserExistsMiddleware.java: Check user's credentials
```java
package blog.dskim.designPatterns.chainOfResponsibility.middleware;

import blog.dskim.designPatterns.chainOfResponsibility.server.Server;

// ConcreteHandler. Checks whether a user with the given credentials exists.
public class UserExistsMiddleware extends Middleware{
    private Server server;

    public UserExistsMiddleware(Server server) {
        this.server = server;
    }

    @Override
    public boolean check(String email, String password) {
        if (!server.hasEmail(email)) {
            System.out.println("This email is not registered!");
            return false;
        }
        if (!server.isValidPassword(email, password)) {
            System.out.println("Wrong password!");
            return false;
        }
        return checkNext(email, password);
    }
}
```
---
middleware/RoleCheckMiddleware.java: Check user's role
```java
package blog.dskim.designPatterns.chainOfResponsibility.middleware;

// ConcreteHandler. Checks a user's role.
public class RoleCheckMiddleware extends Middleware{
    @Override
    public boolean check(String email, String password) {
        if (email.equals("admin@example.com")) {
            System.out.println("Hello, admin!");
            return true;
        }
        System.out.println("Hello, user!");
        return checkNext(email, password);
    }
}
```
---
server/Server.java: Authorization target
```java
package blog.dskim.designPatterns.chainOfResponsibility.server;

import blog.dskim.designPatterns.chainOfResponsibility.middleware.Middleware;

import java.util.HashMap;
import java.util.Map;

public class Server {
    private Map<String, String> users = new HashMap<>();
    private Middleware middleware;

    // Client passes a chain of object to server.
    // This improves flexibility and makes testing
    // the server class easier.
    public void setMiddleware(Middleware middleware) {
        this.middleware = middleware;
    }

    // Server gets email and password from client and sends
    // the authorization request to the chain.
    public boolean logIn(String email, String password) {
        if (middleware.check(email, password)) {
            System.out.println("Authorization have been successful");

            // Do something useful here for authorized users.

            return true;
        }
        return false;
    }

    public void register(String email, String password) {
        users.put(email, password);
    }

    public boolean hasEmail(String email) {
        return users.containsKey(email);
    }

    public boolean isValidPassword(String email, String password) {
        return users.get(email).equals(password);
    }
}
```
---
Demo.java: Client code
```java
package blog.dskim.designPatterns.chainOfResponsibility;

import blog.dskim.designPatterns.chainOfResponsibility.middleware.Middleware;
import blog.dskim.designPatterns.chainOfResponsibility.middleware.RoleCheckMiddleware;
import blog.dskim.designPatterns.chainOfResponsibility.middleware.ThrottlingMiddleware;
import blog.dskim.designPatterns.chainOfResponsibility.middleware.UserExistsMiddleware;
import blog.dskim.designPatterns.chainOfResponsibility.server.Server;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Demo {
    private static BufferedReader reader =  new BufferedReader(new InputStreamReader(System.in));
    private static Server server;

    private static void init() {
        server = new Server();
        server.register("admin@example.com", "admin_pass");
        server.register("user@example.com", "user_pass");

        // All checks are linked. Client can build various
        // chains using the same components.
        Middleware middleware = new ThrottlingMiddleware(2);
        middleware.linkWith(new UserExistsMiddleware(server))
                .linkWith(new RoleCheckMiddleware());

        // Server gets a chain from client code.
        server.setMiddleware(middleware);
    }

    public static void main(String[] args) throws IOException {
        init();

        boolean success;
        do {
            System.out.print("Enter email: ");
            String email = reader.readLine();
            System.out.print("Input password: ");
            String password = reader.readLine();
            success = server.logIn(email, password);
        } while (!success);
    }
}
```
---
```
> Task :Demo.main()
Enter email: admin@example.com
Input password: admin_pass
Hello, admin!
Authorization have been successful

> Task :Demo.main()
Enter email: user@example.com
Input password: user_pass
Hello, user!
Authorization have been successful

> Task :Demo.main()
Enter email: no@example.com
Input password: no_pass
This email is not registered!
Enter email: false@example.com
Input password: false_pass
This email is not registered!
Enter email: admin@example.com
Input password: false_password
Request limit exceeded!

> Task :Demo.main() FAILED
```



```java
public class ThrottlingMiddleware extends Middleware{
    private int requestPerMinute;
    private int request;
    private long currentTime;

    public ThrottlingMiddleware(int requestPerMinute) {
        this.requestPerMinute = requestPerMinute;
        this.currentTime = System.currentTimeMillis();
    }

    @Override
    public boolean check(String email, String password) {
        if (System.currentTimeMillis() > currentTime + 60_000) {
            request = 0;
            currentTime = System.currentTimeMillis();
        }
        request++;
        if (request > requestPerMinute) {
            System.out.println("Request limit exceeded!");
            Thread.currentThread().stop();
        }
        return checkNext(email, password);
    }
}


public class Server {
    private Map<String, String> users = new HashMap<>();
    private Middleware middleware;

    public void setMiddleware(Middleware middleware) {
        this.middleware = middleware;
    }

    public boolean logIn(String email, String password) {
        if (middleware.check(email, password)) {
            System.out.println("Authorization have been successful");

            // Do something useful here for authorized users.

            return true;
        }
        return false;
    }

    public void register(String email, String password) {
        users.put(email, password);
    }

    public boolean hasEmail(String email) {
        return users.containsKey(email);
    }

    public boolean isValidPassword(String email, String password) {
        return users.get(email).equals(password);
    }
}

```