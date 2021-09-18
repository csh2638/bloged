---
layout: post
title: "[HTML] HTML 정리"
categories: HTML
tags: [HTML]
---

# HTML (Hyper Text Markup Language)
***HTML***은 Hyper Text Markup Language의 약자로 웹 페이지를 구성하기 위해 제목, 단락, 목록 등과 같은 것들을 정해진 서식을 통해 구조적인 문서로 만들 수 있게 해주는 마크업 언어이다. HTML에서 어디선가 많이 들어본 단어가 있다. 바로 **마크업 언어**이다.
지금 이 글을 작성하는데 사용하는게 **마크다운 언어**이다. 그렇다면 마크업 언어와 마크다운 언어의 차이가 무엇일까?

## 마크업 언어(Mark Up Language) vs 마크다운 언어(Mark Down Language)🔥

간단하게 보자면 기존에 마크업 언어의 경량화 버전이 마크다운 언어라고 할 수 있다. 마크업 단어의 경우 여러 태그들과 기능들이 체계적으로 구성되어 문서의 구조를 더 세밀하게 표현할 수 있는 반면, 마크다운의 경우 간단한 태그들만 이용해 일반적인 텍스트 파일을 작성하는데 유리하다. 실제로 사용되는 태그들을 보면 서로 닮은점을 꽤 발견할 수 있다.

### HTML 구조

~~~html
<!DOCTYPE html> 
<html>

<head>
</head>

<body> 
</body>

</html>
~~~

HTML의 구조를 살펴보면 제일 위에 <!DOCTYPE html>이 있다. 이는 해당 페이지의 HTML 버전이 무엇인지 웹 브라우저에 알려주는 기능을 수행하며 예제에서의 경우 **HTML5**를 사용할 것이라는 뜻을 가진다. 그 다음으로 html태그가 전체를 감싸고 있고 그 안에 head태그와 body태그가 있다. head태그의 경우 html에서 사용할 스타일 시트의 위치나 사용할 언어를 인코딩 해줄 메타데이터 등의 요소를 저장한다. 웹 페이지를 구성하면서 직접적으로 보이지는 않지만 다양한 사전작업을 하는 공간이라 볼 수 있겠다. body태그는 구성한 웹 페이지에서 직접 보게되는 부분이며 컨텐츠 영역이라고도 한다. 텍스트나 이미지 뿐만 아니라 하이퍼링크, 버튼 등 필요에 따라 다양한 태그를 이용해 구현할 수 있다.

## HTML Tag 정리

tag는 <>로 둘러쌓인 것들로 안에 들어갈 태그명에 따라 다양한 기능을 수행한다. 자주 쓰일 것 같은 Html 태그를 정리해 보았다.(ctrl+F)로 찾기.

1.\<!-- --> 주석

~~~html
<!--"주석 내용"-->
~~~

</br>

2.\<a> 하이퍼링크 

~~~html
<a href="https://csh2638.github.io/">내 블로그로 이동</a>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|href|URL|링크의 좌표를 명시함.|
|type|미디어 타입|링크된 문서의 미디어 타입을 명시|

</br>

3.\<area> 이미지에서 하이퍼링크가 연결될 영역 정의(이미지에 하이퍼링크 적용)

~~~html
<img src="/examples/images/img_imagemap.jpg" alt="블로그 이동" usemap="#html" style="width:100px; height:100px">
<map name="html"> <!--이미지 usemap과 동일한 이름이어야 한다.-->
    <area shape="rect" alt="블로그 이동" href="https://csh2638.github.io/">
</map>
~~~


|속성명|속성값|설명|
|:---:|:---:|:---:|
|alt|텍스트|이미지가 나오지 않을때 나오는 대체 텍스트|
|coords|좌표|이미지 영역의 좌표를 명시|
|href|URL|해당 영역에 연결된 하이퍼링크의 대상 URL을 명시|
|shape|default,rect,circle,poly|영역의 모양 명시

</br>

4.\<audio> 음악이나 사운드를 정의

~~~html
<audio controls>
    <source src="/사운드경로/파일명.mp3" type="audio/mpeg">
</audio>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|autoplay|autoplay|사운드 준비가 끝나면 자동으로 실행|
|controls|controls|플레이 버튼, 정지버튼과 같은 실행제어 UI 명시|
|loop|loop|사운드 재생이 끝나면 다시 재생됨을 명시|
|muted|muted|사운드가 음소거임을 명시|
|preload|auto,metadata,none|페이지가 로드될때 사운드 파일이 같이 로드되어야 하는지 여부와 방법을 명시|
|src|URL|사운드 파일의 위치를 URL로 명시|

</br>

5.\<br /> 줄바꿈

~~~html
<p>
    안녕하세요.<br>
    오늘은 줄바꿈에 대해<br>
    알아보겠습니다.
</p>
~~~

<br>

6.\<button> 버튼

