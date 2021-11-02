---
layout: post
title: "[Java] Java Brush Up - 3"
categories: JAVA
tags: [JAVA]
---

## 클래스와 인스턴스

~~~java

class Calculator {
    int left, right;

    public void setOperand(int left, int right) {
        this.left = left;
        this.right = right;
    }

    public void sum() {
        System.out.println(this.left + this.right);
    }

    public void avg() {
        System.out.println((this.left + this.right) / 2);
    }

}

public class practice {
    public static void main(String[] args) {
        Calculator c1 = new Calculator();
        c1.setOperand(10, 20);
        c1.sum();
        c1.avg();

        Calculator c2 = new Calculator();
        c2.setOperand(20, 40);
        c2.sum();
        c2.avg();

    }

}

~~~

클래스를 붕어빵을 만드는 틀, 인스턴스를 틀로 만든 붕어빵이라 생각하면 편하다.
main 메소드가 포함된 클래스 밖에서 class Calculator 을 통해 Calculator라는 클래스를 생성한다. 메인 메소드에 Calculator c1 = new Calculator()를 통해 Calculator라는 클래스에 대한 c1 이라는 이름의 인스턴스를 생한다. c1.setOperand(10, 20)를 통해 c1 인스턴스에 속한 메소드에 접근하고 매개변수를 전달할 수 있다.
setOperand라는 매소드에 this.left = left는 c1 인스턴스에서 사용할 변수를 할당하는 역할을 수행한다.

하나의 클래스에서 여러개의 인스턴스를 생설할 수 있다. 같은 클래스로 부터 생성된 인스턴스라도 사용하기에 따라 다른 값을 가질 수 있다.

## 클래스 변수

~~~java

class Calculator {
    int left, right;
    static int base = 0;
    static double PI = 3.14;

    public void setOperand(int left, int right) {
        this.left = left;
        this.right = right;
    }

    public void sum() {
        System.out.println(this.left + this.right + base);
    }

    public void avg() {
        System.out.println((this.left + this.right + base) / 2);
    }

}

public class practice {
    public static void main(String[] args) {
        Calculator c1 = new Calculator();
        c1.setOperand(10, 20);
        c1.sum();
        c1.avg();

        Calculator c2 = new Calculator();
        c2.setOperand(20, 40);
        c2.sum();
        c2.avg();

        Calculator2.base = 10;
        
        c1.sum();
        c1.avg();

    }

}

~~~

클래스 변수는 static double PI = 3.14;와 같이 사용될 수 있으며 생성된 인스턴스마다 같은 값을 공유하거나 변하면 안되는 값을 저장하기 위한 경우에 사용할 수 있다.

## 클래스 메소드

~~~java

class Calculator3{
  
    public static void sum(int left, int right){
        System.out.println(left+right);
    }
     
    public static void avg(int left, int right){
        System.out.println((left+right)/2);
    }
}
 
public class CalculatorDemo3 {
     
    public static void main(String[] args) {
        Calculator3.sum(10, 20);
        Calculator3.avg(10, 20);
         
        Calculator3.sum(20, 40);
        Calculator3.avg(20, 40);
    }
 
}
~~~

클래스 메소드는 인스턴스 생성 없이 클래스의 메소드를 사용할 수 있다. 인스턴스 간의 구분이 필요 없는 경우에 사용될 수 있다.