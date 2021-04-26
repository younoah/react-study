## 8.14 

1. youtube 가 마운트 될때만 업데이트 하게 함 


    ```jsx
    import React, { useEffect, useState } from "react";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";
    import VideoDetail from "./components/video_detail/video_detail";

    function App({ youtube }) {
      const [videos, setVideos] = useState([]);
      const [selectedVideo, setSelectedVideo] = useState(null);

      const selectVideo = (video) => {
        setSelectedVideo(video);
      };
      const search = (query) => {
        setSelectedVideo(null);
        youtube
          .search(query) //
          .then((videos) => setVideos(videos));
      };

      useEffect(() => {
        youtube
          .mostPopular() //
          .then((videos) => setVideos(videos));
      }, [youtube]);

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
              <VideoList
                videos={videos}
                onVideoClick={selectVideo}
                display={selectedVideo ? "list" : "grid"}
              />
            </div>
          </section>
        </div>
      );
    }

    export default App;
    ```

2. ifram에 unique title 간단하게 넣어주기 

```jsx
import React from "react";
import styles from "./video_detail.module.css";

const VideoDetail = ({ video }) => (
  <section className={styles.detail}>
    <iframe
      className={styles.video}
      type="text/html"
      title="youtube"
      width="100%"
      height="500px"
      src={`https://www.youtube.com/embed/${video.id}`}
      frameBorder="0"
      allowFullScreen
    ></iframe>
    <h2>{video.snippet.title}</h2>
    <h2>{video.snippet.channelTitle}</h2>
    <pre className={styles.description}>{video.snippet.description}</pre>
  </section>
);

export default VideoDetail;
```

3. video item들은 클릭이나 디스플레이가 바뀌지 않는 한 렌더링 되야 할 이유가 없음 -> hook의 memo 사용 

```jsx
import React, { memo } from "react";
import styles from "./video_item.module.css";

const VideoItem = memo(
  ({ video, video: { snippet }, onVideoClick, display }) => {
    const displayType = display === "list" ? styles.list : styles.grid;
    return (
      <li
        className={`${styles.container} ${displayType}`}
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
export default VideoItem;
```

4. app이라는 컴포넌트는 function component이기 때문에 관련된 state나 props가 바뀌면 우리가 정의한 멤버변수가 다시 만들어짐. 즉, 콜백함수를 이 함수들이 새롭게 만듦. 

State가 바뀔 때마다 여기 search라는 것은 새로운 함수를 가리키게 됨 

그리고 search는 searchHeader로 전달이 되기 때문에 새로운 props로 전달되는 거라 동일. 

그러므로 **useCallback**을 사용하자 (useCallback을 이용해서 search가 한 번만 만들게 함)

💥 useEffect와 마찬가지로 두번째 인자에 dependency list를 전달하지 않으면 계속해서 새로운 것 만듦.

```jsx
const search = useCallback((query) => {
    setSelectedVideo(null);
    youtube
      .search(query) //
      .then((videos) => setVideos(videos));
  }, [youtube]);
```

❗❗ 주의해서 사용해야 함 

useCallback은 한 번 만들면 메모리상에 계속 보관하기 때문에 메모리에 영향이 많이 감 ! 

그러므로 정말 써야할 때만 사용하기! 

(e.g. ✅써야 할 때 : 자식 컴포넌트에 props로 전달할 때 - 새로운 콜백을 전달하면 자식 컴포넌트에서 re-rendering이 발생할 수 있으니까!

e.g. ✅ 쓰지 않아야 할 때: 만약 자식 컴포넌트가 아닌 간단한 JSX를 이용한 div 태그라던지 button같은 이벤트를 처리하는 콜백을 전달할때는 새로운 것을 전달해도 re-rendering이 되지 않으니 크게 상관없으므로 쓰지 말자! )


## 8.14 Fetch web api & Axios Library

우리는 브라우저 상에서 이런 fetch라는 웹 API 중에 하나인 이 아이를 사용하면 간단하게 네트워크 통신을 할 수 있다. 

하지만 !!!

1. 긴 url을 query params를 섞어서 써야 하고
2. 받은 response를 JSON으로 일일이 변환 해야 함 

이런 반복적인 일 때문에 개발자들이 공통적으로 쓰는 다양한 라이브러리가 있다. 

# AXIOS

- 간단하게 fetch 대용으로 사용할 수 있음
- [https://github.com/axios/axios](https://github.com/axios/axios)

## Fectch 와 비교 :

1. fetch는 internet explore에서 동작하지 않으나 **axios는 다른 부라우저와 호환이 잘 됨** 
2. axios는 예전 브라우저라면 예전에 썼던 XMLHttpRequest라는 object를 쓰고 , 최신 것이라면 fetch를 쓰게 되어 있음 
3. json으로 변환하지 않게 도와줌 
4. fetch보다 가독성이 좋음 

1. cmder에 라이브러리 추가 

    ```jsx
    yarn add {package} // 추가하고자 할때 쓰는 것 
    =======
    yarn add axios
    ```

2. 추가가 된 다음 비주어스튜디오에서 package.json을 보면 axios가 추가 된 것을 확인 할 수 있다 

3.  axios폴더에 import 해주기 

npm, yarn을 써서 라이브러를 추가하는 것이 엄청 간단함
그냥 저렇게 적기만 하면 됨 

4. base 클라이언트 만들기 

```jsx
import axios from "axios";

class Youtube {
  constructor(key) {
    this.youtube = axios.create({
      baseURL: 'https://youtube.googleapis.com/youtube/v3',
      params: {key: key},
    })
  }
}
```
* 기본적으로 유튜브와 통신하는 베이스 유튜브 클라이언트 생성

5. mostPopular 

```jsx
async mostPopular() {
    const response = await this.youtube.get("videos", {
      params: {
        part: "snippet",
        chart: "mostPopular",
        maxResults: 25,
      },
    });
    return response.data.items;
  }
```
* fetch와 다르게 JSON으로 변환하지 않아도 라이브러리 자체에서 JSON으로 변환해줌

6. search 

```jsx
async search(query) {
    const response = await this.youtube.get("search", {
      params: {
        part: "snippet",
        maxResults: 25,
        q: query,
        type: video,
      }
    })
  
  return response.data.items.map(item => ({...item, id: item.id.videoId}))
}
```

7. Dependency injection을 깔끔하게 하고 싶다면~

youtube라는 클래스에서 어떻게 어떤 것을 이용해서 통신하는지 모르게 하고 싶다면! 

key도 전달하지 않고 단순하게 httpClient만 받아서 사용

```jsx
class Youtube {
  constructor(httpClient) {
    this.youtube = httpClient
  }
```

그리고 실제로 사용하는 index.js에 가서 이렇게 수정

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./app";
import Youtube from "./service/youtube";

const httpClient = axios.create({
  baseURL: "https://youtube.googleapis.com/youtube/v3",
  params: { key: process.env.REACT_APP_YOUTUBE_API_KEY },
});
const youtube = new Youtube(httpClient);
ReactDOM.render(
  <React.StrictMode>
    <App youtube={youtube} />
  </React.StrictMode>,
  document.getElementById("root")
);
```

