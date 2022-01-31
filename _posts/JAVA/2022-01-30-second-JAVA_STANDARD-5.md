---
layout: post
title: "[Java] 자바의 정석 ch6. 객체지향 언어"
categories: 자바의 정석 내용정리
tags: [JAVA, 자바의 정석]
---

## 클래스와 객체

- 클래스란 객체를 정의해 놓은것
- 클래스는 객체를 생성하는데 사용

- 객체 = 속성(변수) + 기능(메서드)

- 객체 : 모든 인스턴스를 대표하는 일반적인 용어
- 인스턴스 : 특정 클래스로부터 생성된 객체

클래스를 인스턴스(객체)로 만드는것을 인스턴스화라고 한다.


## 하나의 소스파일에 여러 클래스 작성

- public class가 있는 경우 소스파일의 이름은 반드시 public class의 이름과 일치해야 한다. public class가 하나도 없는 경우 소스파일의 이름은 상관없다.

- 메인 메소드가 들어있는 클래스의 이름과 자바파일명이 동일해야한다.

## 객체의 생성과 사용

~~~java
Tv t; // Tv클래스 타입의 참조변수 t를 선언
t = new Tv(); //TV인스턴스를 생성한 후, 생성된 Tv인스턴스의 주소를 t에 저장

// 다른 참조변수에 동일한 인스턴스에 연결되면 각자 다른 클래스로 분기됨.
// t의 TV()와 t1의 Tv()가 각각 다름
Tv t = new Tv();
Tv t1 = new Tv();

t1 = t;
//이 경우 t1과 연결되어있던 Tv()가 끊기고 t와 연결된 Tv()에 연결된다.
// 연결이 끊긴 기존 t1의 Tv()는 CG(가비지 컬렉터)가 처리한다.

~~~

## 객체 배열

- 객체 배열 == 참조변수 배열

~~~java

//이 경우 t1, t2, t3 각각 다른 Tv()객체에 연결
Tv t1 = new Tv();
Tv t2 = new Tv();
Tv t3 = new Tv();

// 이 경우 TvArr[0], TvArr[1], TvArr[2]가 배열로 이루어져 있고 각각 다른 Tv()객체에 연결 
Tv[] tvArr = new TV[3];
TvArr[0] = new Tv();
TvArr[1] = new Tv();
TvArr[2] = new Tv();

~~~


## 클래스의 정의(1)

- 클래스 == 데이터 + 함수

## 클래스의 정의(2)

- 사용자 정의 타입 : 원하는 타입을 직접 만들 수 있다. ex/시간, 등등


## 선언 위치에 따른 변수의 종류

~~~java

Class Variables{
    int iv; //인스턴스 변수
    static int cv; // 클래스 변수(static + 인스턴스 변수)

    void method(){
        int lv = 0; // 지역변수
    }

}

~~~

|변수의 종류|선언 위치|생성 시기|
|------|---|---|
|클래스 변수|클래스 영역|클레스가 메모리에 올라갈때|
|인스턴스 변수|클래스 영역|인스턴스가 생성되었을때|
|지역변수|클래스 영역 이외의 영역|변수 선언문이 수행되었을때|


## 클래스 변수와 인스턴스 변수

- 인스턴스 변수
개별적인 속성을 사용할때. 카드의 무늬, 숫자

- 클래스 변수
공통적인 속성을 사용할때. 카드의 가로, 세로, 두께 등 

~~~java
class Card{
    String kind;//무늬 -인스턴스 변수
    int number;// 숫자 -인스턴스 변수

    static int width = 100; //폭 -클래스 변수
    static int height = 250;//높이 -클래스 변수

}
public static void main(){
    Card c = new Card();
    c.kind = "HEART";
    c.number = 5;
    /*
    c.width = 200; //사용은 가능하지만 권장하지 않음
    c.height = 300;
    */
    Card.width = 200; //클래스 변수를 사용할땐 클래스 이름을 붙여 사용
    Card.height = 300;

}

~~~

## 호출스택 (call stack)

