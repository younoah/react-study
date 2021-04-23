## 8.9 ~ 8.10 비디오 선택 & 상세 화면 

🦆계획 

1. 원하는 비디오를 선택하면, 그 비디오를 기억했다가(state), 선택된 비디오가 있으면 관련된 정보를 보여주면 됨
2. detail컴포넌트 만들어서 사용 

✅ 연산자 생각 잘하면서 쓰기

✅ Props로 전달해서 CSS 꾸미기 

✅ white-space

구현

### Props로 전달해서 선택된 비디오 정보 나오게 하기

1. 먼저 app.jsx에서 setVideos라는 state만들어 주기 

    ```jsx
    const [selectedVideo, setSelectedVideo] = useState(null);
    ```

2. video_detail폴더와 파일 만든 후 간단하게 먼저 작성

    ```jsx
    import React from "react";
    import styles from "./video_detail.module.css";

    const VideoDetail = ({ video }) => <h1>{video.snippent.title}</h1>;

    export default VideoDetail;
    ```

3. app.jsx에서 rendering 하는 부분에서 '만약 selectedVideo가 있다면~'이렇게 넣어야 하니까 로직 이용 

    ```jsx
    import React, { useEffect, useState } from "react";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";
    import VideoDetail from "./components/video_detail/video_detail";

    function App({ youtube }) {
      const [videos, setVideos] = useState([]);
      const [selectedVideo, setSelectedVideo] = useState(null);

      const search = (query) => {
        youtube
          .search(query) //
          .then((videos) => setVideos(videos));
      };

      useEffect(() => {
        youtube
          .mostPopular() //
          .then((videos) => setVideos(videos));
      }, []);

      return (
        <div className={styles.app}>
          <SearchHeader onSearch={search} />
          {selectedVideo && <VideoDetail video={selectedVideo}/>}
          <VideoList videos={videos} />;
        </div>
      );
    }

    export default App;
    ```

4. 이제 selectVideo라는 함수 만들기 

    ```jsx
    const selectVideo = (video) => {
        setSelectedVideo(video);
      };
    ```

5. VideoList라는 함수에 위에서 만든 selectVideo 전달 
6. 이제 videoList.jsx에 가서 보면 이렇게 되어 있음

    ```jsx
    import React from "react";
    import VideoItem from "../video_item/video_item";
    import styles from "./video_list.module.css";

    const VideoList = (props) => (
      <ul className={styles.videos}>
        {props.videos.map((video) => (
          <VideoItem key={video.id} video={video} />
        ))}
      </ul>
    );

    export default VideoList;
    ```

    props에서 받는 것이 videos 뿐만 아니라 onVideoClick이라는 함수도 있으니 deconstructing

    ```jsx
    const VideoList = ({ videos, onVideoClick }) => (
      <ul className={styles.videos}>
        {videos.map((video) => (
          <VideoItem key={video.id} video={video} />
        ))}
      </ul>
    );
    ```

7. 이제 선택된 video를 아이템으로 보내야 하니 또 전달 

    ```jsx
    import React from "react";
    import VideoItem from "../video_item/video_item";
    import styles from "./video_list.module.css";

    const VideoList = ({ videos, onVideoClick }) => (
      <ul className={styles.videos}>
        {videos.map((video) => (
          <VideoItem key={video.id} video={video} onVideoClick={onVideoClick} />
        ))}
      </ul>
    );

    export default VideoList;
    ```

8. videoItem.jsx

    ```jsx
    import React from "react";
    import styles from "./video_item.module.css";

    const VideoItem = ({ video, video: { snippet }, onVideoClick }) => (
      <li className={styles.container} onClick={() => onVideoClick(video)}>
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

    export default VideoItem;
    ```

## 디자인을 위해 app.jsx에서 rendering 부분 div , section tag로 감싸기

```jsx
return (
    <div className={styles.app}>
      <SearchHeader onSearch={search} />
      <section className={styles.content}>
        <div className={styles.detail}>
          {selectedVideo && <VideoDetail video={selectedVideo} />}
        </div>
        <div className={styles.list}>
          <VideoList videos={videos} onVideoClick={selectVideo} />
        </div>
      </section>
    </div>
  );
```

1. app.module.css 

```jsx
.app {
  max-width: 80rem;
}

.content {
  display: flex;
}

.detail {
  flex: 1 1 70%; **/*grow 와 shrink*/**
}

.list {
  flex: 1 1 30%;
}
```

2. 선택한 비디오가 없어도 list 화면이 저렇게 나오므로 수정 

```jsx
return (
    <div className={styles.app}>
      <SearchHeader onSearch={search} />
      <section className={styles.content}>
        {selectedVideo && (
          <div className={styles.detail}>
            <VideoDetail video={selectedVideo} />
          </div>
        )}
        <div className={styles.list}>
          <VideoList videos={videos} onVideoClick={selectVideo} />
        </div>
      </section>
    </div>
  );
```

