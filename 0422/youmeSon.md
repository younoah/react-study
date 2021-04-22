### Youtube Clone

## 8.5 ~ 8.7 검색 기능 구현하기 

1) component에 search_header 폴더 만들고 app.jsx에 헤더 추가하기

    ```jsx
    import React, { useEffect, useState } from "react";
    import "./app.css";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";

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
      return (
        <div className={styles.app}>
          <SearchHeader />
          <VideoList videos={videos} />;
        </div>
      );
    }

    export default App;
    ```
    * className이 app인 것에 search_header와 videoList를 넣은 것을 볼 수 있다. 
    * 전체적으로 퍼지는 것을 막고, 사이즈를 지정하려면 이 어플리케이션이 들어있는 index에 가서 수정 
    
    ```css
    body {
      margin: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
        "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
        sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
    }

    code {
      font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
        monospace;
    }

    * {
      box-sizing: border-box;
    }

    #root {
      display: flex;
      justify-content: center;
    }
    ```
    * app과 같은 모든 application들이 index에 들어 있으니 여기서 수정 

2)  search_head.jsx에 return값을 줘서 rendering할 것 적기

    ```jsx
    import styles from "./search_header.module.css";
    import React from "react";

    const SearchHeader = (props) => {
      return (
        <header className={styles.header}>
          <img className={styles.img} src="/images/logo.png" alt="logo" />
          <h1 className={styles.title}>YouTube</h1>
          <input className={styles.input} type="search" placeholder="Search..." />
          <button className={styles.button} type="submit">
            <img src="/images/search.png" alt="search" />
          </button>
        </header>
      );
    };

    export default SearchHeader;
    ```
    * button은 위에 있는 input과 함께 쓰이기 때문에 type을 submit으로 해준다. 

    💥 여기서 왜!!! 이미지의 주소가 public/images/logo.png가 아닐까?

    → **리액트에서 빌드가 된 최종 배포상태에서 동작하는 경로를 기준으로 설정이 되기 때문에 /public/..을 붙여주지 않아도 된다~! 배포되면 public/안에 있는 index.html 등 리소스들이 최상위 루트로 지정되기 때문** 

3) 디자인

    ```css
    .header {
      display: flex;
      height: 4rem;
      padding: 0.8em 1em;
      background: black;
      color: white;
    }

    .logo {
      display: flex;
      align-items: center;
      margin-right: 1em;
    }

    **/* 항상 flexbox안에서 가득 채웠으면 좋겠을때 flex-basis 쓰기!!! */**
    .input {
      flex-basis: 100%;
      font-size: 1.2rem;
      outline: 0;
    }

    .button {
      background-color: darkgray;
    }

    .buttonImg {
      height: 100%;
      padding: 0.5em 0.2em;
      outline: none;
      border: none;
      cursor: pointer;
    }

    ```

