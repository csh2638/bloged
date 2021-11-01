---
layout: post
title: "[Java] Java Brush Up - 2"
categories: JAVA
tags: [JAVA]
---

## 조건문

### if

~~~java

if(true)
{
    System.out.println("true");
}
else
{
    System.out.println("false");
}

~~~

### switch 

~~~java

switch(2){
    case 1:
        System.out.println("1");
    case 2:
        System.out.println("2"); // 2 출력
    case 3:
        System.out.println("3");
        }

~~~


## 반복문

### while

~~~java

while(true){
   System.out.println("true"); 
}

int i = 0;
while(i >= 10){
    System.out.println(i); 
    i++;
}

~~~

### for

~~~java

for(int i = 0; i < 10, i++){
    if(i==5){
        continue;
    }
    if(i == 8){
        break;
    }
    System.out.println(i); 
}

~~~

## 배열

~~~java
public class practice {
    public static void main(String[] args) {
        // String[] code = { "a", "b", "c", "d", "e" };
        String[] code = new String[5];
        code[0] = "a";
        code[1] = "b";
        code[2] = "c";
        code[3] = "d";
        code[4] = "e";
        System.out.println(code.length);
        for (int i = 0; i < code.length; i++) {
            System.out.println(code[i]);
        }
    }

~~~

## for each

~~~java

public class practice {
    public static void main(String[] args) {
        String[] code = { "a", "b", "c", "d", "e" };
        for (String e : code) {
            System.out.println(e);
        }

    }
}

~~~

## 메소드(= 함수)

메소드는 다른 언어에서 함수라고 불리는 것이다. 

~~~java
public class practice {

    public static void printNum(int selnum) {
        for (int i = 0; i < selnum; i++) {
            System.out.println(i);
        }
    }

    public static void main(String[] args) {
        printNum(10);
    }

}
~~~

## String[] args

~~~java

public class practice {
    public static void main(String[] args) {
    }

}
~~~

main 메소드의 형태를 살펴보자. ***(String[] args)*** 부분은 String[] 즉 문자열을 담을수 있는 변수인 args라는 뜻이고 이 변수를 main메소드의 매개변수(parameter)로 사용한다는 해석이 된다. 


## 입력

~~~java

import java.util.Scanner;

public class practice {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int i = sc.nextInt();
        System.out.println(i * 1000);
        sc.close();
    }

}

~~~