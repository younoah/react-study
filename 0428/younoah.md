### SDK란?

software debelopment kit

프레임워크와 비슷하게 개발에 필요한 모든 것들이 들어있다.



API (Application Programming Interface)
\- 개발자가 공개적으로 노출한 멤버들을 사용하여 기능에 접근하고, 해당 기능을 구현하는데 사용된 코드를 숨길 수있는 인터페이스

SDK(software Development Kit)
\- 소프트웨어 개발 도구 모음
\- SDK 안에는 개발에 도움이 될 개발 도구 프로그램, 디버깅 프로그램, 문서, API 등이 있다.

Software Framework
\- 정의된 API를 제공하는 Software library의 모음
\- 라이브러리와 달리 애플리케이션의 틀과 구조를 결정할 뿐만 아니라, 그 위에 개발된 개발자의 코드를 제어함.

Software Library
\- 컴퓨터 프로그램에서 자주 사용되는 부분 프로그램들을 모아 놓은 것.
\- 정적, 동적(링크, 로드) 라이브러리로 나뉨



출처: https://you9010.tistory.com/147 [본질을 알고픈 개발자]



## PostCSS



### CSS에서 변수

- 정의

```css
:root { 
    --red : #DC3545; 
    --blue : #007bff; 
}
```



- 사용

```css
body { 
    background-color: var(--red); 
}



### PostCSS에서 변수

- 정의

```css
@value key: value;


@value blackColor: black;
```



- 사용

```css
/* PostCSS에서 변수를 사용할 때는 확장자명을 module.css로 변경해야한다. */
@value blackColor from '경로';

body {
    background-color: blackColor;
}
```