~~~html
<button type="button" onclick="alert('확인을 누르셨습니다.')">확인</button>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|autofocus|autofocus|페이지가 로드될때 자동으로 포커스가 버튼으로 이동하는것을 명시|
|disable|disable|버튼이 비활성화 됨을 명시|
|formmethod|get,post|폼 데이터가 서버로 제출될 때 사용할 HTTP메소드를 명시, submit일때만 사용가능|
|form|form id|버튼이 포함될 하나 이상의 \<form> 요소를 명시|
|name|텍스트|버튼의 이름을 명시|
|type|button,reset,submit|버튼의 타입을 명시|
|value|텍스트|버튼의 초기값을 명시|

<br>

7.\<datalist> 텍스트박스에서 선택할 수 있는 리스트를 드롭다운 형식으로 보여줌

~~~html
    좋아하는 과일 : <input type="text" name="fruit" list="fruit_list"><br>
    이름 : <input type="text" name="name"><br><br>
    <datalist id="fruit_list">
        <option value="수박"></option>
        <option value="사과"></option>
        <option value="배"></option>
        <option value="체리"></option>
    </datalist>
    <button type="submit">제출하기</button>
</form>
~~~

<br>

8.\<details> 접기, 펴기를 이용해 글을 숨기거나 보여줌

~~~html
<details>
    <summary>펼쳐서 더보기</summary>
        <p>접으면 안보입니다.</p>
</details>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|open|open|\<details>요소가 사용자에게 보이도록 펼처지는 것을 명시|

<br>

9,\<div> 특정 영역이나 구획을 정할때 사용, 블록으로 나누고자 할때

<br>

10.\<fieldset> \<form>요소에서 관련된 요소들을 하나의 그룹으로 묶을때 사용

~~~html
  <fieldset>
            <legend>로그인</legend>
            아이디 : <input type="text" name="user_id"><br>
            비밀번호 : <input type="password" name="user_pw"><br>
            <button type="submit">제출하기</button>
        </fieldset>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|disabled|disabled|fieldset에 포함된 요소들이 비활성화됨을 명시|
|name|이름|fieldset의 이름을 명시|

<br>

11.\<form> 사용자로부터 입력을 받을 수 있는 HTLM 입력 폼(form)을 정의할 때 사용.

\<button>\<fieldset>\<input>\<label>\<option>\<optgroup>\<select>\<textarea> 중 하나 이상을 포함할 수 있다.

~~~html
<form action="./login.html">
    아이디 : <input type="text" name="user_id"><br>
    비밀번호 : <input type="password" name="user_pw"><br>
    <input type="submit">
</form>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|action|URL|폼 데이터가 서버로 제출될 때 해당 데이터가 도착할 URL 명시|
|autocomplete|on,off|자동 완성 기능을 사용할지 여부를 명시|
|enctype|application,form-data,text,plain|폼 데이터가 서버로 제출될때 데이터의 인코딩 방식 명시
|method|get,post|폼 데이터가 서버로 제출될 때 사용되는 HTTP 메소드를 명시|
|name|텍스트|\<form>의 이름을 명시|
|novalidate|novalidate|폼 데이터가 서버로 제출될 때 해당 데이터의 유효성 검사를 하지 않음을 명시|

<br>

12.\<head> 문서에 대한 정보인 메타데이터의 집합을 정의할때 사용

~~~html
<!DOCTYPE html>
<html>
<head>
    <title>문서의 title.</title>
</head>
<body>
    문서의 콘텐츠 영역
</body>
</html>
~~~

<br>

13.\<h1> ~ \<h6> 문서에서 제목을 정의할때 사용. 숫자와 글자 크기가 비례한다.

~~~html
<h1>h1</h1>
<h2>h2</h2>
<h3>h3</h3>
<h4>h4</h4>
<h5>h5</h5>
<h6>h6</h6>
~~~

<br>

14.\<html> HTML요소를 포함하기 위한 컨테이너이며, 브라우저가에게 해당 문서가 HTML문서임을 알려주는 역할을 한다.

~~~html
<!DOCTYPE html>
<html>
<head>
    <title>문서의 title</title>
</head>
<body>
    문서의 contents
</body>
</html>
~~~

<br>

15.\<iframe> 인라인 프레임을 정의할때 사용. 현재 HTML에 다른 문서를 포함시킬때 사용.

~~~html
<iframe src="https://csh2638.github.io">
</iframe>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|sandbox|allow-forms<br>allow-pointer-lock<br>allow-popups<br>allow-same-origin<br>allow-scripts<br>allow-top-navigation|\<iframe>요소에 보일 컨텐츠에 대한 추가적인 제한사항들의 집합을 명시|
|src|URL|\<iframe>요소에 보일 문서에 보일 URL명시|
|srcdoc|HTML코드|\<iframe>요소에 보일 웹 페이지의 HTML코드를 명시|
|width|픽셀|\<iframe>요소의 너비를 명시|

<br>

16/\<img> HTML문서에서 이미지를 정의할때 사용. 반드시 src, alt 속성 명시

