---
title: Responsive Web Publishing
---

### 1. 개요

본 문서는 Responsive web publishing project를 위한 개발 가이드로써 프로젝트를 진행함에있어 고려해야할 요건과 사용 기술에대해 서술하였다. 또한 앞서 진행된 프로젝트 사례를 통해 개발 과정 및 발생했던 이슈를 서술하였다.
### 2. 호환성

호환성이란 지원할 브라우저의 범위를 말한다. 웹 브라우저 종류 별, 버전 별 지원 기술 및 렌더링 방식이 조금씩 다르기 때문에 브라우저 호환 범위에 따라 공수 산정에도 지대한 영향을 미친다.
특히 microsoft사의 internet explorer의 버전에 따른 이슈가 가장 크다. 이슈는 Javascript, css, html5, font-end-framework 4가지로 나뉜다.
(오픈소스 플러그인 Modernizer.js를통해 html5 및css최신요소지원여부를검사할수도있다 http://modernizr.com/)
#### 2.1. Javascript

Javascipt라 썼지만 사실 jquery에대한 항목이다. 인터넷 익스플로러9를 기준으로 그 이전 버전(6,7,8)은 jQuery 1.x버전대를 사용하고, 9 이후 버전은 jQuery 2.x버전을사용한다.
참고) http://jquery.com/browser-support/
또한 멋진 웹을 개발 하기 위해 jQuery plugin을 사용 해야 하는 경우 해당 플러그인의 호환성을 확인 해야 한다.
해당플러그인의 호환성은 플러그인의 description을 참고하면 된다.
#### 2.2. Css

반응형 웹을 개발하는데 있어서 가장 큰 이슈가 되는 항목이다. css코드 및 미디어 쿼리 지원 여부 등을 꼼꼼히 살펴봐야 한다. 여기서 미디어쿼리(Media Query)는 필수적이다.
브라우저별 HTML5 및 css 지원현황 참고) http://fmbip.com/litmus
브라우저별 미디어쿼리 지원현황 참고) http://caniuse.com/css-mediaqueries
스크립트 플러그인을 사용하여 css의 호환성을 높이는 방법도 있다.
https://github.com/scottjehl/Respond
#### 2.3. html5

html5표준이 아직 정해지지 않은 만큼 최신 요소들에 관한 지원여부도 천차만별이다. 반응형 웹을 개발한다는 것은 모바일도 고려하여 개발하는 것이 때문에 html5에 추가된 기능을 사용 하기 전에 지원여부를 꼼꼼히 체크해 봐야 한다.
브라우저별 HTML5 및 css 지원현황 참고) http://fmbip.com/litmus
스크립트플러그인을 사용하여 html5의 호환성을 높이는 방법도 있다.
https://github.com/aFarkas/html5shiv
#### 2.4. Front-end Framework

개발 편의성을 위해 반응형을 지원하는 그리드 기반 Front-end Framework를 많이 찾아보게 된다. 이때 브라우저 지원여부를 꼼꼼히 살펴 봐야 한다. 참고)http://libsora.so/posts/ie7-responsive-web-framework/
그리고 프레임워크 학습에 많은 시간이 소요 될 수 있기에 이 또한 고려되어야 한다. 예를 들면 각종 기능이 포함되어있는 bootstrap같은경우 처음 접하는 개발자는 개발시간보다 학습시간이 더 소요 될 수 있다.
프레임워크가 반드시 사용 되어야 할 필요는 없다. 그저 좀더 편리한 개발을 위한 도구일 뿐 이라는 것을 명심 해야 한다. 자신이 진행할 프로젝트의 특성에 맞게 사용여부를 판단 할 수 있어야 한다.
### 3. 개발

#### 3.1. 조건부 로딩