4) 이제 본격적인 기능 구현!
    1. 버튼이 클릭이 되면 처리해주기 + input 필드에서 사용자가 엔터를 치면 처리해주기

    ```jsx
    <input className={styles.input} type="search" placeholder="Search..." onKeyPress={onKeyPress}/>
          <button className={styles.button} type="submit" onClick={onClick}>
            <img
              className={styles.buttonImg}
              src="./images/search.png"
              alt="search"
            />
          </button>
    ```

    ```jsx
    import styles from "./search_header.module.css";
    import React from "react";

    const SearchHeader = (props) => {
      const inputRef = useRef();
      const handleSearch = () => {};
      const onClick = () => {
        handleSearch();
      };

      const onKeyPress = (event) => {
        if (event.key === "Enter") {
          handleSearch();
        }
      };
      return (
        <header className={styles.header}>
          <div className={styles.logo}>
            <img className={styles.img} src="./images/logo.png" alt="logo" />
            <h1 className={styles.title}>YouTube</h1>
          </div>
          <input
            ref={inputRef}
            className={styles.input}
            type="search"
            placeholder="Search..."
            onKeyPress={onKeyPress}
          />
          <button className={styles.button} type="submit" onClick={onClick}>
            <img
              className={styles.buttonImg}
              src="./images/search.png"
              alt="search"
            />
          </button>
        </header>
      );
    };

    export default SearchHeader;
    ```
    * click이 되어도, key가 눌려도 작동되는 것은 하나이므로 handleSearch라는 함수를 만들어서 넣기 
    * handleSearch에서 필요한 것은 input값! 
    * input값은 ref를 이용하여 지정해주기 
    * 그리고 key다운이 되었을때 모든 키가 눌릴때 작동 되면 안 되니, enter가 됐을때 눌리게 if 문 걸기 

    ```jsx
    import styles from "./search_header.module.css";
    import React, { useRef } from "react";

    const SearchHeader = ({ **onSearch** }) => {
      const inputRef = useRef();

      const handleSearch = () => {
        const value = inputRef.current.value;
        console.log(value);
        **// 이 안에서 검색을 해주는 것이 아니라 , 검색하는 것을 props로 받아와야 함!(onSearch)
        // 검색하는 이벤트가 발생되면 내가 전달 해준 onSearch라는 콜백 함수를 불러**
        **onSearch**(value);
        inputRef.current.value = "";
      };
      const onClick = () => {
        handleSearch();
      };

      const onKeyPress = (event) => {
        if (event.key === "Enter") {
          handleSearch();
        }
      };
      return (
        <header className={styles.header}>
          <div className={styles.logo}>
            <img className={styles.img} src="./images/logo.png" alt="logo" />
            <h1 className={styles.title}>YouTube</h1>
          </div>
          <input
            ref={inputRef}
            className={styles.input}
            type="search"
            placeholder="Search..."
            onKeyPress={onKeyPress}
          />
          <button className={styles.button} type="submit" onClick={onClick}>
            <img
              className={styles.buttonImg}
              src="./images/search.png"
              alt="search"
            />
          </button>
        </header>
      );
    };

    export default SearchHeader;
    ```

5) app.jsx에서 onSearch라는 함수 만들고 props로 내보내기

    ```jsx
    import React, { useEffect, useState } from "react";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";

    function App() {
      const [videos, setVideos] = useState([]);

      const search = (query) => {
        const requestOptions = {
          method: "GET",
          redirect: "follow",
        };

        fetch(
          `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=AIzaSyB5KXzG9_12MArgsiWARPYJz3DF6cNfhHo`,
          requestOptions
        )
          .then((response) => response.json())
          .then((result) => setVideos(result.items))
          .catch((error) => console.log("error", error));
      };

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
      return (
        <div className={styles.app}>
          <SearchHeader onSearch={search} />
          <VideoList videos={videos} />;
        </div>
      );
    }

    export default App;
    ```
    1. 먼저 search라는 함수를 만들어 postman에 있는 search api를 fetch해서 가져오기 
    2. 이때 query를 받아서 fetch주소에 query 넣어 주기 
    3. rendering 되는 부분에서 onSearch={search}를 적어 searchhdeaer 컴포넌트에서 props로 보내기 

    ❗이때 id가 오브젝트 형태로 반환이 되서 콘솔창에 id가 같은 child가 있다고 error가 난다 

    ```jsx
    const search = (query) => {
        const requestOptions = {
          method: "GET",
          redirect: "follow",
        };

        fetch(
          `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=AIzaSyB5KXzG9_12MArgsiWARPYJz3DF6cNfhHo`,
          requestOptions
        )
          .then((response) => response.json())
          .then((result) =>
            result.items.map((item) => ({ ...item, id: item.id.videoId }))
          ) **//변환하는 과정, id만 덮어써줌**
          .then((items) => setVideos(items))
          .catch((error) => console.log("error", error));
      };
    ```
    🎃 재미있던 부분 
    ```js
    .then((result) =>
            result.items.map((item) => ({ ...item, id: item.id.videoId }))
          ) **//변환하는 과정, id만 덮어써줌**
          
    ```
    
