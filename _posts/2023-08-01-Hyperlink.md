---
title:  "Hyperlink"
categories: Html
published : true
typora-root-url: ../
author_profile: true
sidebar_main: true
---
HyperText의 Hyper는 컴퓨터 용어로서 텍스트 등의 정보가 동일 선상에 있는 것이 아니라 다중으로 연결되어 있는 상태를 의미한다.

이것은 HTML의 가장 중요한 특징인 link의 개념과 연결되는데 기존 문서나 텍스트의 선형성, 고정성의 제약에서 벗어나 사용자가 원하는 순서대로 원하는 정보를 취득할 수 있는 기능을 제공한다. 한 텍스트에서 다른 텍스트로 건너뛰어 읽을 수 있는 이 기능을 하이퍼링크(hyper link)라 한다.

## a tag
링크를 만들 때 사용하는 태그

<script src="https://gist.github.com/qwp0/60b181f436f278ff712da9d7edc9f747.js"></script>

<img src="/images/2023-08-01-Hyperlink/atag.png" alt="a태그" style="zoom:50%;" />

<img src="/images/2023-08-01-Hyperlink/a.png" alt="a태그 이미지" style="zoom: 25%;" />

- 속성

    - href
        - 이동하고자 하는 파일의 경로를 값으로 받는다. 경로(path)란 파일 시스템 상에서 특정 파일의 위치를 의미한다.
        - 사용 가능한 값
        
        ![href](/images/2023-08-01-Hyperlink/href.png)
        
    - target
        - 링크를 클릭했을 때 창을 어떻게 오픈할 지를 지정한다.
            - _blank
                - 연결된 문서가 새 탭에서 열린다.
            - _self   
                - 연결된 문서가 현재 탭에서 열린다.