익스플로러일 경우에만 사용할 수 있는 html 주석 조건문을 이용해 분기를 나누어 선택적으로 스크립트 및 css를 임포트 시킬 수 있다.
예)
```
<!--[if !IE]> -->                // 익스플로러가아닐때 선택됨
<script src="jquery-2.0.0.min.js"></script>
<!--<![endif]-->
<!--[if lt IE 9]>                 // 익스플로러 9미만 일 때 선택됨
<script src="jquery-1.9.1.min.js"></script>
<![endif]-->
<!--[if gte IE 9]>             //익스플로러 9 이상 일 때 선택됨
<script src="jquery-2.0.0.min.js"></script>
<![endif]-->
 
Javascipt를 통해 분기를 나눌 수 도 있다.
예)
if ((navigator.appVersion.indexOf("MSIE 7.") != -1)||(navigator.appVersion.indexOf("MSIE 8.") != -1)){
      // IE7과 IE8일 경우 동작
}else{
      // 그 외의 브라우저에서 동작
}
```
#### 3.2. 미디어 쿼리 구조화

반응형 웹에서 가장 핵심은 미디어 쿼리이다. 미디어 쿼리 작성 방식 및 분기에 따라 최적화가 얼마나 어디에 맞춰지는지를 결정하게 된다.
##### 3.2.1. 미디어 쿼리 구조

미디어 쿼리를 작성할 때 보통 처음 부분에 default로 적용될 css 내용을 작성하고 그 밑에 각종 분기를 작성하게 된다. 전자의 영역이 Mobile First, Desktop First 방식이 결정되는 영역이다.
##### 3.2.2.      Mobile First

모바일에 먼저 맞춰 개발 후 더 큰 사이즈에 맞춰 개발
구버전 브라우저에 대한 지원을 배제한 프로젝트라면 최신기술이 대부분 문제없이 동작 할 것이기 때문에 모바일에 대한 중요성이 강조 될 것이다. 이때는 모바일 우선 개발 방식이 더 용이하다.
##### 3.2.3.      Desktop First

일반적인 pc화면에 맞춰 개발 후 점차 작은 사이즈에 맞춰 개발
해당 프로젝트가 구 버전 브라우저와의 호환성을 중시하고 모바일 지원은 보다 비중이 낮을 경우 Desktop First 방식으로 작성하는 것이 개발에 용이하다. 또한 구버전 브라우저는 기본적으로 반응형이 동작하지 않는다는 전제를 깔아야 한다.
##### 3.2.4.      미디어쿼리 분기

미디어쿼리 분기는 device width에 따라 선택적으로 css가 적용되게 할 수 있다. 이는 다양한 사이즈의 디바이스 크기에 최적화 시킬 수 있는 핵심적인 부분이다.
디바이스 스크린 정보 참고) http://screensiz.es/tablet
위의 참고를 바탕으로 모바일과타블렛pc의 디바이스 width를 정리해 보았다.
**[ 모바일 ]**
iPhone (Original - 3GS), Nokia Lumia 900, Nokia Lumia 720, HTC Desire HD, HTC Evo,
Alcatel One Touch Ultra 995, Galaxy SII, Galaxy S Plus, Galaxy S, Nokia Lumia 520,
Samsung Nexus S, Nokia Lumia 620, Nokia Lumia (710, 800), HTC Desire, Droid,
iPhone 4 (4, 4S), iPhone 5 (5c, 5s)
**[Device-Width 320px]**
Droid Razr, Droid Razr Maxx, Galaxy Note II, Droid 3 & 4, Galaxy SIII, Galaxy Nexus,
Alcatel One Touch Idol Ultra, BlackBerry Q10, Galaxy SIV, Sony Xperia Z, HTC One
**[Device-Width 360px]**
Google Nexus 4 by LG, Nokia Lumia 925, Nokia Lumia 1020, Nokia Lumia 920, BlackBerry Z10
**[Device-Width 384px]**
Galaxy Note
**[Device-Width 400px]**
**[TabletPC]**
Samsung Galaxy Tab 10.1, Amazon Kindle Fire HD 7"
**[Device-Width 533px]**
Nook Tablet, Amazon Kindle Fire 1st Gen, Samsung Galaxy Tab 2, Samsung Galaxy Tab,
Asus Nexus 7 (2013)
**[Device-Width 600px]**
Asus Nexus 7 (2012)
**[Device-Width 603px]**
Apple iPad 1 & 2, Apple iPad Mini, Acer Picasso, Apple IPad 3 & 4
**[Device-Width 768px]**
Samsung Galaxy Tab 2 10.1, Galaxy Note 10.1, Asus Transformer, Amazon Kindle Fire HD 8.9",
Google Nexus 10 by Samsung
**[Device-Width 800px]**
Motorola Xoom, Toshiba AT100
**[Device-Width 853px]**
BlackBerry PlayBook, Ainol Novo 7 Crystal, Ainol Novo 9 Spark
**[Device-Width 1024px]**
Ainol Novo 10 Hero, Microsoft Surface Pro
**[Device-Width 1280px]**
Microsoft Surface
**[Device-Width 1366px]**
 
