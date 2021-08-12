---
title: Interpreter 패턴
tags:
  - 디자인패턴
  - 행위패턴
---

> 작성자 : 전효희

<!--more-->

<hr>


### Interpreter 패턴

	문법적 규칙을 클래스화 하여, 일련의 규칙을 통해 언어/문법을 해석하는 패턴이다.


### 	😊 사용되는 곳

	SQL 문은 SELECT (어쩌구)  FROM (저쩌구) WHERE (이러쿵저러쿵) 과 같이
	
	특정 문법을 지키면 MYSQL에서 알아서 쿼리결과를 짠 하고 내줍니다.
	
	덕분에 개발자들은 손쉽게 DB에서 결과를 얻게 됩니다. 
	
	이렇듯 간단한 **문법적 규칙(대체로 문자열을 해석하는)을 클래스화해서 사용자가 원하는 답을 얻게 해주는 패턴**이 Interpreter 패턴입니다.
	
	주로 SQL 해석, shell 명령어 해석기 등에 사용하는 패턴입니다.




### 	🤔 구현을 어떻게 할까 (구현 예시)

Interpreter 패턴으로 [후위 표기법](https://ko.wikipedia.org/wiki/%EC%97%AD%ED%8F%B4%EB%9E%80%EB%93%9C_%ED%91%9C%EA%B8%B0%EB%B2%95) 을 구현해보겠습니다.

```
후위 표기법에서는 1+2 를 1 2 + 로 표현합니다.

(3 + 5) * (4 + 2)  => 3 5 + 4 2 + *

우리가 "a b +" 를 요청해도, "w x z - +" 를 요청해도 

문법 해석기가 알아서 해석해서 결과를 줄 수 있어야 합니다.
```

![](https://images.velog.io/images/hoit_98/post/d00ed04e-25ef-4640-b55d-6724f88ac4c7/image.png)

1. 우리가 "w x z - +"라는 문장을 Evaluator라는 문법해석기에 넣을 겁니다.
2. Evaluator는 문장을 해석해 저 문장대로 계산이 가능한 계산기를 출력해줍니다.
3. 그 계산기에 사용자가 원하는대로 문자=10(w=10) 과 같이 입력을 넣어주면
4. 우리는 답 37을 얻을 수 있습니다.


### 📜 코드로 살펴볼까요! 

 1. 연산자들

 - 연산자 interface 

```java
import java.util.Map;

//우리가 만들 연산자들
public interface Expression {
    //어떻게 해석할지 구현
    public int interpret(Map<String, Expression> variables);
}
```
 - Plus 연산자 구현
```java
//Plus 연산자 구현
public class Plus implements Expression{
    //+의 왼쪽에 올 표현(숫자일 수도 있고 이미 연산된 결과일 수 있음)
    Expression leftOperand;
     //+의 오른쪽에 올 표현
    Expression rightOperand;

    public Plus(Expression leftOperand, Expression rightOperand) {
        this.leftOperand = leftOperand;
        this.rightOperand = rightOperand;
    }

    @Override
    public int interpret(Map variables) {
        //왼쪽과 오른쪽의 결과를 더해서 반환
        return leftOperand.interpret(variables)+rightOperand.interpret(variables);
    }
}
```
- Minus 연산자 구현
```java
//Minus 연산자 구현
public class Minus implements Expression{
    //-의 왼쪽에 올 표현(숫자일 수도 있고 이미 연산된 결과일 수 있음)
    Expression leftOperand;
    //-의 왼쪽에 올 표현(숫자일 수도 있고 이미 연산된 결과일 수 있음)
    Expression rightOperand;

    public Minus(Expression leftOperand, Expression rightOperand) {
        this.leftOperand = leftOperand;
        this.rightOperand = rightOperand;
    }

    @Override
    public int interpret(Map variables) {
         //왼쪽에서 오른쪽의 결과를 빼서 반환
        return leftOperand.interpret(variables)-rightOperand.interpret(variables);
    }
}
```
- Variable(알파벳에 해당) 연산자 구현
```java
public class Variable implements Expression{
    private String alphabet;

    public Variable(String alphabet) {
        this.alphabet = alphabet;
    }

    @Override
    public int interpret(Map<String, Expression> variables) {
        // 사용자가 입력한 Map에 alphabet이 있으면 그 알파벳에 해당하는 숫자 반환
        // 없으면 0 반환 
        if(variables.get(alphabet) == null)
            return 0;
        return variables.get(alphabet).interpret(variables);
    }
}
```

- Number(사용자가 입력한 숫자) 연산자 구현
```java
public class Number implements Expression{
    private int num;

    public Number(int num) {
        this.num = num;
    }

    @Override
    public int interpret(Map variables) {
        return num; //숫자를 반환
    }
}
```
 2. 문법에 따라 연산 제조
 - Evaluator(해석기) 구현
 - 후위 연산법은 스택을 통해 구현합니다.(참고 : [후위 표기법](https://ko.wikipedia.org/wiki/%EC%97%AD%ED%8F%B4%EB%9E%80%EB%93%9C_%ED%91%9C%EA%B8%B0%EB%B2%95))

```java
    import java.util.Map;
    import java.util.Stack;
    
    public class Evaluator implements Expression{
        private Expression syntax; //만들 최종 해석
        
        public Evaluator(String expression){
            //expression을 입력받음
            Stack<Expression> stack = new Stack<Expression>();
    
            for(String strToken : expression.split(" ")){
                if(strToken.equals("+")){
                    Expression expressionPlus = new Plus(stack.pop(),stack.pop());
                    stack.push(expressionPlus);
                }else if(strToken.equals("-")){
                    Expression expressionMinus = new Minus(stack.pop(),stack.pop());
                    stack.push(expressionMinus);
                }else {
                    stack.push(new Variable(strToken));
                }
            }
            //해석 결과로 나온 계산기 
            syntax = stack.pop();
        }
    
        //사용자가 입력한 숫자로 계산기에 계산돌리기
        @Override
        public int interpret(Map<String, Expression> variables) {
            return syntax.interpret(variables);
        }
    }
```

​    

 3. 메인

```java
    import java.util.HashMap;
    import java.util.Map;
    
    public class Main {
        public static void main(String[] args) {
            //해석을 원하는 문장
            String expression ="w x z - +";
            
            //문장에 따른 해석기를 제작
            Evaluator sentence = new Evaluator(expression);
    
            //사용자가 w = 5, x = 10, z =42 를 입력
            Map<String, Expression> variables = new HashMap<String, Expression> ();
            variables.put("w",new Number(5));
            variables.put("x",new Number(10));
            variables.put("z",new Number(42));
    
            //해석기에 입력을 넣고 결과 얻기
            int result = sentence.interpret(variables);
            System.out.println(result);
        }
    
    }
```

​    

💻 결과

 "w x z - +" 

(z-x) + w == (42-10) + 5

```
37
```





------

### 💕 참고한 링크들

1. 기본적인 Interpreter 패턴이해

   [블로그 링크](https://beomseok95.tistory.com/288)

2. 구체적인 Interpreter 패턴 구현(**후위 표기법** 구현)

   [블로그 링크](https://hamait.tistory.com/199)

### 🥰 만든 이(전효희) 블로그 포스트: [링크](https://velog.io/@hoit_98/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-Interpreter-%ED%8C%A8%ED%84%B4)
