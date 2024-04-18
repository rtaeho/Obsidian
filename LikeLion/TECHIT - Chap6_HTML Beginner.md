### HTML 구성
- HTML : HyperTextMarkupLanguage
	- 태그를 통해 Markup
		ex) `<p>멋쟁이사자처럼<p>`
	- `<! -- --!>` 를 통해서 주석 표현
	- 크게 `<head>`와`<body>`로 나뉘는 Boilerplate의 문서구조를 가짐

### HTML `<head>`
- `<meta charset="UTF-8">`
	전세계 언어를 지원하는 인코딩 방식
- `<meta http-equiv="X-UA-Compatible" content="IE=edge">` 
	브라우저 호환성 설정
- `<meta name="viewport" content="width=devie-width, initial-scale=1.0>` 
	브라우저의 너비를 각 장비에 맞게 설정
- `<title>`
	표지에 적혀있는 제목

### 블록 요소와 인라인 요소
- 블록 요소 : 브라우저에서 하나의 면을 차지하는 요소
	ex) `<h1>, <h2>, <h3>, <p>, <ol>, <ul>, <li>, <div>`
- 인라인 요소 : 브라우저에서 선을 그리는 요소
	ex) `<a>, <span>`
- 블록 요소는 블록 요소와 인라인 요소를 포함 가능하지만 인라인 요소는 블록 요소를 포함 불가능

### HTML `<body>`
- heading : `<h1> <h2> ...` 제목용 태그
	*글씨를 키우려는 목적으로 사용 X*
- paragraph : `<p>` 하나의 문단을 구성하는 태그
- line break : `<br>` 줄 바꿈 태그
- list : `<ol> <li> <ul>`
	- `<ol>` : 순서가 있는 리스트 태그
	- `<li>` : 리스트 내부 요소 태그
	- `<ul>` : 순서가 없는 리스트 태그
- anchor : `<a>` 다른 페이지로 링크를 걸 수 있는 태그
- `<span>`문자열의 일부 영역을 선택하기 위한 태그
- `<div>`요소들을 묶어서 그룹화 하기 위한 태그