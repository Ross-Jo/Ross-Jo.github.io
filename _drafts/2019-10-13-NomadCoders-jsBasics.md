---
title: "Nomad Coders : 바닐라 JS로 크롬 앱 만들기"
classes: wide
categories:
  - Dev
tags:
  - 클론코딩
  - 프로젝트
link: 
---

목표 : momentum 크롬 확장프로그램을 모방한 프로그램 만들기
전체코드 : <https://github.com/Ross-Jo/nomadCoders/tree/master/js-basics>

[핵심 기능]

- 시간 표시
- TODO 리스트 관리
- 배경화면 이미지 연동 및 변경

## [이번 강의를 들으면서 배울 수 있었던 점]

### HTML 페이지의 각 태그의 기능

- \<!DOCTYPE html\> : DOCTYPE은 어떤 역할을 하는 걸까?
웹 브라우저의 레이아웃 엔진으로 3가지 방식이 존재하는데, DOCTㅌYPE부분에 그 3가지의 방식 중 어떤 방식으로 랜더링을 할지 적어줌으로서, 브라우저는 랜더링 방식을 결정하게 된다.
3가지 레이아웃 엔진은 각각 호환모드(quirks mode), 거의 표준 모드(almost standards mode), 표준모드(standards mode) 등이다.
호환모드(quirks mode) : 기존 방식으로 제작된 웹사이트들을 표현하기 위해 네비게이터 4와 인터넷 익스플로러 5의 비표준 동작들을 에뮬레이션 함
표준모드(standards mode) : HTML과 CSS에 의해 웹 페이지가 표시된다.
거의표준모드(almost standars mode) : 소수의 호환모드 요소만 지원한다.
이때, 웹 페이지를 표준모드로 랜더링하고 싶은 경우 \<!DOCTYPE html\>과 같이 기술하며, 이는 HTML5에서 권장하는 가장 간단한 방식이다.

- \<html\> : 전체 html 문서를 감싸는 태그. 하나만 존재해야 하고 html 바깥에 DOCTYPE을 제외한 다른 태그가 있으면 안 됨
- \<head\> : html 문서에 대한 정보를 나타내는 부분. 하나만 존재해야하고 html 바로 아래에 있어야 함.
- \<body\> : html 문서에서 실제적으로 보여지는 부분을 나타냄. 하나만 존재해야 하고, html 바로 아래, head 다음에 위치해야 함.
- \<meta\> : 문서에 대한 설명을 표시함. 사람에게는 보이지 않고, 브라우저만 읽을 수 있음. 항상 \<head\> 요소 안에 위치해야 함.
  - charset : HTML 문서의 문자 인코딩 방식을 특정함.
  - name : 메타 데이터에 대한 이름을 특정한다.
  - content : http-equiv나 name 속성과 연관된 '값'을 제공.
  - http-equiv : content 속성의 정보 / 값에 대한 HTTP 헤더를 제공
  CF) \<meta http-equiv="X-UA-Compatible" content="ie=edge"\> : 익스플로러 브라우저 같은 경우 버전별로 보여지는 웹의 모습이 많이 다름. 그래서 X-UA-Compatible 속성을 활용해 IE의 어떤 모드로 렌더링 할지를 결정함. (웹페이지가 과거에 제작된 웹 페이지라) 굳이 과거 버전으로 랜더링 할 것이 아니라면, content값을 ie=edge로 하는 것을 권장함. 만약 X-UA-Compatible이 지정된 meta 태그가 없다면 사용자가 호환성 보기를 선택했는지, 개발자의 사이트가 MS 호환성 정보 관리 사이트 목록에 있는지(트래픽이 많은 사이트만 해당)등의 요인에 따라 달라짐.
- \<title\> : head 안에 들어가는 태그로 제목표시줄의 내용을 나타냄.
- \<link\> : 링크 태그는 웹문서와 외부 자원 / 외부 style sheets를 연결할 때 사용. head 섹션에만 삽입될 수 있으며, 몇번이고 사용할 수 있음.
- \<section\> : 구획.
- \<div\> vs \<span\> : Block vs Inline (Inline-block의 경우 따로 CSS를 설정해줘야함. ex. \<div style="display:inline-block;width:30%;height:50px;background:yellow"\>)
- \<form\> : 서버로 정보를 보내는 역할을 함. 안에 정보를 받을 수 있는 태그들이 보통 들어감. 가령 \<input\>, \<select\> 등
  - action 속성은 정보를 보낼 서버 주소를 적는 부분
  - method는 보내는 방식을 적는 부분
  - \<input\>
    - type: input에 어떤 형식의 정보를 받을지를 정하는 부분. ex) text, radio, checkbox 등
    - placeholder: input에 아무 값도 입력되지 않았을 때 나타나는 글.
    - name: input의 이름이면서 form을 전송했을 때 서버에서 받을 이름
    - value: input의 값
    - id: label(각 input이 어떤 역할을 하는지 알려주는 이름표 태그)과 연결할 값
