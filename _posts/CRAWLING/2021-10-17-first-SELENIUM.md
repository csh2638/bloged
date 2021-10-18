---
layout: post
title: "[파이썬 크롤링] Selenium과 크롬 웹드라이버를 이용한 웹 크롤링"
categories: CRAWLING
tags: [PYTHON, CRAWLING]
---

##  seleninum과 크롬 웹드라이버를 이용한 웹 크롤링

selenium과 크롬 웹드라이버를 통해 크롤링을 하면 웹 브라우저에 존재하는 로그인과 같은 버튼이나 검색창에 텍스트를 입력해 그 결과를 추출하는 등의 기능을 이용할 수 있다. 브라우저의 각 요소에 접근할 때는 보통 xpath를 이용한다.

~~~python
from selenium import webdriver
import time

chromedriver_dir = "C:\\chromedriver_win32\\chromedriver.exe"
driver = webdriver.Chrome(chromedriver_dir)

driver.get("https://www.bbq.co.kr/shop/shopList.asp")
time.sleep(2)
~~~

우선 사용중인 크롬 버전에 맞게 설치한 크롬 웹드라이버의 경로를 지정한다. 이후 get()함수를 이용해 특정한 웹 브라우저의 url을 입력하고 실행하게 되면 해당 url에 해당하는 페이지가 크롬으로 실행된다. time.sleep()으로 딜레이를 주는 이유는 다음 동작이 실행되기 전에 발생할 수 있는 버퍼링에 대비하기 위해서이다.

~~~python
element = driver.find_element_by_id("search_text")
element.send_keys("서울")
time.sleep(2)
~~~

driver에는 https://www.bbq.co.kr/shop/shopList.asp의 html정보가 담겨져 있다. 
위 예제에서는 find_element_by_id를 통해 검색창을 지정하였지만 find_element_by_class_name, find_element_by_css_selector 등 다양한 방법으로 html요소에 접근할 수 있다. element.send_keys("서울")를 통해 검색창에 '서울'이라는 텍스트를 작성할 수 있다.

~~~python
xpath = """//*[@id="search_form"]/div/button/img"""
element_sel_gu = driver.find_element_by_xpath(xpath).click()
time.sleep(2)
~~~

xpath에 검색창에 붙어있는 검색버튼의 xpath를 지정하고  driver.find_element_by_xpath(xpath).click()을 통해 그 버튼을 누르는 동작을 수행시킨다.
그 결과로 '서울'이라는 키워드로 검색된 결과들이 나오게 된다.

~~~python
source = driver.page_source
bs = bs4.BeautifulSoup(source, 'lxml')
entire =bs.find('div', class_='shop_sort')

subject, add, tel = [], [], []

subject_list = entire.find_all('p', class_='subject')
add_list = entire.find_all('p', class_='add')
tel_list = entire.find_all('p', class_='tel') 

for info in subject_list:
    subject.append(info.text)

for info in add_list:
    add.append(info.text)
    
for info in tel_list:
    tel.append(info.text)

print(len(subject), len(add), len(tel))

for i in range(len(subject)):
    if '서울' in add[i]: #상세 주소가 서울인 것만 출력
        print(subject[i], add[i], tel[i])
~~~

이제 원하는 데이터를 화면에 출력하는데 까지 성공했기 때문에 페이지에 있는 데이터들을 bs4를 이용해 차례대로 추출하면 된다.




