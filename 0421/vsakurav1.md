### useEffect Hook

> componentDidMount, componentDidUpdate 의 기능을 묶어놓은 훅이다.

이 때 주의해야 하는것은 depth의 활용인데, depth를 설정하지 않으면 컴포넌트가 리랜더링 될때마다 호출되게 된다

**기본적으로는 부모 컴포넌트의 props나 state가 변경되어도 자식 컴포넌트가 리랜더링 되기 때문에 map을 돌려서 list를 불러오는 경우, 과도한 호출이 발생할 수 있다.**

```jsx
// useEffect로 API 호출
useEffect(() => {
  const requestOptions = {
    method: 'GET',
    redirect: 'follow',
  };

  fetch(
    'https://youtube.googleapis.com/youtube/v3/...,
    requestOptions,
  )
    .then(response => response.json())
    .then(result => setVideos(result.items))
    .catch(error => console.log('error', error));
}, []);
```

개인적으로는 useEffect 안에서 함수를 바로 생성하는것 보다는, 따로 함수를 생성하고, useEffect에서 호출하는 편이 좋을 것 같다

### destructuring assignment

- 구조 분해 할당 예시 1

```jsx
function VideoItem({
  video: {
    snippet: { title, thumbnails, channelTitle },
  },
}) {
  return (
    <li className={styles.item}>
      <img src={thumbnails.medium.url} alt="thumbnail" />
      <div className={styles.metadata}>
        <p>{title}</p>
        <p>{channelTitle}</p>
      </div>
    </li>
  );
}
```

props를 구조분해 해서 바로 사용 할 수 있다.

- 구조 분해 할당 예시 2

```jsx
function VideoItem({ video }) {
  const { title, thumbnails, channelTitle } = video.snippet;

  return (
    <li className={styles.item}>
      <img src={thumbnails.medium.url} alt="thumbnail" />
      <div className={styles.metadata}>
        <p>{title}</p>
        <p>{channelTitle}</p>
      </div>
    </li>
  );
}
```

---

> box-shadow Tip!

[cssMatic](https://www.cssmatic.com/box-shadow) 에서 custom할 수 있다.
이 때, PostCSS로 module화를 하면 AutoPrefix 기능을 사용할 수 있기 때문에 box-shadow 코드만 복사하면 된다.
