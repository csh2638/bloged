---
layout: post
title: "[Java] 자바의 정석 ch2. 변수"
categories: 자바의 정석 내용정리
tags: [JAVA, 자바의 정석]
---


## 변수, 상수, 리터럴

변수 : 하나의 값을 저장하기 위한 공간

~~~java
int A = 100; //A -> 변수
A = 100;
~~~

상수 : 한 번만 값을 저장 가능한 변수(변경 불가)

~~~java
final int B = 100; //B -> 상수
B = 200; // 오류(이미 B=100으로 저장해서 변경불가)
~~~

리터럴 : 그 자체로 값을 의미하는 것

~~~java
int A = 10; // int = 변수형, A = 변수, 10 = 리터럴
final int B = 20; // final int = 변수형, B = 상수, 20 = 리터럴
char ch = 'A'; // char = 변수형, ch = 변수, 'A' = 리터럴
String str = "abc"; // String = 변수형, str = 변수, "abc" = 리터럴 
~~~


## 리터럴의 접두사와 접미사

기본적으로 접미사는 대소문자 구별하지 않는다.

논리형 : false, true
정수형 : 100L(Long), ob0101(ob: 2진 접두사)
실수형 : 3.14F(float), D(double)
문자형 : ''
문자열 : ""

~~~java
//논리형
bolean A = true; // true/false

//정수형
Long B = 100L;// 접미사 L은 생략가능, 앞에서 Long B라고 선언했기 때문

int C = 100; // 10진수(접두사 없음)
int D = 0100; // 8진수(접두사 0)
int E = 0x100; // 16진수(접두사 16)

//실수형
float F = 3.14f; /* 접미사 f 생략 불가, 생략할 경우 자동으로 
                    double타입으로 인식하기 때문에 float형에 double타입의
                    변수가 들어가기 때문에 오류 발생
                */
double G = 3.14d; // 접미사 d 생략가능

//문자형
char H = 'A'

//문자열
String I = "ABCD"

/*
추가
10. -> 10.0  접미사가 없어 double
.10 -> 0.10  접미사가 없어 double
10f -> 10.0f  접미사에 f가 붙어 float
le3 -> 1000.0d e는 10^n을 나타내므로 double
*/

~~~

## 기본형과 참조헝

기본형(Primitive type) : boolean, char, byte, short, int, long, float, double 총 8개
- 실제 값을 저장

참조형(Reference type) : 기본형을 제외한 나머지. String, System 등
- 메모리 주소를 저장

## printf() 지시자

~~~java
//다양한 진수로 출력하기.
System.out.printf("%d", 15); // 10진수
System.out.printf("%o", 15); // 8진수
System.out.printf("%x", 15); // 16진수
System.out.printf("%s", Integer.toBinaryString(15)); // 2진수

//접두사 붙이기
System.out.printf("%#o", 15); // 8진수 017
System.out.printf("%#x", 15); // 16진수 0xf
System.out.printf("%#X", 15); // 16진수 0Xf

//지수형식 출력
System.out.printf("%e", 3.142); // 3.142000e+00
System.out.printf("%g", 3.142); // 3.14200

//오른쪽 정렬
System.out.printf("[%5d]", 3.142); // [   10] 

//왼쪽 정렬
System.out.printf("[%-5d]", 3.142); // [10   ]

//빈칸을 채워서 오른쪽 정렬
System.out.printf("[%05d]", 10); // [00010]

~~~
