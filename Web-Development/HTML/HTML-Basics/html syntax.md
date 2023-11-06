# HTML Syntax

### 목차

- [1 HTML 요소]()

***

<br>

## 1 HTML 요소

HTML 요소(Element)란 HTML 문서(Document)를 이루는 구성 단위를 의미한다. 즉, 요소들이 모여 하나의 HTML 문서를 이루는 것이다. 

- 요소는 시작/종료 태그, 그리고 그 태그 사이에 위치한 콘텐츠(Content)로 구성된다
- 태그는 브라켓(Brackets, `<`, `>`) 사이에 위치한다. 태그를 브라켓으로 감싸 브라우저에 '태그'로서 인식시켜 주기 위함이다
- 태그는 일반적으로 소문자로 작성한다3

<img style="max-width: 100%" src="https://user-images.githubusercontent.com/92138751/230755958-3a92dee0-7b55-43f1-b73d-4f79d1e5e6ca.png">

아래 그림은 `<h1>` 요소의  `<h1>` 태그와 콘텐츠가 HTML 문서에 표시된 상태다:

<img style="max-width: 100%" src="https://user-images.githubusercontent.com/92138751/230807083-67b1a42c-13ce-45ac-92db-59f68e21699c.png">

### 1-1 요소의 중첩

요소는 요소 안에 요소를 위치시켜 중첩할 수 있다. 중첩된 요소는 '부모-자식' 형태의 부자 관계가 성립된다. 일반적으로 요소의 중첩을 통해 HTML의 정보를 구조화한다. 아래 그림의 예시에서는 `<body>` 요소는 자식 요소인  `<h1>`, `<p>` 요소의 부모 요소다

<img style="max-width: 100%" src="https://user-images.githubusercontent.com/92138751/230806937-e365e43a-3e12-4efe-9ebb-8e41dc8db559.png">

그렇다면 아래 그림의 요소들은 관계가 어떻게 성립될까? `<body>` 요소는 자식 요소인  `<h1>`의 부모 요소이며, `<h1>` 요소는 자식 요소인 `<a>` 요소의 부모 요소다. 즉, `<body>` 요소의 자손 요소는  `<a>` 요소다

<img style="max-width: 100%" src="https://user-images.githubusercontent.com/92138751/230806826-20a5313e-268a-42f5-a4f1-1801e959ee71.png">

### 1-2 빈 요소

시작 태그와 종료 태그 없이 시작이자 종결하는 단일 태그는 콘텐츠를 가질 수 없으며 이러한 요소를 빈 요소(Empty Element / Self-closing Element)라고 부른다. 아래는 요소가 빈 요소에 해당한다. 빈 요소는 [속성](#2-요소의-속성)만을 가질 수 있다

```html
<br>
<hr>
<img>
<input>
<link>
<meta>
```

<br>

## 2 요소의 속성

속성(어트리뷰트, Attribute)은 요소의 성질, 즉 요소가 지닌(지닐) 특징을 정의한다. 어트리뷰트는 일반 요소와 빈 요소의 특징을 모두 명세할 수 있으며 요소의 시작 태그(빈 요소는 단일 태그) 바로 뒤에 어트리뷰트 이름(Attribute Name)과 어트리뷰트 값(Attribute Value)이 위치한다 

어트리뷰트 이름은 요소의 특징 종류를, 어트리뷰트 값은 해당 특징의 상세 내용을 정의하는 것이다. 어트리뷰트 값은 항상 쌍따옴표(`""`)로 문자열 값을 감싸줘야 정상적으로 적용이 된다 

<img style="max-width: 100%" src="https://user-images.githubusercontent.com/92138751/230855133-45a54a2c-b8b9-4933-ac8e-7901ab3a61f9.png">

위 이미지는 아래 HTML 코드와 같이 `<img>` 요소를 활용해서 본문에 첨부했다. 어트리뷰트 `src`, `style`은 각각 이미지 파일 경로와 파일명, 최대 이미지 너비를 유지하는 상세 명세로 기술되어 있다

```html
<img src="https://images.githubusercontent.com/7901ab3a61f9.png" style="max-width: 100%">
```



 

 









