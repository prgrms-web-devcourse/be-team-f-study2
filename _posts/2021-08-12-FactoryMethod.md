---
title: Factory Method 패턴
tags:
  - 디자인패턴
  - 생성패턴
---

> 작성자 : 한재원

<!--more-->

<hr>

## 그게 뭐야 ❓

- 인터페이스를 사용해 객체를 생성할 때, 어떤 종류의 클래스(해당 인터페이스를 상속한)를 만들지는 서브 클래스에서 결정하게 하는 패턴
- 메인 클래스에서는 팩토리 클래스로 어떤 객체의 선택을 위임한다.
- 팩토리 클래스는 객체 생성을 대신 수행해주는 공장이라고 생각하면 된다.

<br>

## 클래스 다이어그램

<img style='margin:20px' src="https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png">

- 대충 이렇게 생겼다고 한다. 예시를 통해 이해해보자

<br>

## 문제상황

- 요즘엔 모든 로그인이 OAuth 로그인으로 이루어진다.
- 따라서 현재 시스템에선 회원정보에 해당 회원이 어떤 사이트로 가입했었는지를 저장하고 있다.
- 회원이 로그인하려할때, 어떤 사이트의 로그인 방식을 가져올지 팩토리 메소드로 구현해보자

<br>

## 시스템 구조

<img width="450" src="https://user-images.githubusercontent.com/71180414/128675506-e199e421-8c06-4115-aca2-78e0ba7e4365.png">

<br>

<img src="https://user-images.githubusercontent.com/71180414/128660471-199e5ba1-737d-4ea4-abe2-12b854abfce8.png">

- 로그인 종류는 Naver, Kakao 두 가지가 있고, 둘 다 Login 인터페이스를 상속받은 클래스로 구현됨.
- Main 클래스에서는 해당 회원이 어떤 사이트로 가입했는지 정보(Login Type)만을 Factory 클래스로 넘겨준다
- Factory 클래스에선 해당 정보에 맞는 로그인 방식(클래스)을 선택해 객체를 생성하여 Main에 리턴한다
- 그럼 Main 클래스에선 정확히 어떻게 선택했는지, 뭐가 선택됐는지 알 필요 없이 리턴받은 인터페이스 객체를 사용하면 끝~

<br>

## 추가 정보

- 위처럼 팩토리 클래스를 `싱글톤`패턴을 사용해 클래스로 구현해도되지만, 그냥 메인에서 `static` 메소드로 구현해도 됨!!
- Primitive 타입을 감싸는 wrapper class : `Boolean`, `Integer`, `Long` 등의 `valueOf()`가 팩토리 메소드 패턴으로 구현되었다

### 장점

- 객체 간 결합도를 낮추고 DI를 행함으로써 유지보수를 용이하게 하기 위해서! (거의 모든 디자인패턴이 그렇다.)
- 또한 호스트 코드?에서 객체를 직접 생성하지 않으므로, 객체를 **팩토리 클래스**에서 따로 관리할 수 있다(생명주기 관리 GOOD)

### 단점

- 인터페이스를 상속받는 새로운 하위 클래스가 계속 생겨나면 ... 복잡성 UP

<br>

## 참고링크

- [블로그 원본](https://hanjo8813.github.io/designpattern/1/) 
- https://refactoring.guru/design-patterns/factory-method
- https://niceman.tistory.com/143?category=940951

<br>