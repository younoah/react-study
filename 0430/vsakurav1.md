### Styling

```css
/* 스스로 적용 해 본 코드 */
.maker {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 100%;
  height: 100vh;
}

.content {
  display: flex;
  height: 100%;
  background-color: makerWhite;
}

/* 엘리님 코드 */
.maker {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
}

.container {
  flex: 1;
}
```

나는 이런 식으로 작성을 했는데 강의에서 훨씬 쉽게 적용하는 것을 보고 css도 아직 많이 부족하다고 느꼈다.. 😅

❔ 의문점

웹페이지에서 h1 태그는 한 번만 사용하는 것이라고 알고 있는데 각 컴포넌트마다 h1을 사용해도 되는 건가 하는 생각이 들었다

```jsx
function Preview() {
  return (
    <section className={styles.preview}>
      <h1 className={styles.title}>Card Preview</h1>
    </section>
  );
}
```

💥 강의 중에 종종 h1이 사용되는 경우가 있는데 이런 부분은 웹 표준에서 알맞은 것일까? 생각해 볼 필요가 있겠다!