- \<script\> : 클라이언트 사이드 스크립트를 정의하는데 사용. 보통 스크립트 문장을 포함하고 있거나, src 속성을 통해 외부 스크립트 파일을 가리키고 있음.

### 몇몇 JS 함수의 기능

- setInterval : 일정한 간격으로 작업을 수행하기 위해서 사용함. clearInterval 함수를 사용하여 중지 가능. 주의할 점은 일정한 시간 간격으로 실행되는 작업이 그 시간 간격보다 오래걸릴 경우 문제가 발생할 수 있음
- setTimeout : 일정한 시간 후에 작업을 한번 실행함. 보통 재귀적 호출을 사용해 작업을 반복. 기본적으로 setInterval과는 달리 지정된 시간을 기다린 후 작업을 수행하고, 다시 일정한 시간을 기다린 후 작업을 수행하는 방식. 지정된 시간 사이에 작업 시간이 추가되는 것. clearTimeout을 사용해서 작업을 중지
CF) clearInterval(), clearTimeout()이 실행중인 작업을 중지시키는 것은 아님. 지정된 작업은 모두 실행되고 다음 작업 스케쥴이 중지 되는 것임

### 기타

- document.querySelector()를 이용해서 html 요소를 선택할때, 클래스 이름을 타고 내려가서 특정 요소를 선택할 수 있음. 아래의 HTML 부분과 JS 부분을 보자

```html
  <div class="js-clock clock">
      <h3 class="clock__text">00:00</h3>
  </div>
```

```javascript
  const clock = document.querySelector(".js-clock .clock__text");
```

- localStorage 속성
읽기 전용 localStorage 속성은 사용자 로컬의 Storage 객체에 접근하게 해줌. localStorage는 sessionStorage와 비슷함. 유일한 차이점은 localStorage에 저장된 데이터는 만료 기간이 없지만, sessionStorage에 저장될 데이터는 세션이 끝나면 (브라우저가 종료되면) 지워진다는 것. localStorage 또는 sessionStorage에 저장될 데이터는 프로토콜 페이지에 명시되어 있음.
모든 key와 value는 항상 string으로 저장됨 (즉, object와 integer 들은 string으로 자동 변환됨)

- JS에서 동적으로 HTML 요소를 생성하여 삽입하는 방법

```javascript
function paintInput() {
  const input = document.createElement("input");
  input.placeholder = "Type your name here";
  input.type = "text";
  input.className = "name__input";

  const form = document.createElement("form");
  form.addEventListener("submit", handleSubmit);
  form.appendChild(input);
  nameContainer.appendChild(form); // const nameContainer = document.querySelector(".js-name");
}

function handleSubmit(event) {
  event.preventDefault(); // submit 이벤트에 의해 input칸에 입력된 text가 화면상에서 없어지는 default behavior가 일어나지 않게 됨
  const form = event.target;
  const input = form.querySelector("input");
  const value = input.value;
  localStorage.setItem(USER_LS, value);
  paintName(value);
}
```

- readableStream에서 데이터를 읽기 위한 방법

```javascript
  fetch(UNSPLASH_URL)
  .then(response => {return response.json()}) // readableStream에서 데이터를 읽기 위해서는 .json과 같은 conversion 메소드 중 하나를 써야 한다.
  // 참고 : https://stackoverflow.com/questions/40385133/retrieve-data-from-a-readablestream-object
```

- 다음날을 지정하는 한가지 방법

```javascript
  const expirationDate = new Date();
  expirationDate.setDate(expirationDate.getDate() + 1);
```

- 백그라운드 이미지를 지정하는 방법

```javascript
  body.style.backgroundImage = `linear-gradient(rgba(0, 0, 0, 0.4),rgba(0, 0, 0, 0.4)), url(${parsedImage.url})`; // 참고 : https://stackoverflow.com/questions/2504071/how-do-i-combine-a-background-image-and-css3-gradient-on-the-same-element

```

참고 페이지

- <https://developer.mozilla.org/ko/docs/Web/HTML/Quirks_Mode_and_Standards_Mode>
- <https://www.zerocho.com/category/HTML&DOM/post/58177daee4184c0015b8058a>
- <https://www.zerocho.com/category/HTML&DOM/post/5821b15f577d375e5c73bbc5>
- <https://www.zerocho.com/category/HTML&DOM/post/5823eff1cf58b000181d2ea3>
- <https://www.w3schools.com/tags/tag_meta.asp>
- <https://www.w3schools.com/tags/tag_link.asp>
- <https://www.w3schools.com/tags/tag_script.asp>
- <https://webdir.tistory.com/38>
- <https://offbyone.tistory.com/241>
- <https://developer.mozilla.org/ko/docs/Web/API/Window/localStorage>
