### YouTube React study 
___
## 8.1 ~ 8.2 Popular 목록 API 이용해서 받아와서 보여주기 

대표적인 기능부터 접근하자! 

- App이라는 컴포넌트 안에 '비디오 리스트' 컴포넌트를 넣고 그 안에 각각의 '비디오 아이템' 넣는다 생각하자

- **1)  APP 이라는 컴포넌트가 사용자에게 보여질때, 즉 마운트가 되었을 때 유튜브로 데이터를 받아오기**

    ✅1. App component에는 video의 목록을 가질 수 있는 state가 있어야 함 

    ```jsx
    function App() {
      const [videos, setVideos] = useState([]);
      return <h1>Hello :)</h1>;
    }
    ```
    * React hook - videos라는 변수와 update할 수 있는 함수 할당
    * 초기값은 텅텅 비어있는 videos의 목록 

    ✅2.  useEffect()를 이용하여 마운트 되었을때 유튜브 데이터 받아오기 

    - postman이용

    ```jsx
    import React, { useEffect, useState } from "react";
    import "./app.css";

    function App() {
      const [videos, setVideos] = useState([]);

      useEffect(() => {
        const requestOptions = {
          method: "GET",
          redirect: "follow",
        };

        fetch(
          "https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=AIzaSyB5KXzG9_12MArgsiWARPYJz3DF6cNfhHo",
          requestOptions
        )
          .then((response) => response.json())
          .then((result) => setVideos(result.items))
          .catch((error) => console.log("error", error));
      }, []); //🎃데이터 한 번만 받아오기 
      return <h1>Hello :)</h1>;
    }

    export default App;
    ```
  * mostPopular한 비디오의 api를 fetch해서 가져옴
  * 가져온 결과 (result)를 setVideos를 통해 업데이트하는데 우리가 관심있는 items만 가져와서 업데이트하기 

- **2) 업데이트가 되면 videos를 video_list와 video_item 컴포넌트로 전달(child component 만들기)**

    ```jsx
    import React from "react";
    import VideoItem from "../video_item/video_item";

    const VideoList = (props) => (
      <ul>
        {props.videos.map((video) => (
          <VideoItem video={video} />
        ))}
      </ul>
    );

    export default VideoList;
    ```
    * 리스트이나 순서가 상관없으니 ul로 함 
    * props로 받은 videos를 빙글빙글 돌면서 video에 있는 아이템을 생성(video component 만들기)
    * 받은 video를 videoItem에 전달
    ```jsx
    import React from "react";

    const VideoItem = (props) => <h1>{props.video.title}</h1>;

    export default VideoItem;
    ```

- 3) App 컴포넌트에 가서 rendering 값에 videoList 컴포넌트 넣어주기

    ```jsx
    import React, { useEffect, useState } from "react";
    import "./app.css";
    import VideoList from "./components/video_list/video_list";

    function App() {
      const [videos, setVideos] = useState([]);

      useEffect(() => {
        const requestOptions = {
          method: "GET",
          redirect: "follow",
        };

        fetch(
          "https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=AIzaSyB5KXzG9_12MArgsiWARPYJz3DF6cNfhHo",
          requestOptions
        )
          .then((response) => response.json())
          .then((result) => setVideos(result.items))
          .catch((error) => console.log("error", error));
      }, []);
      return <VideoList videos={videos} />;
    }

    export default App;
    ```
    💥li에는 유니크한 키 사용하기 
    ```js
    <VideoItem key={video.id} video={video} />
    ```

    ‼주의! 

    ```jsx
    import React from "react";

    const VideoItem = (props) => <h1>{props.video.snippet.title}</h1>;

    export default VideoItem;
    ```

    개발툴을 보면 props에서 받아온 video 안에 title은 snippet 안에 있으므로 반드시 snippet도 적어줘야 함!!

    이렇게 하다가 작동이 잘 안 되면 개발 툴 이용!
    
    ## 8.3 ~ 8.4 리스트 아이템 만들기 
    
    1) list 아이템에서 사용되는 videoItem이므로 li를 쓴다

    ```jsx
    import React from "react";

    const VideoItem = (props) => (
      <li>
        <img
          src={props.video.snippet.thumbnails.medium.url}
          alt="video thumbnail"
        />
      </li>
    );

    export default VideoItem;
    ```
    * li 안에 img를 넣어주는 것이므로 img tag 사용해서 비지니스 로그 이용 
    * 뭐를 넣어야 할지 모르겠으면 postman이나 받은 정보를 보면서 넣기 

2) 메타데이터 정보 추가
    1. 스타일링을 위해 div 로 묶어주기
    2. P태그를 이용해서 title과 chanel title 넣어주기 

    ```jsx
    import React from "react";

    const VideoItem = (props) => (
      <li>
        <img
          src={props.video.snippet.thumbnails.medium.url}
          alt="video thumbnail"
        />
        <div>
          <p>{props.video.snippet.title}</p>
          <p>{props.video.snippet.channelTitle}</p>
        </div>
      </li>
    );

    export default VideoItem;
    ```
    * 위의 코드를 보면 props.video.snippet이 반복되어 있다. 

