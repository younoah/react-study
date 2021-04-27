## 9.2 React Router란? 

✅ 우리가 특정 URL을 이용했을 때, 어떤 경로를 이용해서 데이터를 방아 올 건지 길을 결정해주는 아이 

✅ A mechanism where HTTP requests gets link to specific web pages/resources. 

✅ Router determines which page should be open when a user visits a certain path. 

e.g. www.youme.com/**profile** 메인 경로 다음에 이런 profile, home, login 같은 경로가 붙었을 때 어떤 페이지를 보여줄 건지 결정하고 도와주는 것이 라우팅! 


#### 싱글 페이지 어플리케이션(SPA)이란? 
  - 하나의 URL로 한 페이지가 로딩 -> 사용자가 클릭, 링크 클릭 시 -> ❌새로운 페이지 ❌리프레쉬  -> 부분적인 내용만 업데이트 

🔻 리액트 라우터의 장점 
1. SPA을 유지하면서 URL을 붙일 수 있다.
2. 북마크 추가 가능
3. 뒤로 가기, 앞으로 가기 등의 네비게이션도 추가 

✅ <BrowserRouter> - 최상위 컴포넌트. 자동적으로 라우터 돔에 있는 기본적인 것으로, import해오기
  
두 가지 방식! 
1. ✅ <Swtich> - Looks through its children <Route>s and renders the first one that matches the current URL.
 
```jsx
import logo from "./logo.svg";
import "./App.css";
import { BrowserRouter, Route, Switch } from "react-router-dom";
import Home from "./components/home";
import Profile from "./components/profile";

function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route path="/home">
          <Home />
        </Route>
        <Route path="/profile">
          <Profile />
        </Route>
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```

✨ 혹시 path에 두가지 이상을 넣고 싶으면 

[https://reactrouter.com/web/api/Route/path-string-string](https://reactrouter.com/web/api/Route/path-string-string)

```jsx
import logo from "./logo.svg";
import "./App.css";
import { BrowserRouter, Route, Switch } from "react-router-dom";
import Home from "./components/home";
import Profile from "./components/profile";

function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route path={["/", "/home"]}>
          <Home />
        </Route>
        <Route path="/profile">
          <Profile />
        </Route>
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```

2. ✅ 바로 메뉴에 추가 <Link >

💥중요한 점! exact를 쓰지 않으면 "/"으로만 간다. 
```js
 <Route path={["/", "/home"]} exact>
 ```
 
3. ✅ 버튼을 클릭하면 profile과 home으로!

* profile 안에는 props가 전달 되지 않았음

해결 방법 
  1) component={home}으로 props 전달
  
  👺 하지만 이 방법은 성능이 좋지 않다 
  * The router uses React.createElement to create a new React element from the given component. -> 렌더링 될 때마다 새로운 컴포넌트 만드는 것과 같음 

  2) react router 웹에 나와있는 API Hooks의 useHistory 사용하기

[https://reactrouter.com/web/api/Hooks/usehistory](https://reactrouter.com/web/api/Hooks/usehistory)

  * 엄청 간단하게 const history = useHistory(); 이렇게 받아오기만 하면 됨 
  ```jsx
import React from "react";
import { useHistory } from "react-router";

const Home = (props) => {
  const history = useHistory();
  return (
    <>
      <h1>Home</h1>
      <button
        onClick={() => {
          history.push("/profile");
        }}
      >
        Go to Profile
      </button>
    </>
  );
};

export default Home;
```

최종 정리 

*** HTML에서 링크를 쓰는 것과 가장 큰 차이점은? 

> 네비게이션을 쓰거나 버튼을 클릭해서 다른 경로로 이동했을 때 우리의 전체적인 페이지는 그대로 있음 -> 해당하는 내용만 업데이트


> HTML에서 링크를 쓰면 HTML의 페이지가 다른 HTML로 교체되는 반면에 리액트 라우팅을 이용하면 기존의 페이지는 그대로 유지한 상태에서 ~ 싱글페이지 어플리케이션에서 제공하는 장점을 그대로 유지하면서 라우팅을 할 수 있기때문에 성능이 좋음!**

  