## 8.8 리팩토링 
```jsx
import React, { useEffect, useState } from "react";
import SearchHeader from "./components/search_header/search_header";
import VideoList from "./components/video_list/video_list";
import styles from "./app.module.css";

function App() {
  const [videos, setVideos] = useState([]);

  const search = (query) => {
    const requestOptions = {
      method: "GET",
      redirect: "follow",
    };

    fetch(
      `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=나는중요한키입니다`,
      requestOptions
    )
      .then((response) => response.json())
      .then((result) =>
        result.items.map((item) => ({ ...item, id: item.id.videoId }))
      ) //변환하는 과정, id만 덮어써줌
      .then((items) => setVideos(items))
      .catch((error) => console.log("error", error));
  };

  useEffect(() => {
    const requestOptions = {
      method: "GET",
      redirect: "follow",
    };

    fetch(
      "https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=나는중요한키입니다",
      requestOptions
    )
      .then((response) => response.json())
      .then((result) => setVideos(result.items))
      .catch((error) => console.log("error", error));
  }, []);
  return (
    <div className={styles.app}>
      <SearchHeader onSearch={search} />
      <VideoList videos={videos} />;
    </div>
  );
}

export default App;
```

# MVC에서 어긋나지 않게 하기 (역할에 맡게 세부적으로 레이어를 나눠야 함)

- 한가지의 것이 한 가지의 responsibility만 가지게 해야 함

→ 테스터도 쉽게하고, 유지 보수도 쉬움. 개발할때도 더 쉽고 재밌음 

- 여기서 **react는 view layer를 담당** →즉, 단순히 사용자에게 데이터를 보여주고, 클릭이 되면 그 클릭 이벤트 자체를 처리하는 뷰에 관련된 아이

    (나쁘게 말하면 **약간 멍청한 아이**) : 멍청하게 만들수록 좋음 

**위 app.jsx의 두가지 문제점은?**

1. **코드 안에 중요한 키가 들어 있음 (깃허브에 올리면 안 좋음)**
2. **컴포넌트 안에 네트워크 통신을 하는 로직이 들어 있음** 

> 그러므로 네트워크 통신을 하는 아이들은 따로 클래스로 만들어 놓기
app에서 필요한 dependency를 주입 (**dependency injection**이라고 불림)

### 해결법

- 컴포넌트 안에 네트워크 통신을 하는 로직이 있을때
    1. src 안에 service라는 새로운 폴더를 만든다. 
    2. youtube에 필요한 네트워크 서비스를 만드는 youtube.js라는 순수 자바스크립트 파일을 생성

        1) constructor에 키 받아주기 

        ```jsx
        class Youtube {
          constructor(key) {
            this.key = key;
          }
        }
        ```

        2) app.jsx 네트워크에서 보면 search와 video에 공통적으로 쓰이는 부분 가져오기 

        ```jsx
        class Youtube {
          constructor(key) {
            this.key = key;
            this.getRequestOptions = {
              method: "GET",
              redirect: "follow",
            };
          }
        }
        ```

    3) 이제 함수를 만들어 줘야하는데, 함수는 두 가지가 나올 것으로 예상 

    ```jsx
    class Youtube {
      constructor(key) {
        this.key = key;
        this.getRequestOptions = {
          method: "GET",
          redirect: "follow",
        };
      }
      **//함수는 두 가지 정도가 나올 것임! (search와 mostPopular video)**
      mostPopular() {
        return fetch(
          "https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=나는중요한키입니다",
          this.getRequestOptions
        )
          .then((response) => response.json())
          .then((result) => result.items);
      }

      search(query) {
        return fetch(
          `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=나는중요한키입니다`,
          requestOptions
        )
          .then((response) => response.json())
          .then((result) =>
            result.items.map((item) => ({ ...item, id: item.id.videoId }))
          ); //변환하는 과정, id만 덮어써줌
      }
    }
    export default Youtube;
    ```
    * return fetch를 하고 then이 나오면서 프로미스가 만들어지므로, 그 프로미스를 리턴해야해서 Return을 꼭 붙여줘야한다.  

    4) 이제 service에 있는 youtube를 받아와야함!!! 

    - 이것을 바로 app.jsx에 받아오면 매번 youtube 클래스가 생기고 계속 네트워크를 하므로 안 좋음
    - 그러므로 youtube라는 props로 받아오기

    → 상위 파일인 index.js에서 class youtube import 하기 

    ```jsx
    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.css";
    import App from "./app";
    import Youtube from "./service/youtube";

    const youtube = new Youtube("key넣기");
    ReactDOM.render(
      <React.StrictMode>
        <App youtube={youtube}/>
      </React.StrictMode>,
      document.getElementById("root")
    );
    ```
    * 이렇게 하면 index.js가 호출 될때 딱 한 번만 class youtube가 만들어짐 🙂

    5) app.jsx에서 youtube에 있는 search api & mostPopular api를 이용하기

    ```jsx
    import React, { useEffect, useState } from "react";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";

    function App({ youtube }) {
      const [videos, setVideos] = useState([]);

      const search = (query) => {
        youtube.search(query).then(console.log);
      };

      useEffect(() => {
        youtube.mostPopular().then(console.log);
      }, []);
      return (
        <div className={styles.app}>
          <SearchHeader onSearch={search} />
          <VideoList videos={videos} />;
        </div>
      );
    }

    export default App;
    ```

    이제 받아온 아이템을 setVideos라는 리액트 훅의 api를 통해서 업데이트 하기 

    ```jsx
    import React, { useEffect, useState } from "react";
    import SearchHeader from "./components/search_header/search_header";
    import VideoList from "./components/video_list/video_list";
    import styles from "./app.module.css";

    function App({ youtube }) {
      const [videos, setVideos] = useState([]);

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
          <VideoList videos={videos} />;
        </div>
      );
    }

    export default App;
    ```