3) Deconstructing 팁

    ```jsx
    import React from "react";

    const VideoItem = (props) => (
      <li>
        <img
          src={props.video.snippet.thumbnails.medium.url}
          alt="video thumbnail"
        />
        <div>
          <p>{props.video.snippet.title}</p>
          <p>{props.video.snippet.channelTitle}</p>
        </div>
      </li>
    );

    export default VideoItem;
    ```

    이럴 경우 쓸 수 있는 것이 바로 **Deconstructing!!!** 

    ```jsx
    import React from "react";

    const VideoItem = **({ video })** => (
      <li>
        <img
          src={video.snippet.thumbnails.medium.url}
          alt="video thumbnail"
        />
        <div>
          <p>{video.snippet.title}</p>
          <p>{video.snippet.channelTitle}</p>
        </div>
      </li>
    );

    export default VideoItem;
    ```
    * props라는 인자에서 props 안에 있는 video를 바로 받아 올 수 있음 
    * 이름이 동일하면 그대로 쓰면 할당됨
    * 이름을 바꾸고 싶으면  ({ video: videoItem })
    * 그리고 반복되는 props라는 부분 지워도 video로 바로 접근 가능 

    그래도 video 안에 있는 snippet이 반복되는 부분이 있으므로 

    ```jsx
    import React from "react";

    const VideoItem = **({ video: { snippet } })** => (
      <li>
        <img src={snippet.thumbnails.medium.url} alt="video thumbnail" />
        <div>
          <p>{snippet.title}</p>
          <p>{snippet.channelTitle}</p>
        </div>
      </li>
    );

    export default VideoItem;
    ```
    * video 안에 있는 snippet 받아오기 

4) PostCSS를 이용하여 디자인 !
    1. video_item.jsx에다가 styles import 하기 

    ```jsx
    import React from "react";
    import styles from "./video_item.module.css";
    ```

    2. 클래스 넣어주기 

    ```jsx
    import React from "react";
    import styles from "./video_item.module.css";

    const VideoItem = ({ video: { snippet } }) => (
      <li className={styles.video}>
        <img
          className={styles.thumbnail}
          src={snippet.thumbnails.medium.url}
          alt="video thumbnail"
        />
        <div>
          <p className={styles.title}>{snippet.title}</p>
          <p className={styles.channel}>{snippet.channelTitle}</p>
        </div>
      </li>
    );

    export default VideoItem;
    ```

- 5) video_item CSS

    ```css
    .video {
      display: flex;
      align-items: center;
      border: 1px solid lightgray;
      margin-bottom: 0.2em;
      cursor: pointer;
      transition: transform 250ms ease-in;
    }

    .video:hover {
      transform: scale(1.02);
    }
    .thumbnail {
      width: 40%;
    }

    .title,
    .channel {
      margin: 0;
    }

    .channel {
      font-size: 0.9rem;
    }
    ```

6) default page에서 한 줄에 두 개씩 나오기를 원함
    1. 이런 경우는 video_list에서 디자인 수정 필요 
    2. 먼저 클래스 지정하고 import styles

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

    ```css
    .videos {
      display: flex;
      flex-wrap: wrap; /*아이템이 크기에 맞게 내려감  */
      list-style: none;
      padding-left: 0;
    }
    ```

    3. 다시 video_item.module.css로 가서 video의 width 50%로

    ```css
    .video {
      display: flex;
      align-items: center;
      **width: 50%;**
      border: 1px solid lightgray;
      margin-bottom: 0.2em;
      cursor: pointer;
      transition: transform 250ms ease-in;
    }
    ```
    * 50%를 해도 box-sizing이 content를 베이스로 되어 있어 옆에 border가 추가 되어서 ..실제로 화면에서는 50%이상이기 때문에 item들이 동시에 2개가 나오지 않음 
    * 그러므로 box-sizing을 border box로 해주면 해결 됨! 

    ❤ 보통 기본으로 box-sizing을 border box로 하면 간편하기 때문에 index에서 * { } 전체적으로 css 바꾸기 

    최종 css 

    ```css
    .container {
      width: 50%;
      padding: 0.2em;
    }

    .video {
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      border: 1px solid lightgray;
      box-shadow: 3px 3px 5px 0px rgba(191, 191, 191, 0.53);
      cursor: pointer;
      transition: transform 250ms ease-in;
    }

    .video:hover {
      transform: scale(1.02);
    }
    .thumbnail {
      width: 40%;
      height: 100%;
    }

    .metadata {
      margin-left: 0.2em;
    }
    .title,
    .channel {
      font-size: 0.8rem;
      margin: 0;
    }

    .channel {
      font-size: 0.6rem;
    }
    ```

   
