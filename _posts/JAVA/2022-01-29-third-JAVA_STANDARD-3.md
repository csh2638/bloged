---
layout: post
title: "[Java] 자바의 정석 ch4. 조건문과 반복문"
categories: 자바의 정석 내용정리
tags: [JAVA, 자바의 정석]
---

## 이름붙은 반복문

반복문에 이름을 붙여서 하나 이상의 반복문을 벗어날 수 있다.

~~~java
package ex1;

public class test {
	public static void main(String [] args) {
		Loop1 : for(int i = 2; i<=9; i++) {
			for(int j = 1; j<=9; j++) {
				if(j==5) {
					break Loop1;
					break;
					continue Loop1;
					continue;
					System.out.println();
				}
			}
		}

~~~