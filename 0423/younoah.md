## 상태에 따른 스타일 분기

props를 활용하여 상태에 따라 스타일을 분기처리하여 다룰수 있다.



- app.jsx

```jsx
function App({ youtube }) {
  const [videos, setVideos] = useState([]);
  const [selectedVideo, setSelectedVideo] = useState(null);

  const selectVideo = video => {
    setSelectedVideo(video);
  };

  const search = query => {
    setSelectedVideo(null);
    youtube
      .search(query) //
      .then(videos => setVideos(videos));
  };

  useEffect(() => {
    youtube
      .mostPopular() //
      .then(videos => setVideos(videos));
  }, []);
  return (
    <div className={styles.app}>
      <SearchHeader onSearch={search} />
      <section className={styles.content}>
        {selectedVideo && ( // (*) 렌더링 여부
          <div className={styles.detail}>
            <VideoDetail video={selectedVideo} />
          </div>
        )}
        <div className={styles.list}>
          <VideoList
            videos={videos}
            onVideoClick={selectVideo}
            display={selectedVideo ? 'list' : 'grid'} // (*) 어떻게 스타일 할지 분기
          />
        </div>
      </section>
    </div>
  );
}
```



- video_list.jsx

```jsx
const VideoList = ({ videos, onVideoClick, display }) => (
  <ul className={styles.videos}>
    {videos.map(video => (
      <VideoItem
        key={video.id}
        video={video}
        onVideoClick={onVideoClick}
        display={display} // (*) dispaly 전달
      />
    ))}
  </ul>
);
```



- video_item.jsx

```jsx
const VideoItem = memo(
  ({ video, video: { snippet }, onVideoClick, display }) => {
    // (*) 스타일 지정자 분기처리
    const displayType = display === 'list' ? styles.list : styles.grid;
    return (
      <li
        className={`${styles.container} ${displayType}`} // (*) 스타일 지정
        onClick={() => onVideoClick(video)}
      >
        <div className={styles.video}>
          <img
            className={styles.thumbnail}
            src={snippet.thumbnails.medium.url}
            alt="video thumbnail"
          />
          <div className={styles.metadata}>
            <p className={styles.title}>{snippet.title}</p>
            <p className={styles.channel}>{snippet.channelTitle}</p>
          </div>
        </div>
      </li>
    );
  }
);
```