- 메소드 수행에 필요한 메모리가 제공되는 공간
- 메소드가 호출되면 호출스택에 메모리 할당, 종료되면 해제
- 맨 위의 메서드 하나만 실행중이고 나머지는 대기중
- 아래 있는 메서드가 위의 메소드를 호출한 것

![image](https://user-images.githubusercontent.com/78485996/151705359-6ebae1eb-00a8-4984-87c5-f17de997800c.png)


##  기본형 매개변수

- 기본형 매개변수 : 변수의 값을 읽기만 할 수 있다.(read only)

~~~java
class Date {
    int x
}
class Ex_6{
    public static void main(String [] args){
        Data d = new Data();
        d.x = 10;
        System.out.println("main() : " + d.x); 

        change(d.x);
        System.out.println("After change(d.x)");
        System.out.println("main() : " + d.x); 
    }
    static void change(int x){
        x = 1000;
        System.out.println("change() : " + x);
    }

    /*
    main() : 10
    change() : 1000
    After change(d.x)
    main() : 10

    change 메소드의 지역변수 x가 1000이라 메인 메소드의 d.x에 영향 없음
    */
}
~~~

## 참조형 매개변수

- 참조형 매개변수 : 변수의 값을 읽고 변경할 수 있다.(read & write)

~~~java
class Date {
    int x
}
class Ex_6{
    public static void main(String [] args){
        Data d = new Data();
        d.x = 10;
        System.out.println("main() : " + d.x); 

        change(d.x);
        System.out.println("After change(d.x)");
        System.out.println("main() : " + d.x); 
    }
    static void change(Data2 x){// 참조형 변수 = 값이 아닌 주소가 할당됨 즉 d.x의 주소가 할당되어 있음. data2 x의 값이 변하면 d.x의 값도 변함.
    같은 주소를 사용하기 때문
        d.x = 1000;
        System.out.println("change() : " + x);
    }

    /*
    main() : 10
    change() : 1000
    After change(d.x)
    main() : 1000

    */
}
~~~


## static 메소드와 인스턴스 메소드

인스턴스 메소드
- 인스턴스 생성 후 '참조변수.메소드명()'으로 호출
- 인스턴스 멤버(인스턴스 변수, 인스턴스 메소드 )와 관련된 작업을 하는 메소드
- 메서드 내에서 인스턴스 변수 사용 가능

static 메소드(클래스 메소드)
- 객체 생성없이 '클래스이름.메서드이름()'으로 호출. 
- 인스턴스 멤버와 관련없는 작업을 하는 메소드
- 메소드 내네서 인스턴스 변수 사용불가


~~~java
class MyMath2{
    long a, b;

    long add(){ // 인스턴스 메소드, iv(인스턴스 변수) 사용
        return a + b;
    }

    static long add(long a, long b){ //클래스 메소드(static 메소드) iv(인스턴스 변수) 사용하지 않음. 사용할 경우 static 못붙임
        return a + b;
    }
}

class math test{
    public static void main(String args[]){
        System.out.println(MyMath2.add(200L, 100L)); //클래스 메소드 호출
        MyMath2.mm = new MyMath2(); // 인스턴스 생성
        mm.a = 200L;
        mm.b = 100L;
        System.out.println(mm.add()); //인스턴스 메소드 호출

    }
    
}


~~~

## static을 언제 붙여아 할까?

- 속성(멤버변수) 중에서 공통 속성에 static을 붙인다.
- 인스턴스 멤버를 사용하지 않는 메서드에 static을 붙인다.


## 메서드 간의 호출과 참조

~~~java
class TestClass{
    int iv; //인스턴스 변수
    static int cv; //클래스 변수

    void instanceMethod(){ //인스턴스 메서드- 객체 생성 후 호출가능
        System.out.println(iv); //인스턴스 변수 사용가능
        System.out.println(cv); //클래스 변수 사용가능
    }

    static void staticMethod(){ //static 메서드
        System.out.println(iv); //에러, 인스턴스 변수 사용불가
        System.out.println(cv); //클래스 변수 사용가능
    }

}
~~~


## 오버로딩(Overloading)

- 한 클래스 안에 같은 이름의 메서드 여러 개 정의하는 것

오버로딩이 성립하기 위한 조건

1. 메서드 이름이 같아야 한다.
2. 매개변수의 개수 또는 타입이 달라야 한다.
3. 반환타입은 영향이 없다.

~~~java

//오버로딩이 성립하는 예시
int add(int a, float b){
    return a + b;
}

long add(long a, float b){
    return a + b;
}

int add(int[] a){
    System.out.println(a);
    return result;
}

~~~

## 생성자(Constructor) = iv 초기화 메소드

- 인스턴스가 생성될 때마다 호출되는 인스턴스 초기화 메소드
- 인스턴스 생성시 수행할 적업(iv 초기화)에 사용

~~~java
Time t = new Time();
t.hour = 12;
t.minute = 34;
t.second = 56;

Time t = new Time(12,34,56);
~~~

- 이름이 클래스 이름과 같아야 한다.
- 리턴값이 없다.
- 모든 클래스는 반드시 생성자를 가져야 한다.

~~~java
class Card{
    Card(){ //매개변수가 없는 생성자
        //인스턴스 초기화 작업
    }
    Card(String kind, int number){ //매개변수 있는 생성자
        //인스턴스 초기화 작업
    }

}

~~~

![image](https://user-images.githubusercontent.com/78485996/151762767-9ff2613d-2efa-4f71-87be-7b136df551f0.png)


## 기본 생성자(default constructor)

- 매개변수가 없는 생성자  
- 생성자가 하나도 없을 때만, 컴파일러가 자동으로 추가

~~~java
클래스이름(){} // 기본생성자
point(){}//point클래스의 기본생성자
~~~


## 매개변수가 있는 생성자

![image](https://user-images.githubusercontent.com/78485996/151763268-2f857218-25dc-4446-aa34-ae1cc40944aa.png)


## 생성자 this()

- 생성자에서 다른 생성자를 호출할 때 사용
- 다른 생성자 호출시 첫 줄에서만 사용가능

~~~java
class Car2{
    String color;
    String gearType;
    int door;

    Car2(){
        this("white", "auto", 4);
    }
    Car2(String color){
        this("color", "auto", 4);
    }
    Car2(){
        a = 4; 
        Car("white", "auto", 4); //두번째 줄이고, this가 아닌 car을 써서 안됨
    }
    Car(String color, String gearType, int door){
        this.color = color;
        this.gearType = gearType;
        this.door = door;
    }
}

~~~


## 참조변수 this

- 인스턴스 자신을 가리키는 참조변수
- 인스턴스 메서드(생성자 포함)에서 사용 가능
- 지역변수(lv)와 인스턴스 변수(iv)를 구별할 때 사용

~~~java
    Car(String color, String gearType, int door){
            this.color = color;
            this.gearType = gearType;
            this.door = door;
        }
~~~

## 참조변수 this와 생성자 this

- this : 인스턴스 자신을 가리키는 참조변수. 인스턴스의 주소가 저장되어있다. 모든 인스턴스 메서드에 지역변수로 숨겨진 채로 존재한다.

- this(), this(매개변수) : 생성자. 같은 클래스의 다른 생성자를 호출할 때 사용한다.

## 변수의 초기화

- 지역변수(lv)는 수동초기화 해야함(사용전 꼭!)
- 멤버변수(iv, cv)는 변수형에 따라 자동 초기화된다.

|자료형|기본값|
|-----|-----|
|boolean|false|
|char|'\u0000'|
|byte, short, int|0|
|long|0L|
|float|0.0f|
|double|0.0d 또는 0.0|
|참조형|null|

1. 명시적 초기화(=)

~~~java
class Car{
    int door = 4; //기본형(primitive type) 변수의 초기화
    Engine e = new Engine(); //참조형(reference type) 변수의 초기화
}
~~~

2. 초기화 블럭

- 인스턴스 초기화 블럭 : {}
- 클래스 초기화 블럭 : static{}

3. 생성자

~~~java
    Car(String color, String gearType, int door){
            this.color = color;
            this.gearType = gearType;
            this.door = door;
        }
~~~