### 코드 안에 중요한 키가 들어 있을 때
   1. 먼저 key 복사한 상태에서 ${this.key}로 변경 

    ```jsx
    class Youtube {
      constructor(key) {
        this.key = key;
        this.getRequestOptions = {
          method: "GET",
          redirect: "follow",
        };
      }
      //함수는 두 가지 정도가 나올 것임! (search와 mostPopular video)
      mostPopular() {
        return fetch(
          `https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=${this.key}`,
          this.getRequestOptions
        )
          .then((response) => response.json())
          .then((result) => result.items);
      }

      search(query) {
        return fetch(
          `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=${this.key}`,
          this.getRequestOptions
        )
          .then((response) => response.json())
          .then((result) =>
            result.items.map((item) => ({ ...item, id: item.id.videoId }))
          ); //변환하는 과정, id만 덮어써줌
      }
    }

    export default Youtube;

    ```

  2. index.js에 가서

    ```jsx
    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.css";
    import App from "./app";
    import Youtube from "./service/youtube";

    const youtube = new Youtube("나는중요한키입니다");
    ReactDOM.render(
      <React.StrictMode>
        <App youtube={youtube} />
      </React.StrictMode>,
      document.getElementById("root")
    );
    ```

    하지만 저렇게 직접 키를 넣어서 드러내면 안된다!!! 

   3. create react app 웹사이트에 가서 확인

   - 최상위 파일에 .env라는 파일 만들기

    ```jsx
    REACT_APP_YOUTUBE_API_KEY=나는중요한키입니다
    ```

  그리고 이 변수를 index.js에 쓰기 

    ```jsx
    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.css";
    import App from "./app";
    import Youtube from "./service/youtube";

    const youtube = new Youtube(process.env.REACT_APP_YOUTUBE_API_KEY);
    ReactDOM.render(
      <React.StrictMode>
        <App youtube={youtube} />
      </React.StrictMode>,
      document.getElementById("root")
    );
    ```

   이렇게 새로운 폴더를 넣는데 잘 안 될때는 cmder에 가서 yarn start를 취소하고(ctrl+ c) 다시 yarn start하기 

   → 그러니까 된다 ❤

   4.  git ignore에 추가하기

   gitignore파일에 들어가서 #API keys치고 .env 넣으면 github에서 추적되지 않음 

   5. promise 를 리턴하니까 async를 쓰는 게 좋음

    ```jsx
    class Youtube {
      constructor(key) {
        this.key = key;
        this.getRequestOptions = {
          method: "GET",
          redirect: "follow",
        };
      }
      //함수는 두 가지 정도가 나올 것임! (search와 mostPopular video)
      async mostPopular() {
        const response = await fetch(
          `https://youtube.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=${this.key}`,
          this.getRequestOptions
        );
        const result_1 = await response.json();
        return result_1.items;
      }

      async search(query) {
        const response = await fetch(
          `https://youtube.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=${this.key}`,
          this.getRequestOptions
        );
        const result_1 = await response.json();
        return result_1.items.map((item) => ({ ...item, id: item.id.videoId })); //변환하는 과정, id만 덮어써줌
      }
    }

    export default Youtube;
    ```
