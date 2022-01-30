---
layout: post
title: "[Java] 자바의 정석 ch5. 배열"
categories: 자바의 정석 내용정리
tags: [JAVA, 자바의 정석]
---

## 커맨드 라인을 통해 입력받기

~~~java
class EX5_7
    public static void main(String[] args){
        System.out.println("매개변수의 개수: " +args.length);
        for(int i=0; i<agrs.length; i++){
            System.out.println("args[" + i + "] = \"" + args[i] + "\"");
        }

    }
/** 
    입력 : c:\jdk1.8\wokr\ch5>java Ex5_7 abc 123 "Hello world"
    결과 : arg[0] = "abc"
           arg[1] = "123"
           arg[2] = "Hello world"

    c:\jdk1.8\wokr\ch5>java Ex5_7 abc 123 "Hello world"가 main 메소드의
    참조변수인 args(String 타입의 배열)에 들어가 사용됨 

**/ 
~~~

## String 클래스

- String 클래스는 char[]와 메서드를 결합한 것
- String 클래스는 변경할 수 없다. (read only)

![image](https://user-images.githubusercontent.com/78485996/151685933-f394c857-902b-4427-a15f-35b9c7458316.png)

위와 같은 경우 변수 a에 주소에 ab가 저장될것 같지만 a+b를 한 결과의 주소가 a에 저장되게 된다. 즉, 기존 a주소의 값이 변하지 않고 a의 주소가 변경되어 값이 변한다.

## Arrays로 배열 다루기

~~~java

//배열 출력
int[] arr = {0,1,2,3,4};
int[][] arr2D = {{11,12}, {21,22}};

System.out.println(Arrays.toString(arr)); // [0,1,2,3,4] 1차원 배열 확인
System.out.println(Arrays.deepToString(arr2d)); // [[11, 12], [21, 22]] 2차원 이상 배열 확인

//배열 비교
int[][] arr2D = new String[][] {{11,12}, {21,22}};
int[][] arr2D2 = new String[][] {{11,12}, {21,22}};

System.out.println(Arrays.equals(arr2D, arr2D2)); //false 1차원 배열 비교
System.out.println(Arrays.deepEquals(arr2D, arr2D2)); //false 2차원 이상 배열 비교

//배열 복사
int arr = {0,1,2,3,4};
int arr2 = Arrays.copyOf(arr, arr.length); // arr2 = [0,1,2,3,4] , arr 길이만큼
int arr3 = Arrays.copyOf(arr, 3); // arr3 = [0,1,2], 3개
int arr4 = Arrays.copyOf(arr, 7); // arr2 = [0,1,2,3,4,0,0,0], 7개
int arr5 = Arrays.copyOfRange(arr, 2, 4); // arr5 = [2,3], index 2~3
int arr6 = Arrays.copyOfRange(arr, 0, 7); // arr6 = [0,1,2,3,4,0,0,0], index 0~6

//정렬
int arr[] = {3,2,0,1,4}
Arrays.sort(arr); // [0,1,2,3,4] 오름차순 정렬


~~~
