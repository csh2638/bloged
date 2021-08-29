---
layout: post
title: "[알고리즘] 정렬 (선택 정렬, 삽입 정렬, 퀵 정렬, 계수 정렬)"
categories: 알고리즘
tags: [python, algolithm, sort]
---

## 정렬 (선택 정렬, 삽입 정렬, 퀵 정렬, 계수 정렬)

정렬은 데이터를 특정한 기준에 따라 순서대로 나열하는 것을 말한다.   
예를 들어 1~10 까지의 숫자가 한 리스트에 숫자 크기에 상관 없이 나열되어있다 생각해보자.
이 숫자들을 1부터 10까지 크기 순으로 나열하려면 어떻게 해야할까? 이때 사용할 수 있는게 정렬 알고리즘이다. 이 문제의 경우 파이썬의 내장함수인 sort()를 이용해 간단하게 해결할 수 있다. 하지만 sort()를 사용하지 않고 숫자들을 정렬할 수 있는 방법들을 알아보았다.

### 1. 선택 정렬(Selection Sort) 

선택정렬은 무작위의 여러 데이터들 중에서 가장 작은 데이터를 선택해 맨 앞에 데이터와 바
꾸고, 두번째로 작은 데이터를 두번째 데이터와 바꾸고... 를 반복해 데이터를 정렬한다.
선택정렬의 시간복잡도는 대략 O(N^2)이라고 볼 수 있다. 또한 데이터가 10,000개 이상일때 속도가 급격히 감소하는 단점이 있다.

~~~python
# 선택정렬
# array = [7,5,9,0,3,1,6,2,4,8]

# for i in range(len(array)):
#     min_index = i        # 가장 작은 원소의 인덱스
#     for j in range(i+1, len(array)): # 가장작은 원소를 제외한 나머지 원소들 중에서
#         if array[min_index] > array[j]:
#             min_index = j # 가장 작은 원소의 인덱스 번호 = j
#     array[i], array[min_index] = array[min_index], array[i]
# print(array)
~~~

### 2. 삽입 정렬(Insertion Sort) 

삽입 정렬은 무작위의 여러 데이터들 중에서 특정한 데이터를 적절한 위치에 삽입하여 정렬한다. 특정한 데이터가 적절한 위치에 들어가기 이전에, 그 앞까지의 데이터는 이미 정렬되어 있다고 가정한다는 특징을 가진다. 삽입 정렬의 시간복잡도는 리스트가 거의 정렬되어있는 상태인 최선의 경우 O(N)이고, 최악의 경우 O(N^2)이다.

~~~python
# 삽입정렬
# array = [7,5,9,0,3,1,6,2,4,8]

# for i in range(len(array)):
#     for j in range(i, 0, -1):
#         if array[j] < array[j-1]:
#             array[j], array[j-1] = array[j-1], array[j]
#         else:
#             break
# print(array)
~~~

### 3. 퀵 정렬(Quick Sort) 

퀵 정렬은 주어지는 리스트 원소 중에 하나를 골라 피벗(pivot)이라고 설정한다. 피벗보다 큰 수와 작은수로 분류해 일종의 파티션을 형성한다. 형성된 파티션 속에서 각각의 새로운 피벗을 설정하고 분류를 반복하며, 반복이 종료되었을때 리스트의 원소들이 정렬된다.
 퀵 정렬의 평균 시간복잡도는 O(NlogN)이며 최악의 경우에도 O(N^2)를 보장한다. 특이하게 데이터가 정렬되어 있을수록 느리게 동작한다.

~~~python
array = [5,7,9,0,3,1,6,2,4,8]

def quick_sort(array, start, end):
    if start >= end: # 원소의 개수가 1개인 경우 종료
        return
    pivot = start # 피벗을 첫번째 원소로 지정
    while left <= end and array[left] <= array[pivot]:
        left += 1
    while right > start and array[right] >= array[pivot]:
        right -= 1
    if left > right:
        array[right], array[pivot] = array[pivot], array[right]
    else:
        array[left], array[right] = array[right], array[left]
    quick_sort(array, strat, right -1)
    quick_sort(array, right+1, end)

quick_sort(array, 0, len(array)-1)
print(array)
~~~

~~~python
array = [5,7,9,0,3,1,6,2,4,8]
def quick_sort(array):
    if len(array) <= 1:
        return array
    pivot = array[0]
    tail = array[1:]

    left_side = [x for x in tail if x <= pivot]
    right_side = [x for x in tail if x > pivot]

    return quick_sort(left_side) + [pivot] + quick_sort(right_side)
print(quick_sort(array))
~~~

### 4. 계수 정렬(Count Sort) 

계수 정렬은 모든 데이터가 양의 정수이며 크기 범위가 제한되어 있으며 가장 큰 데이터와 가장 작은 데이터의 크기 차이가 1,000,000을 넘지 않을때 효과적으로 사용이 가능하다.
이때 데이터의 개수가 N, 최대값이 K일때 최악이더라도 O(N+K)를 보장한다. 다만 데이터의 모든 범위를 담을 수 있는 크기의 리스트(배열)을 선언해야 하기 때문에 너무 큰 데이터를 사용할 수 없다.

~~~python
array = [7,5,9,0,3,1,6,2,9,1,4,8,0,5,2]
count = [0] * (max(array) + 1)

for i in range(len(array)):
    count[array[i]] += 1

for i in range(len(count)):
    for j in range(count[i]):
        print(i, end=' ')

~~~