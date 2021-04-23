오늘 강의는 능숙함의 차이 이지, 기억을 요하는 문제는 없었던 것 같다.
다만 한 가지가 있다면

```jsx
const search = query => {
  // setVideos(null)
  // 등의 처리를 해주면 더 좋았을 것 같다
  setSelectedVideo(null);

  youtube
    .search(query) //
    .then(videos => setVideos(videos));
};
```

selectedVideo만 수정하면
검색할 때 기존의 list가 잠깐 보이는 현상이 있어서 그런 부분을 생각하면 좋을 것 같다
