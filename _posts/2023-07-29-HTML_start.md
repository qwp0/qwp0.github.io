---
title:  "HTML start"
categories: Html
published : true
typora-root-url: ../
author_profile: true
sidebar_main: true
---
## HTML이란?
- **HTML(HyperText Markup Language)** : 웹페이지를 기술하기 위한 **마크업 언어**
  - HyperText : 문서를 서로 연결해주는 링크
  - Markup : 표시하다  

즉, 웹 브라우저에 내용을 보여주는 텍스트, 이미지, 영상 등의 위치를 표시하는 것을 의미한다.

## HTML 기본 문법
![HTML tag(/images/2023-07-29-HTML_start/HTML tag.png)

- ### 요소(Element)

    시작 태그와 종료 태그 , 태그 사이에 위치한 content로 구성된다.
    - 요소의 중첩(Nested Element)
        - 요소는 다른 요소를 포함할 수 있다. 이것을 중첩관계(부자관계)라고 한다. 이 관계를 이용하여 웹페이지의 구조를 표현한다.
    - 빈 요소(Empty Element)
        - content를 가질 수 없는 요소를 빈 요소라고 한다. 빈 요소는 content가 필요 없으며 attribute만을 가질 수 있다. 속성(attribute)은 아래에서 설명한다.


- ### 속성(Attribute)
    요소의 성질, 특징을 정의하는 명세이다. 모든 요소는 속성을 가지며 속성은 요소의 추가적인 정보를 제공한다. 속성은 시작 태그에 위치하며 이름과 값의 쌍으로 표현한다.
    - Global Attribut
        - 모든 HTML 요소가 공통으로 사용할 수 있는 속성이다. 몇몇 요소에는 효과가 적용되지 않을 수 있지만 대체로 모든 요소에 사용 가능하다.
    
    ![global_attributes](/images/2023-07-29-HTML_start/global_attributes.png)

## HTML 구조
### <!DOCTYPE html>
- 웹 문서의 유형을 지정하는 선언문
- 현재 문서가 **HTML5 언어로 작성한 웹 문서**라는 의미
- HTML5는 2014년 10월 28일 확정된 차세대 웹 표준이다. 

### <html> 태그
- **웹 문서의 시작과 끝**을 나타내는 태그.
- 웹 브라우저는 \<html> 태그를 만나면 \</html>까지 소스를 읽어 화면에 표시한다.

### <head> 태그
- **메타데이터**를 표함하기 위한 태그 
- \<head> 영역의 내용은 대부분 웹 브라우저 화면에는 보이지 않는다.
- **\<meta>태그**
    - 웹 문서와 관련된 정보를 저장할 때 사용
    - **\<meta charset = "UTF-8">**
        - 화면에 글자를 표시할 때 한글 인코딩을 사용하겠다는 의미

    - \<meta> 태그는 나중에 더 자세히 다룰 예정
- **\<title>태그**
    - **문서 제목**을 나타내는 태그
    - 웹 브라우저의 제목 표시줄에 표시됨

### <body> 태그
- **웹 브라우저에 내용을 표시**하는 태그

