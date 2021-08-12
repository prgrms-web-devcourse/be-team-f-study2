---
title: Flyweight(플라이웨이트) 패턴
tags:
  - 디자인패턴
  - 구조패턴
---

> 작성자 : 전효희

<!--more-->

<hr>


### Flyweight(플라이웨이트) 패턴

	인스턴스를 가능한 한 공유해서 사용함으로써 메모리를 절약하는 패턴

<!--more-->


### 👿 문제상황

	마인크래프트 게임에 나무를 설치하고 싶습니다. 



	나무는 색(color)을 정할 수 있고, 특정 위치(x, y)에 세울 수 있습니다.
	
	만약 내가 마크에 미쳐서 나무 10000개를 맵에 심는다고 할 때, 
	
	나무 한 그루에 들어가는 데이터는
	
	color : 4Byte , x: 4byte, y:4byte 라고 합시다.
	
	모든 나무를 심을 때마다 객체를 생성한다면, 나무 10,000개에는 120,000byte 가 듭니다.

![](https://images.velog.io/images/hoit_98/post/80015d98-c611-4252-8cde-0f4601769d85/image.png)

	더 마크에 열광하는 사람들은 더 많은 나무를 세울텐데,, 나~~~중에는 메모리가 터질수도 있습니다.



### 😇 어떻게 해결할까

	모든 나무의 x, y는 다르지만 나무 색은 항상 바뀔 필요는 없죠? 거의 같고 가끔 색 바꾸게 됩니다.
	
	그러면 나무를 계속 새로 만들지 말고,, 저장해놨다가 가져다 쓰는 식으로 한다면??


![](https://images.velog.io/images/hoit_98/post/953ebe1d-262c-4983-9d5e-408335881658/image.png)

	그러면 새로운 색상의 나무가 추가될 때만 새 객체를 생성한다면
	
	나무가 **10000개 여도 12 * (새로운 색상을 추가한 횟수) byte** 이렇게 변하겠네요!
	
	내가 만약 색을 2개만 쓴다면 24byte 이니까 이전보다 119976byte 가량 절약할 수 있습니다!
	
	나무 수가 많을 수록 더 절약할 수 있겠네요!
	
	이렇게 인스턴스를 가능한대로 공유시켜서 쓸데없는 new를 통한 메모리 낭비를 줄이는것이 Flyweight 패턴입니다!



### 🤔 구현을 어떻게 할까

	플라이웨이트 패턴을 구현하기 위해 아래 3가지가 필요합니다.

1. 실제 공유될 객체 (나무)

2. 객체의 인스턴스를 생성하고 공유해주는 팩토리(Factory) 

   -> 같은 색의 나무가 없다면 새로 생성하고, 있다면 그 색의 나무를 반환해줍니다.

3. 패턴을 사용할 고객, 클라이언트 

   -> 우리는 그냥 2번에게 OO색 나무를 요청하고, 받아서 X,Y를 설정하고 설치하면 됩니다.

   

### 📜 코드로 살펴볼까요!

1. 공유할 Tree 객체

```java
public class Tree {

    // 나무는 아래와 같이 3개 정보를 가지고 있습니다.
    private String color;
    private int x;
    private int y;

    //색상으로만 생성자를 만들어줄게요.
    public Tree(String color) {
        this.color = color;
    }

    public void setX(int x) {
        this.x = x;
    }

    public void setY(int y) {
        this.y = y;
    }

    //나무를 심을 때
    public void install(){
        System.out.println("x:"+x+" y:"+y+" 위치에 "+color+"색 나무를 설치했습니다!");
    }
}
```

   

  2. Tree를 제공해줄  TreeFactory

```java
public class TreeFactory {
    //HashMap 자료구조를 활용해서 만들어진 나무들을 관리해볼게요
    public static final HashMap<String, Tree> treeMap = new HashMap<>();
    
   
    public static Tree getTree(String treeColor){
        //Map에 입력받은 색상의 나무가 있는지 찾습니다. 있으면 그 객체를 제공합니다.
        Tree tree = (Tree)treeMap.get(treeColor); 

       //만약 아직 같은 색상의 나무가 Map에 없다면 새로 객체를 생성해 제공합니다.
        if(tree == null){
            tree = new Tree(treeColor);
            treeMap.put(treeColor, tree);
            System.out.println("새 객체 생성");
        }

        return tree;
    }
}
```

​    

  3. 사용할 고객

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("원하는 색을 입력해주세요 :)");
        for(int i=0;i<10;i++){
            //나무 색 입력받기
            String input = scanner.nextLine();
            //팩토리에서 나무 하나 공급받기
            Tree tree = (Tree)TreeFactory.getTree(input);
            //나무 x,y 설정하고
            tree.setX((int) (Math.random()*100));
            tree.setY((int) (Math.random()*100));
            //나무 설치하기
            tree.install();
        }

    }
}
```



 💻 결과

**다른 색상이 들어올 때만 새로운 객체를 생성합니다!**

```shell
원하는 색을 입력해주세요 :)
초록
새로운 객체 생성
x:47 y:55 위치에 초록색 나무를 설치했습니다!
연두
새로운 객체 생성
x:86 y:33 위치에 연두색 나무를 설치했습니다!
초록
x:62 y:30 위치에 초록색 나무를 설치했습니다!
초록
x:83 y:21 위치에 초록색 나무를 설치했습니다!
초록
x:47 y:46 위치에 초록색 나무를 설치했습니다!
연두
x:49 y:59 위치에 연두색 나무를 설치했습니다!
연두
x:10 y:38 위치에 연두색 나무를 설치했습니다!
카키
새로운 객체 생성
x:78 y:39 위치에 카키색 나무를 설치했습니다!
카키
x:21 y:41 위치에 카키색 나무를 설치했습니다!
카키
x:58 y:26 위치에 카키색 나무를 설치했습니다!
```

### ❓ 싱글톤 패턴과 다른게 뭐죠?

 - 싱글톤 역시 객체를 하나만 생성하고 활용하는 패턴입니다.
   둘의 다른 점은 뭘까요?
   우리의 나무 예시를 생각해볼까요?

* 

  우리가 제작한 **Flyweight 패턴**에서,
  나무는 색깔이 바뀔 때 새로운 객체를 생성합니다. 
  색상 별로 하나씩, 결과적으로는 여러개의 나무가 생깁니다.
  또한 만들어진 객체의 색상은 바꿀 수 없는 일입니다.
  따라서 **하나씩 여러종류**를 가질 수 있습니다.

  그러나 **싱글톤 패턴**이라면,
  나무 클래스에 단 한개의 나무만 만들수 있습니다.
  따라서 싱글톤 패턴을 사용한다면,
  만들어진 단 하나의 객체(나무)의 색깔을 바꿔야만합니다. 
  싱글톤은 이렇듯 하나의 클래스에 단 하나의 인스턴스를 생성하고, 대신 변수를 필요시 변경해가며 쓸 수 있다는 차이가 있습니다.
  따라서 싱글톤 패턴은 **종류 상관없이 단 하나만** 가질 수 있습니다.


### 😎 멋진 사실

사실 플라이웨이트 패턴은 Java의 String Constant Pool에 적용됩니다...!

자바의 String은 만들어질때 String Constant Pool에 저장되어 

같은 문자열이 pool에 있다면 이를 불러오는 방식으로 되어있습니다!

(관련 내용을 정리했던 제 블로그 글 : [링크](https://velog.io/@hoit_98/Assignment-StringBuffer-and-String-Builder) ) 

따라서 String class는 Flyweight 패턴을 적용하여 메모리를 절약할 수 있습니다!!


------

### 💕 참고한 링크들

1. 기본적인 패턴 설명 + 용량 절약에 대한 내용(리팩토링 그루)

   https://refactoring.guru/design-patterns/flyweight

2. 플라이웨이트 패턴의 자바 구현 예제

   https://lee1535.tistory.com/106

3. 플라이웨이트 패턴과 String Constant Pool에 대한 내용

   https://it-mesung.tistory.com/183

4. 나무 예제에 대한 자세한 내용

   https://m.blog.naver.com/2feelus/220669069127

5. 싱글톤과의 비교
   https://medium.com/remember/flyweight-pattern-f02c771a72fb

### 🥰 만든 이(전효희) 블로그 포스트: [링크](https://velog.io/@hoit_98/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-Flyweight-%ED%8C%A8%ED%84%B4)
