---
title: Facade 패턴
tags:
  - 디자인패턴
  - 구조패턴
---

> 작성자 : 김동섭

<!--more-->

<hr>
 
 원본자료 - [Refactoring Guru](https://refactoring.guru/design-patterns/template-method)

# Template Method

## 설명
- 템플리 메소드는 상위 클래스안의 알고리즘의 뼈대를 정의하는 디자인 패턴으로, 하위 클래스들이 해당 알고리즘의 구조를 변경하지 않고 단계들을 override할수 있게 해줍니다.


## 예제
![image](https://user-images.githubusercontent.com/24801719/129295865-21e36378-0532-4c15-84fb-4192e9af940f.png)
![image](https://user-images.githubusercontent.com/24801719/129295883-1d44b95b-7cdd-48dd-b57e-5a8687fcbdc3.png)
- 문서를 받아서 내용을 분석하는 프로그램을 작성
- 각 문서의 확장자마다 데이터를 문서에서 추출하는 방법과 파싱하는 방법이 다름
- `DocDataMiner`를 만들어 사용하다가 앱이 커져서 csv, pdf 파일도 지원하기로 함.
- `CSVDataMiner`, `PDFDataMiner` 를 추가해 세 개의 다른 클래스를 만들어 사용
- 세 개의 클래스를 사용하다 보니 중복되는 코드가 발생하고 메인 코드에서도 들어오는 파일의 타입에 따라 다른 클래스를 불러야 하기 때문에 새로운 확장자의 문서를 추가할 때마다 클래스를 새로 만들고, 조건문도 추가해야함. 
- **템플릿 메소드는 문서에서 데이터를 추출하는 방식을 여러단계로 나누고, 해당 단계들을 각각의 메소드로 만든 후, 하나의 template method안에서 단계별 메소드들을 부르는 형식으로 만들게 함.**
- `DataMiner`라는 하나의 base 클래스를 만들고, 각 확장자 마다 해당 클래스를 상속하는 클래스를 만듬
	- base 클래스 안에는 `mine(path)`라는 메소드가 데이터 추출에 필요한 메소드들을 순차적으로 실행함: `openFile(path) ->  extractData(file) -> parseData(rawData) -> analyzeDate(data) -> sendReport(analysis) -> closeFile(file)`
- `mine` 메소드 이외에는 전부 abstract으로 만든 후 base 클래스를 상속하는 클래스들에서 전부 override하도록 작성
	- 만약 override된 코드들에 중복이 많다면, base 클래스로 가져와 default implementation으로 사용 가능 (`analyzeData, sendReport`같은 메소드)
	- 필요하다면, 각 클래스안에서 default implementation을 가진 메소드들도 override 가능
- hooks: 중요한 단계 앞 뒤에 body가 없는 메소드. hook method들이 override되지 않아도 정상적으로 작동하지만, 중요한 단계 앞뒤로 추가적인 동작이 필요한 클래스들에서는 활용가능.

## 구조
![Screen Shot 2021-08-13 at 11 20 43 AM](https://user-images.githubusercontent.com/24801719/129295977-bf59dcb8-555f-4058-8b82-f0472885d632.png)
1. Abstract Class는 알고리즘을 단계별로 나눈 메소드들을 선언하고, 그 메소드들을 순차적으로 실행하는 템플릿메소드를 작성합니다. 각각의 스텝 메소드들은 `abstract` 메소드로 선언되거나, default implementation을 가지고 있을 수 있습니다.
2. Concrete Class들은 모든 스텝들을 override할 수 있으나, tempalte method는 override할 수 없습니다.

## 활용도
- 클라이언트들에게 알고리즘의 특정 부분들만 확장할수 있도록 하면서 전체 알고리즘이나 구조를 변경할 수 있는 기능은 주고 싶지 않을 때 사용할 수 있습니다.
	- 템플릿 메소드는 단일화된 알고리즘을 여러 독립적인 단계로 나누고, 각 단계들은 전체 구조를 변경하지 않으면서 하위 클래스들이 확장할 수 있습니다.
- 여러 클래스들이 조금씩 다르지만 거의 같은 알고리즘을 가지고 있고, 알고리즘이 바뀌면 모든 클래스들을 변경해야 할 때 템플릿 메소드 패턴을 사용할 수 있습니다.
	- 이러한 알고리즘을 템플릿 메소드로 변경하면, 비슷한 단계들을 상위 클래스에서 구현해 중복 코드를 제거할 수 있습니다. 코드들의 다른 부분은 하위 클래스에서 적용됩니다.

## 구현 방법
1. 적용하고자 하는 알고리즘이 여러 단계로 나누어질 수 있는 지 확인합니다. 어떤 단계가 서브클래스들이 공통적으로 가지고 있고, 어떤 단계가 서브클래스들마다 다른지 확인합니다.
2. 알고리즘의 스텝을 추상 메소드로 선언한 추상 클래스를 만들고, 해당 스텝들을 순차적으로 실행하는 템플릿 메소드를 구현합니다. Override되는 것을 막기 위해 템플릿 메소드를 `final`로 만드는 것도 고려해 볼 수 있습니다.
3. default implementation이 있는 게 더 효율적인 메소드들이 작성해 줍니다. 
4. hooks를 중요한 단계들 사이에 추가하는 것을 고려해 봅니다.
5. 작성한 추상 클래스를 상속받는 구상 클래스들을 작성합니다. 모든 abstract 메소드들을 작성해야 하고, optional 메소드들도 override할 수 있습니다.

## 장/단점
- 장점
	- 클라이언트에게 거대한 알고리즘의 특정 부분만 override할 수 있게 합니다. 알고리즘의 다른 파트에 의해 영향 받는 것을 적게 해줍니다.
	- 중복코드를 상위 클래스로 옮겨 제거할 수 있습니다.
- 단점
	- 어떤 클라이언트들은 제공된 알고리즘의 뼈대로 인해 제한될 수 있다
	- LSP 를 위반할 수 있다: default implementation이 있는 메소드를 override할 경우
	- 단계가 많아질 수록 템플릿메소드를 유지보수하기 힘들어 지는 경향이 있다.

## 다른 패턴과의 관계
- 팩토리 메소드는 템플릿 메소드의 전문화(specialization)이다. 동시에, 팩토리 메소드는 거대한 템플릿 메소드의 한 단계 역할을 할 수 있다.
- 템플릿 메소드는 상속을 기반으로 한다: 알고리즘의 일부를 상속받은 서브클래스가 변경한다. Strategy 는 composition 기반이다: 객체에게 다양한 전략을 줘 객체의 행동을 변화 시킨다. 템플릿 메소드는 클래스 레벨에서 작동하기 때문에 static이다. 전략 패턴은 객체 레벨에서 작동하기 때문에, 객체의 행동을 runtime에 변화시킨다.

## 예제
networks/Network.java: Base social network class
```java
package blog.dskim.designPatterns.templateMethod.networks;

// Base class of social network.
public abstract class Network {
    String userName;
    String password;

    Network() {}

    // publish the data to whatever network
    public boolean post(String message) {
        // authenticate before posting. Every network uses a different authentication method.
        if (logIn(this.userName, this.password)) {
            // send the post data.
            boolean result = sendDate(message.getBytes());
            logOut();
            return result;
        }
        return false;
    }

    protected abstract void logOut();

    protected abstract boolean logIn(String userName, String password);

    protected abstract boolean sendDate(byte[] bytes);
}
```
---
networks/Facebook.java: Concrete social network
```java
package blog.dskim.designPatterns.templateMethod.networks;

// class of social newtork. a concrete social network
public class Facebook extends Network {
    public Facebook(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    @Override
    protected void logOut() {
        System.out.println("User: '" + userName + "'was logged out from Facebook");
    }

    @Override
    protected boolean logIn(String userName, String password) {
        System.out.println("\nChecking user's parameters");
        System.out.println("Name: " + userName);
        System.out.print("Password: ");
        for (int i = 0; i < this.password.length(); i++) {
            System.out.print("*");
        }
        simulateNetworkLatency();
        System.out.println("\n\nLogin success on Facebook");
        return true;
    }

    private void simulateNetworkLatency() {
        try {
            int i = 0;
            System.out.println();
            while (i < 10) {
                System.out.print(".");
                Thread.sleep(500);
                i++;
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected boolean sendDate(byte[] data) {
        boolean messagePosted = true;
        if (messagePosted) {
            System.out.println("Message: '" + new String(data) + "'was posted on Facebook");
            return true;
        }
        return false;
    }
}
```
---
networks/Twitter.java: One more social network
```java
package blog.dskim.designPatterns.templateMethod.networks;

// class of social network. a concrete class of network
public class Twitter extends Network{
    public Twitter(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    @Override
    protected void logOut() {
        System.out.println("User: '" + userName + "'was logged out from Twitter");
    }

    @Override
    protected boolean logIn(String userName, String password) {
        System.out.println("\nChecking user's parameters");
        System.out.println("Name: " + userName);
        System.out.print("Password: ");
        for (int i = 0; i < this.password.length(); i++) {
            System.out.print("*");
        }
        simulateNetworkLatency();
        System.out.println("\n\nLogin success on Twitter");
        return true;
    }

    private void simulateNetworkLatency() {
        try {
            int i = 0;
            System.out.println();
            while (i < 10) {
                System.out.print(".");
                Thread.sleep(500);
                i++;
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected boolean sendDate(byte[] data) {
        boolean messagePosted = true;
        if (messagePosted) {
            System.out.println("Message: '" + new String(data) + "'was posted on Twitter");
            return true;
        }
        return false;
    }
}
```
---
Demo.java: Client code
```java
package blog.dskim.designPatterns.templateMethod;

import blog.dskim.designPatterns.templateMethod.networks.Facebook;
import blog.dskim.designPatterns.templateMethod.networks.Network;
import blog.dskim.designPatterns.templateMethod.networks.Twitter;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

// Demo class. a client code
// Everything comes together here.
public class Demo {
    public static void main(String[] args) throws IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        Network network = null;
        System.out.print("Input user name: ");
        String userName = reader.readLine();
        System.out.print("Input password: ");
        String password = reader.readLine();

        // Enter the message
        System.out.print("Input message: ");
        String message = reader.readLine();

        System.out.println("\nChoose social network for posting message.\n" +
                "1 - Facebook\n" +
                "2 - Twitter");
        int choice = Integer.parseInt(reader.readLine());

        // Create proper network object and send the message
        if (choice == 1) {
            network = new Facebook(userName, password);
        } else if (choice == 2 ) {
            network = new Twitter(userName, password);
        }
        network.post(message);
    }
}
```
---
Execution result
```
> Task :Demo.main()
Input user name: dskim
Input password: abcd
Input message: Hello World!

Choose social network for posting message.
1 - Facebook
2 - Twitter
2

Checking user's parameters
Name: dskim
Password: ****
..........

Login success on Twitter
Message: 'Hello World!'was posted on Twitter
User: 'dskim'was logged out from Twitter
```


```java
// class of social network. a concrete class of network
public class Twitter extends Network{
    public Twitter(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    @Override
    protected void logOut() {
        System.out.println("User: '" + userName + "'was logged out from Twitter");
    }

    @Override
    protected boolean logIn(String userName, String password) {
        ... // check username and password
    }

    @Override
    protected boolean sendDate(byte[] data) {
        boolean messagePosted = true;
        if (messagePosted) {
            System.out.println("Message: '" + new String(data) + "'was posted on Twitter");
            return true;
        }
        return false;
    }
}
```

---
```java
public class SmellyFacebook {
    String userName;
    String password;

    public SmellyFacebook(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    public void postToFacebook(byte[] data) {
        // login
        boolean success = facebookLogin();
        if (success) {
            // send data
            success = sendDataToFacebook(data);
            if(success) System.out.println("Message posted on Facebook!");
            // logout
            facebookLogout();
        }
    }

    private void facebookLogout() {
        ... // try to logout
    }

    private boolean sendDataToFacebook(byte[] data) {
        ... // send message to Facebook server.
    }

    private boolean facebookLogin() {
        ... // try to login in
    }
}


public class SmellyTwitter {
    String userName;
    String password;

    public SmellyTwitter(String userName, String password) {
        this.userName = userName;
        this.password = password;
    }

    public void postToTwitter(byte[] data) {
        // login
        boolean success = twitterLogin();
        if (success) {
            // send data
            success = sendDataToTwitter(data);
            if(success) System.out.println("Message posted on Twitter!");
            // logout
            twitterLogout();
        }
    }

    private void twitterLogout() {
        ... // try to logout
    }

    private boolean sendDataToTwitter(byte[] data) {
        ... // send message to Twitter server.
    }

    private boolean twitterLogin() {
        ... // try to login in
    }
}

```