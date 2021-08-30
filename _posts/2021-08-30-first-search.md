---
layout: post
title: "[알고리즘] 탐색 (순차 탐색, 이진 탐색, 트리 자료구조)"
categories: 알고리즘
tags: [python, algorithm, search]
---

## 탐색 (순차 탐색, 이진 탐색)

탐색은 데이터 안에서 원하는 정보를 찾는 것을 말한다. 데이터가 주어졌을때 얼마나 빠른 시간 안에 원하는 정보를 찾아내는 것이 중요하다.

## 순차 탐색(Sequential Search)

순차 탐색이란 리스트 안에 있는 특정한 데이터를 찾기 위해 앞에서부터 데이터를 하나씩 차례대로 확인하는 방법이다. 보통 정렬되지 않은 데이터에서 특정한 원소를 찾기 위해 사용한다. 파이썬의 내장함수인 count()에서도 순차 탐색이 수행된다. 데이터의 개수가 N개일때 최악의 경우 시간복잡도는 O(N)이다.

~~~python
# 순차 탐색
def sequential_search(n,target,array):
    for i in range(n):
        if array[i] == target:
            return i +1


N, M = list(map(int,(input().split()))) # N=찾고자 하는 원소, M=배열의 길이
arr = list(map(int,(input().split())))

print(sequential_search(M, N, arr))
~~~

## 이진 탐색(Binary Search)

이진 탐색은 배열 내부의 데이터가 정렬되어 있어야만 사용가능한 알고리즘이다. 시작점과 끝점, 중간점의 위치를 나타내는 3개의 변수를 데이터의 중간점과 반복적으로 비교하여 원하는 데이터를 찾아낸다. 반복할때 마다 절반씩 데이터가 줄어들기 때문에 시간복잡도는 O(logN)이다.

~~~python
# 이진 탐색
def binary_search(array, target, start, end):
    if start > end:
        return None
    mid = (start+end)//2

    if array[mid] == target:
        return mid
    elif array[mid] > target:
        return binary_search(array, target, start, mid+1)
    elif array[mid] < target:
        return binary_search(array, target, mid-1, end)        
    
N, M = list(map(int,(input().split()))) # N=찾고자 하는 원소, M=배열의 길이
arr = list(map(int,(input().split())))

result = binary_search(arr, N, 0, M-1)
print(result+1) # 원하는 원소의 인덱스번호
~~~

