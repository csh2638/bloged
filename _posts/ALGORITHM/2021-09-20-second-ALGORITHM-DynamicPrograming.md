---
layout: post
title: "[알고리즘] Dynamic Programing(동적 계획법)"
categories: 알고리즘
tags: [python, algorithm, Dynamic Programing]
---

## Dynamic Programing (DP, 동적 계획법) 🤸

다이나믹 프로그래밍은 큰 문제를 작은 문제로 나누고, 같은 문제라면 한 번씩만 풀어 문제를 효율적으로 해결하는 알고리즘 기법이다.<br>
대표적인 예제인 피보나치 수열을 구하는 함수를 살펴보면 이해하기 수월하다.

~~~python
#재귀함수로 구현한 피보나치 수열
def fibo(x):
    if x== 1 or x == 2:
        return 1
    return fibo(x-1) + fibo(x-2)
~~~

![sdds](https://user-images.githubusercontent.com/78485996/134025960-b97cfe90-1282-49ac-9bd6-657140fed22b.PNG)

위 그림은 fibo(6)인 경우 함수가 재귀적으로 동작하는 모습이다. 빨간색 원(fibo(4))과 파란색 원(fibo(3))의 경우와 같이 같은 계산을 반복하는 것을 볼 수 있다.
인자값이 6과 같이 작을 경우에는 큰 문제가 없지만 인자값에 비례해 연산횟수가 기하급수적으로 증가하게 된다. 따라서 이 문제를 다이나믹 프로그래밍으로 해결해보고자 한다.

~~~python
d = [0]*100

def fibo(x):
    if x== 1 or x == 2:
        return 1
    if d[x] != 0:
        return d[x]
    
    d[x] = fibo(x-1) + fibo(x-1)
    return d[x]
~~~

다이나믹 프로그래밍을 구현하는 방법중 하나인 메모이제이션 기법을 이용해 피보나치 수열을 구하는 코드이다.

![ddd](https://user-images.githubusercontent.com/78485996/134029663-74a3257b-9ed4-4cfd-874b-8d274c37e77c.PNG)

그림에서도 볼 수 있듯이 특정한 인자값을 가진 fibo 함수의 계산 결과를 d[]에 저장한다. 이후 또다시 같은 인자값을 가진 함수, 즉 같은 계산을 하게 될 경우 이미 
저장된 값을 return하게 하여 계산 횟수를 줄일 수 있다. 결국 그림에서 빨간 원이 씌여져 있는 케이스만 계산을 하게 된다. 

## 메모이제이션 기법(Memoization)

다이나믹 프로그래밍을 구현하는 방법중 하나로, 한 번 구한 결과를 메모리 공간에 메모해두고 같은 식을 다시 호출하면 메모한 결과를 그대로 가져오는 방법을 이용한다.
이는 값을 저장하는 방법이므로 캐싱(Cashing)이라고도 부른다.




탑다운 방식(Top Down, 하향식): 큰 문제를 해결하기 위해 작은 문제를 호출

바텀업 방식(Bottom Up, 상향식): 작은 문제부터 답을 도출