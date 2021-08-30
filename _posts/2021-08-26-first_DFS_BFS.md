---
layout: post
title: "[알고리즘] 탐색 알고리즘 DFS/BFS"
categories: 알고리즘
tags: [python, algorithm, DFS/BFS algolythm]
---

##  탐색 알고리즘 DFS/BFS

탐색이란 많은 양의 데이터에서 원하는 데이터를 찾는 과정을 뜻한다. 대표적인 탐색 알고리즘인 DFS/BFS가 있다.<br>
탐색 알고리즘을 보기 전에 그래프를 알고 가야한다. 그래프는 정점(node)와 간선(edge)로 표현된다. 두 노드가 간선으로 연결되 있는것을 두 노드가 인접한다(adjacent)라고 표현한다.<br>
![ddddd](https://user-images.githubusercontent.com/78485996/131140510-86c453d2-1078-4273-8379-2137f073d122.PNG)


### DFS 알고리즘(Depth-First Search)

깊이 우선 탐색으로 그래프에서 깊은 부분을 우선적으로 탐색하는 알고리즘이다. 스택, 재귀함수를 이용하여 구현한다. 한 노드에서 갈수 있는 노드까지 들어가면서 탐색하는 특징을 가진다.


~~~python
#DFS 메서드 정의
def dfs(graph, v, visited):
    #현재 노드를 방문 처리
    visited[v] = True
    print(v, end = ' ')
    #현재 노드와 연결된 다른 노드를 재귀적으로 방문
    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)
# 각 노드가 연결된 정보를 리스트 자료형으로 표현(2차원리스트)
graph = [
    [],
    [2,3,8],
    [1,7],
    [1,4,5],
    [3,5],
    [3,4],
    [7],
    [2,6,8],
    [1,7],
]
#각 노드가 방문한 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9
#정의된 DFS함수 호출
dfs(graph, 1, visited)
~~~

### BFS 알고리즘(Breadth First Search)

너비 우선 탐색으로 그래프에서 가까운 노드부터 우선적으로 탐색하는 알고리즘이다. 큐를 이용해 구현하며 파이썬에서는 큐를 구현하기 위해 deque를 이용한다. 현재 노드에서 가까운 노드를 우선적으로 탐색하는 특징을 가진다. 미로나 최단거리를 구하는 문제에서 유리하다. 하지만 검색 규모가 크다면 DFS를 사용도 고려해야한다.

~~~python
from collections import deque

#BFS매서드 정리
def bfs(graph, start, visited):
    #큐(que) 구현을 위해 deque 라이브러리 사용
    queue = deque([start])
    #현재 노드를 방문 처리
    visited[start] = True
    #큐가 빌 때 까지 반복
    while queue:
        #큐에서 하나의 원소를 뽑아 출력
        v = queue.popleft()
        print(v, end=' ')
        #해당 원소와 연결된, 아직 방문하지 않은 원소들을 큐에 삽입
        for i in graph[v]:
            if not visited[i]:
                queue.append(i)
                visited[i] = True
# 각 노드로 연결된 정보를 리스트 자료형으로 표현(2차원리스트)
graph = [
    [],
    [2,3,8],
    [1,7],
    [1,4,5],
    [3,5],
    [3,4],
    [7],
    [2,6,8],
    [1,7],
]
#각 노드가 방문한 정보를 리스트 자료형으로 표현(1차원 리스트)
visited = [False] * 9
#정의된 bFS함수 호출
bfs(graph, 1, visited)
~~~