~~~html
<img src="/경로/그림.png" alt="그림이름">
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|alt|텍스트|이미지 로드 실패시 보여줄 대체 텍스트|
|height|픽셀|이미지의 높이 명시|
|longdec|URL|이미지에 대한 부가적인 설명을 제공하는 URL 제공|
|size|텍스트|서로 다른 페이지 레이아웃에 대한 이미지 소스 크기를 명시|
|src|URL|이미지 소스의 URL을 명시|
|width|픽셀|이미지의 너비를 명시함|

<br>

17.\<input> 사용자로부터 입력받을 수 있는 input filed를 정의할때 사용. input field를 선언하기 위해 form 내부에서 사용됨

~~~html
<form action="./login.html">
    아이디 : <input type="text" name="user_id"><br>
    비밀번호 : <input type="password" name="user_pw"><br>
    <input type="submit">
</form>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|autocomplete|on<br>off|자동 완성 기능을 사용할지 여부를 명시|
|autofocus|autofocus|페이지가 로드될 때 자동으로 포커스가 input으로 이동됨을 명시
|disabled|disabled|input이 비활성화됨을 명시|
|form|form id|input이 포함될 하나 이상의 form을 명시|
|frommethod|post<br>get|폼 데이터가 서버로 제출될 때 해당 데이터가 인코딩 되는 방식을 명시|
|formnovalidate|formnovalidate|폼 데이터가 서버로 제출될 때 해당 데이터의 유효성 검사를 하지 않음을 명시함|
|max|숫자<br>날짜|input의 최대값을 명시|
|multiple|multiple|input에 사용자가 둘 이상의 값을 입력할 수 있음을 명시|
|name|텍스트|input의 이름을 명시|
|placeholder|텍스트|input에 입력될 값에 대한 짧은 힌트를 명시|
|readonly|readonly|input의 입력필드가 읽기 전용임을 명시|
|required|required|폼 데이터가 서버로 제출되기 전 반드시 채워져 있어야 하는 입력 필드를 명시.|
|size|숫자|input의 너비를 문자수 단위로 명시|
|step|숫자|input에 입력할 수 있는 숫자들 사이의 간격을 명시|
|value|텍스트|input의 초기값을 명시|
|width|픽셀|input의 너비를 픽셀 단위로 명시|

<br>

18.\<label> 사용자 인터페이스 요소의 라벨을 정의할때 사용

~~~html
<form action="select_age.html" method="get">
    나이대를 골라보세요.<br>
    <input type="radio" name="ages" id="teen" value="teenage">
    <label for="teen">10대</label><br>
    <input type="radio" name="ages" id="twenty" value="twenties">
    <label for="twenty">20대</label><br>
    <input type="radio" name="ages" id="thirty" value="thirties">
    <label for="thirty">30대</label><br>
    <input type="radio" name="ages" id="forty" value="forties">
    <label for="forty">40대 이상</label><br>
    <input type="submit">
</form>
~~~

<br>

19.\<meta> 해당 문서에 대한 정보인 메타데이터를 정의할때 사용. 반드시 head태그 내부에 위치해야 한다.

~~~html
<head>
    <meta charset="UTF-8">
    <meta name="keyword" content="HTML, meta, tag, element, reference">
    <meta name="description" content="HTML meta tag page">
    <meta name="author" content="TCPSchool">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML meta tag</title>
</head>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|charset|문자셋|해당 문서의 문자 인코딩 방식을 명시함|
|context|텍스트|name 속성이나 ht1tp-equiv 속성과 관련된 값을 명시|
|http-equiv|content-type<br>default-style<br>refresh|content 속성에 명시된 값에 대한 HTTP 헤더를 제공함.|
|name|application-name<br>author<br>description<br>generator<br>keywords<br>viewport|메타데이터를 위한 이름을 명시

20.\<meter> 분수 값이나 게이지 내에서 특정 스칼라 값이 어느 정도인지 표현할때 사용

~~~html
<p>디스크 사용량 : <meter min="0" max="1000" value="350">350GB</meter></p>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|form|form id|meter가 포함될 하나 이상의 form을 명시|
|high|숫자|높은 값으로 간주되는 범위를 명시|
|low|숫자|낮은 값으로 간주되는 범위를 명시|
|max|숫자|게이지의 최대값 명시|
|min|숫자|게이지의 최소값 명시|
|optimum|숫자|게이지의 최적값을 명시|
|value|숫자|필수 속성으로 게이지의 현재 또는 측정된 값을 명시|

<br>

21.\<ol> 순서가 있는 HTML리스트를 정의

~~~html
<ol>
    <li>아메리카노</li>
    <li>카페라떼</li>
    <li>핫초코</li>
</ol>

<ol type="A" start="10">
    <li>녹차</li>
    <li>홍차</li>
    <li>자스민차</li>
</ol>
~~~

|속성명|속성값|설명|
|:---:|:---:|:---:|
|reversed|reversed|순서가 있는 리스트에서 리스트 마커의 숫자를 내림차순으로 명시|
|start|숫자|순서가 있는 리스트에서 리스트 마커의 시작값을 명시함|
|type|l<br>A<br>a<br>I<br>i|순서가 있는 리스트에서 리스트 아이템에 사용되는 마커의 종류를 명시|

<br>




[ 참고 페이지 http://tcpschool.com/ ]
 