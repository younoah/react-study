## PostCSS

### 1. PostCSS란?

POST CSS는 우리의 CSS를 조금더 현대적으로 바꿔주는 **플러그인**이다.

좀더 풀어 설명하자면 POST CSS 는 JS 플러그인을 사용하여 CSS를 변환시키는 툴 입니다.

POST CSS 는 언어가아니라 자동으로 신기술 CSS 를 호환가능하도록 변환시켜주는 플러그인일 뿐이다.

1. CSS에 문제가없는지 미리 확인해서 에러로그를 준다.

2. 지금 발전중인 CSS의 현대기술들을 브라우저에 호환되도록 자동 변환해준다.

PostCSS 자체는 아무 일도 하지 않는다. 다만 다양한 플러그인과, 플러그인을 추가할 수 있는 환경을 제공할 뿐이다. 

 [https://postcss.org](https://postcss.org/)

> create react-app 을 이용하면 postcss가 임포트되어있다.



### 2. PostCSS의 모듈화 활용

##### 기존방식

BEM을 이용해서 클래스를 분리하여 작성해야 서로 다른 타겟으로 css를 적용할 수 있었다.

- button1.jsx & button2.jsx

```jsx
// -----------in button1.jsx-----------
import React, { Component } from 'react';
import './button1.css';

class Button1 extends Component {
  render() {
    return (
      <div className="button1__button">
        <span className="button1_text">Button1</span>
      </div>
    );
  }
}

export default Button1;


// -----------in button2.jsx-----------
import React, { Component } from 'react';
import './button2.css';

class Button2 extends Component {
  render() {
    return (
      <div className="button2__button">
        <span className="button2__text">Button1</span>
      </div>
    );
  }
}

export default Button2;

```



- button1.css & button2.css

```css
/* in button1.css */
.button1__button {
  background-color: aquamarine;
}

.button1_text {
  color: blue;
}

/* in button2.css */
.button2__button {
  background-color: sandybrown;
}

.button2__text {
  color: black;
}
```



- 결과

![postcss2](/Users/uno/Desktop/postcss2.png)



- 분리(BEM)해서 작성안 할 경우 마지막으로 읽힌 css로 덮어씌워진다.

![postcss1](/Users/uno/Desktop/postcss1.png)



##### PostCSS활용

PostCSS 이용한다면 동일한 클래스명을 사용하더라고 간편하게 모듈화가 되어 서로다른 css가 적용이 된다.

>`.module.css` 확장자명
>
>`styles` 로 import
>
>`className={styles.선택자}`



- button1.jsx & button2.jsx

```jsx
// -----------in button1.jsx-----------
import React, { Component } from 'react';
import styles from './button1.module.css';

class Button1 extends Component {
  render() {
    return (
      <div className={styles.button}>
        <span className={styles.text}>Button1</span>
      </div>
    );
  }
}

export default Button1;


// -----------in button2.jsx-----------
import React, { Component } from 'react';
import styles from './button2.module.css';

class Button2 extends Component {
  render() {
    return (
      <div className={styles.button}>
        <span className={styles.text}>Button1</span>
      </div>
    );
  }
}

export default Button2;

```



- **button1.module.css** & **button2.module.css**

```css
/* in button1.module.css */
.button {
  background-color: aquamarine;
}

.text {
  color: blue;
}

/* in button2.module.css */
.button {
  background-color: sandybrown;
}

.text {
  color: black;
}
```

![postcss3](/Users/uno/Desktop/postcss3.png)

렌더링된 후 각각의 클래스명을 확인해보면 

`모듈명_선택자_ID` 형태로 세팅이 되어있다.

