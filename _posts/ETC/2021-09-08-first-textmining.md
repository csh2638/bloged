---
layout: post
title: "[과제] txt파일에서 원하는 형태로 데이터 뽑아내기"
categories: 과제
tags: [python, BigData]
---

과제 내용은 txt파일에 대해 특정한 조건들을 만족시킴과 동시에 문제가 요구하는 특정한 데이터를 출력하는 것이다.

### 조건<br>

1. 영문자, 숫자를 제외한 특수문자를 제외시킨다.
2. 단어의 개수를 셀 때 대문자가 포함된 단어의 경우 소문자로 변환후 센다.

## 소스코드

~~~python
from collections import Counter

f = open(r'C:/경로~~/파일명.txt')
text = (f.read()).lower()

symbols = [',', '.', ':', '-', '+', '/', '*', '&', '%', '[', ']', '(', ')', '"', "'", '?', '#', '$']

for symbol in symbols: 
    text = text.replace(symbol, '')

counts = Counter(sorted(text.split()))
for i in counts:  # 출력 1 :단어 오름 차순으로 정렬하여 출력
    print(i)

# counts = Counter(sorted(text.split())).most_common() 
# for i in range(len(counts)):   # 출력2: 단어 빈도순으로 내림 차순으로 정렬 (동일 빈도의 경우는 단어 오름차순으로 정렬)
#     print("단어 : {} , 나온 횟수 : {}".format(counts[i][0], counts[i][1]))
~~~

문제를 보자 boj 중에 최빈값을 구하는 문제가 떠올랐다.  [[백준] 2108 / 통계학](https://csh2638.github.io/boj/2021/08/31/first_boj_2108.html)<br> 
따라서 txt 파일을 가져와 특수기호만 제거한다면 쉽게 풀 수 있는 과제였다.<br>
문제가 요구하는 두 가지의 조건을 고려해 적절히 counter를 이용해 문제를 해결하였다.




