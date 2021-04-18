### PostCSS

> PostCSS는 CSS 전처리기

**PostCSS 장점**

- postcss는 sass,less와 같은 프레임 워크가 아니기 때문에 유연하다.
- 다양한 플러그인을 조합해서 사용 할 수 있다.
- 모듈화가 가능하다.
- Autoprefixer 기능이 있다.

#### 일반적인 CSS

> react에서 같은 클래스명의 css를 사용하게 되면 import하지 않아도 덮어 씌어지는 문제가 있다. 그를 해결 하기 위해 보편적으로 BEM 등의 네이밍 방법을 사용했다.

```jsx
function Button1() {
  return (
    <div className="button">
      <span className="text">Button1</span>
    </div>
  );
}
```

```css
/* button1.css */
.button {
  background-color: aquamarine;
}

.text {
  color: blue;
}
```

```jsx
function Button2() {
  return (
    <div className="button">
      <span className="text">Button2</span>
    </div>
  );
}
```

```css
/* button2.css */
.button {
  background-color: sandybrown;
}

.text {
  color: black;
}
```

**결론적으로는 css의 기본 성격에 따라 뒤에 선언된 button2.css의 스타일이 적용 된다.**

#### PostCSS 모듈화

> PostCSS의 모듈화를 적용하면 클래스명이 자동으로 변환 된다.

```jsx
import styles from './button1.module.css';

function Button1() {
  return (
    <div className={styles.button}>
      <span className={styles.text}>Button1</span>
    </div>
  );
}
```

이런식으로 css 파일이름에 module을 넣어주고 import를 해서 사용한다.
이렇게 하면 컴포넌트 이름과 클래스명, 해쉬값이 붙어있는 클래스 명으로 변환되어진다.

```html
<div class="button1_button__2mglj">
  <span class="button1_text__1cbSb">Button1</span>
</div>
```

**주의 할 점**
위의 코드들에서 보여졌지만 컴포넌트에서 불러올때는

`import styles from './button1.module.css';`

처럼 모듈화 된 css를 불러오고

기존의 클래스명이 아닌

`{styles.클래스명}`

으로 작성 해 주어야 한다.
