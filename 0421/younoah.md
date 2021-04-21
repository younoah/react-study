## useEffect

컴포넌트가 업데이트 될 때(state, props) 콜백을 호출하고 싶을 때 사용한다.

```jsx
// 빈 값일 때, 컴포넌트가 렌더링될 때마다 호출된다.
useEffect(()=>{
    ...
})

// 텅 빈 배열을 전달하면 첫 번째 렌더링 후에만 호출됩니다.
useEffect(()=>{
    ...
},[])

// state가 업데이트 될 때 마다 호출된다.
useEffect(()=>{
    ...
},[state])
```



저기 저 배열을 의존성 배열(deps 배열)이라고 한다.



## 객체 분해



```js
// const { 객체 프로퍼티: 목표 변수 } = 객체

const VideoItme = (props) = {
    ...
    {props.video.snippet.~}
    ...
}

const VideoItme = ({video: videoItem}) = {
    ...
    {videoItem.snippet.~}
    ...
}

const VideoItme = ({video: {snippet}}) = {
    ...
    {snippet.~}
    ...
}
```

