---
layout: post
title: "[파이썬 크롤링] Beautiful soap4를 이용한 웹 크롤링"
categories: CRAWLING
tags: [PYTHON, CRAWLING]
---

## beuatiful soap4를 이용한 웹 크롤링

웹 페이지에서 사용자가 원하는 정보만 골라서 추출하기 위해 사용되는 기술을 웹 크롤링이라고 한다. 우선 간단하게 requests 라이브러리를 사용해 원하는 페이지에 해당하는 html정보를 가져올 수 있다.

~~~python
import requests

url = requests.get("http://naver.com")
print(url.text)
~~~

이 방법으로 가져온 html 정보 중에서 원하는 데이터만 찾기 위해 일종의 필터 역할을 수행할 무언가가 필요하다. 바로 이때 사용할수 있는 방법 중 하나가 beautiful soap4 라이브러리를 사용하는 것이다.

~~~python
import urllib.request
from bs4 import BeautifulSoup

url = "http://naver.com"
html = urllib.request.urlopen(url)
soup = BeautifulSoup(html, "html.parser")
print(soup)
~~~

requests를 사용하였을 때와 같은 결과를 나타내며 현재 soup라는 변수에 해당 웹 페이지의 html정보가 담겨져 있다. 이 정보를 bs4 함수를 통해 특정한 데이터들을 추출할 수 있다.

그렇다면 원하는 데이터에 어떻게 접근할 것인가를 생각해볼 수 있다. 그 답은 html 정보에 있다. html에는 여러 태그와 클래스이름, 아이디 이름이 있는데 주로 이를 통해 데이터에 접근한다. 클래스이름이나 아이디이름과 같은 경우 고유한 값을 가지기 때문에 접근하기에 용이하다.

보통 html의 어떤 태그에 원하는 데이터가 있는지 모르기 때문에 크롬 웹브라우저 기준으로 [F12]를 누르면 개발자 도구를 열 수 있다. 여기서 찾고자 하는 데이터의 태그가 무엇인지, 어느 태그가 상위에 있는지를 포함해 다양한 정보를 얻을 수 있다.

### select(), select_one

~~~python
import urllib.request
from bs4 import BeautifulSoup

url = "https://news.naver.com/main/main.naver?mode=LSD&mid=shm&sid1=100"
html = urllib.request.urlopen(url)
soup = BeautifulSoup(html, "html.parser")
data1 = soup.select(
    '#main_content > div > div._persist > div > div > div.cluster_body > ul > li > div.cluster_text > a')
data2 = soup.select_one(
    '#main_content > div > div._persist > div > div > div.cluster_body > ul > li > div.cluster_text > a')

for i in data1:
    print(i)
~~~

select(), select_one() 메소드는 soup에 할당되어있는 html에서 CSS selector로 tag를 찾아 원하는 데이터를 추출한다. select()와 select_one()의 차이는 select()는 해당하는 태그에 있는 데이터가 여러 개 일 경우 모두 리턴하고, select_one()는 해당하는 태그에 있는 데이터가 여러 개 이더라도 제일 처음에 발견된 데이터 하나만을 리턴한다. 

### find(), find_all()

~~~python
import urllib.request
from bs4 import BeautifulSoup

url = "https://news.naver.com/main/main.naver?mode=LSD&mid=shm&sid1=100"
html = urllib.request.urlopen(url)
soup = BeautifulSoup(html, "html.parser")
data1 = soup.find_all('a', {'class': 'cluster_text_headline nclicks(cls_pol.clsart)'})

for i in data1:
    print(i.get_text())

~~~

find(), find_all() 메소드는 soup에 할당되어 있는 html에서 태그와 class name, id name등으로 원하는 데이터를 특정하여 추출한다. 위 예제의 경우 a태그 중 class name이 cluster... 인 태그들을 모두 찾는 경우이다. find(), find_all() 메소드의 차이는 select()에서와 같이 여러개의 데이터가 있을때 제일 처음에 있는 데이터 하나만 리턴하는가, 여러 데이터가 있을때 모두 리턴하는가의 차이를 가진다.