다음은 위의 내용을 실제 프로젝트에서 사용하기 위해 미디어쿼리 분기로 나타낸 것이다. (desktop first)
/* desktop pc and tablet pc width All common value, 768px <= width */
 @media only screen and (min-width: 768px){/*insert your css*/}
 /* tablet pc big width All common value, 1025px <= width <= 1180px */
 @media only screen and (min-width: 1025px) and (max-width: 1180px) {/*insert your css*/}
 /* tablet pc middle width All common value, 768px <= width <= 1024px, nav X */
 @media only screen and (min-width: 768px) and (max-width: 1024px) {/*insert your css*/}
  /* tablet pc, 854px <= width <= 1024px */
 @media only screen and (min-width: 854px) and (max-width: 1024px) {/*insert your css*/}
 /* tablet pc, 801px <= width <= 853px, nav X */
 @media only screen and (min-width: 801px) and (max-width: 853px) {/*insert your css*/}
 /* tablet pc, 768px <= width <= 800px, nav X */
 @media only screen and (min-width: 768px) and (max-width: 800px) {/*insert your css*/}
 /* mobile All common value, width <= 767px */
 @media only screen and (max-width: 767px) {/*insert your css*/}
 /* mobile, 604px <= width <= 767px */
 @media only screen and (min-width: 604px) and (max-width: 767px) {/*insert your css*/}
 /* mobile, 534px <= width <= 603px */
 @media only screen and (min-width: 534px) and (max-width: 603px) {/*insert your css*/}
 /* mobile, 401px <= width <= 533px */
 @media only screen and (min-width: 401px) and (max-width: 533px) {/*insert your css*/}
 /* mobile, 385px <= width <= 400px */
 @media only screen and (min-width: 385px) and (max-width: 400px) {/*insert your css*/}
 /* mobile, 361px <= width <= 384px */
 @media only screen and (min-width: 361px) and (max-width: 384px) {/*insert your css*/}
 /* mobile, 321px <= width <= 360px */
 @media only screen and (min-width: 321px) and (max-width: 360px) {/*insert your css*/}
 /* mobile, width <= 320px */
 @media only screen and (max-width: 320px) {/*insert your css*/}
이와 같이 모든 디바이스에 일일이 최적화 하기란 보통 일이 아니다. 실제 프로젝트에서는 분기를 적절히 통합하여 개발 하는 것이 대부분이다. (desktop pc, tablet pc, mobile정도로 통합)
 
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no, target-densitydpi=medium-dpi" />
user-scalable=no : 사용자의 확대보기 허용 여부(no/yes)
initial-scale=1.0  : 페이지 로딩 시 확대비율
maximum-scale=1.0  : 최대 확대 비율
minimum-scale=1.0  : 최소 축소 비율
width=device-width : 플랫폼 가로 크기에 맞춤, 수치를 넣으면 그 수치에 맞게 맞춰짐.
target-densitydpi=medium-dpi : dpi([dots per inch])를 맞춤. 일반적으로 medium-dpi로 하는게 좋음. (device-dpi/low-dpi/medium-dpi/high-dpi)