3. 그리고 선택된 비디오가 있을때 list는 한 줄씩 하고 싶음 

- **이렇게 선택된 상태에 따라 보여주는 게 다르다면 props로 조절이 가능**
- 간단한 방법으로 VideoList에 어떻게 보여줄 건지 알려주기

    ```jsx
    <VideoList
                videos={videos}
                onVideoClick={selectVideo}
                display={selectedVideo ? "list" : "grid"}
              />
    ```

videoList에 가서 확인해보면...아이템의 width는 실제로 item에서 정하니까 videoitem.jsx에 display 전달해줘야함 

```jsx
import React from "react";
import VideoItem from "../video_item/video_item";
import styles from "./video_list.module.css";

const VideoList = ({ videos, onVideoClick, display }) => (
  <ul className={styles.videos}>
    {videos.map((video) => (
      <VideoItem
        key={video.id}
        video={video}
        onVideoClick={onVideoClick}
        display={display}
      />
    ))}
  </ul>
);

export default VideoList;
```

이제 videoitem.jsx에서 

```jsx
import React from "react";
import styles from "./video_item.module.css";

const VideoItem = ({ video, video: { snippet }, onVideoClick, display }) => {
  const displayType = display === "list" ? styles.list : styles.grid;
**// 이렇게 변수를 만들고** 
  return (
    <li
      className={**`${styles.container} ${displayType}`**}
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
};

export default VideoItem;
```

4. 선택된 상태에서 비디오 보이게 하기

1. videoDetail.jsx에서 값을 수정하는데 .. 이 안에는 따로 코드를 넣어주지 않을 것이기 때문에 return값{ } 말고 ( )로 간단하게 만들 수 있다.
2. 그리고 youtube ifram의 sample에 가서 값 가져오기

    ```jsx
    import React from "react";
    import styles from "./video_detail.module.css";

    const VideoDetail = ({ video }) => (
      <section className={styles.detail}>
        <iframe
    		
          id="ytplayer"
          type="text/html"
          width="720"
          height="405"
          src="https://www.youtube.com/embed/M7lc1UVf-VE"
          frameborder="0"
          allowfullscreen
        ></iframe>
      </section>
    );

    export default VideoDetail;
    ```

3. ifram 안에 들어 있는 값 수정해주기 

    ```jsx
    import React from "react";
    import styles from "./video_detail.module.css";

    const VideoDetail = ({ video }) => (
      <section className={styles.detail}>
        <iframe
          className={styles.video}
          type="text/html"
          width="100%"
          height="500px"
          src={`https://www.youtube.com/embed/${video.id}`}
          frameborder="0"
          allowfullscreen
        ></iframe>
      </section>
    );

    export default VideoDetail;
    ```

4. 영상 밑에 title, channelTitle, 그리고 description 넣어주기 

    ```jsx
    import React from "react";
    import styles from "./video_detail.module.css";

    const VideoDetail = ({ video }) => (
      <section className={styles.detail}>
        <iframe
          className={styles.video}
          type="text/html"
          width="100%"
          height="500px"
          src={`https://www.youtube.com/embed/${video.id}`}
          frameborder="0"
          allowfullscreen
        ></iframe>
        <h2>{video.snippet.title}</h2>
        <h2>{video.snippet.channelTitle}</h2>
        <pre>{video.snippet.description}</pre>
      </section>
    );

    export default VideoDetail;
    ```

5. 디자인 
    1. detail에 padding 0.2em 주고 
    2. video_list에 있는 marign 0으로 
6. 👺pre 태그 주의! 
    1. 만약 text가 계속 연결되어 있으면 비디오가 한도 끝도 없이 옆으로 늘어짐 

     → Pre 태그에서 CSS로 해결하면 됨 


👀 white-space 가 뭔가요? : 

[https://developer.mozilla.org/en-US/docs/Web/CSS/white-space](https://developer.mozilla.org/en-US/docs/Web/CSS/white-space)

### [화이트스페이스](https://terms.naver.com/entry.naver?docId=1232515&cid=40942&categoryId=32838)white space

[화면상으로는 아무것도 표시되지 않는 문자](https://terms.naver.com/entry.naver?docId=1232515&cid=40942&categoryId=32838)

## 8.11 ~ 8.12 다시 목록으로 돌아가기 
✅ **상태만 전환하기**

1. app.jsx의 렌더부분에서 selected가 된 게 없으면 보이지 않도록, 상태에 따라 볼 수 있도록 지정되어 있기때문에 상태만 업데이트하면 됨! 

```jsx
const search = (query) => {
    youtube
      .search(query) //
      .then((videos) => {
        setVideos(videos);
        setSelectedVideo(null);
      });
  };
```

→ 

```jsx
const search = (query) => {
    setSelectedVideo(null);
    youtube
      .search(query) //
      .then((videos) => setVideos(videos));
  };
```
* ❤ 이런식으로 결과에 상관없이 항상 먼저 초기화 하는 게 더 좋음! 
