---
layout: post
title: "[Java] Java Brush Up - 4"
categories: JAVA
tags: [JAVA]
---

## 유효범위

~~~java

public class Practice {
    static int i;
     
    static void a() {
        int i = 0;
    }
 
    public static void main(String[] args) {
        for (i = 0; i < 5; i++) {
            a();
            System.out.println(i);
        }
    } 
}

~~~