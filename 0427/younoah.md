## React Router

### 웹에서 라우팅이란?

사용자가 요청하는 URL링크를 요청했을 때

어떤 특정한 페이지로 연결 할 건지를 결정하는 매커니즘이다.



```
www.card-maker.com
www.card-maker.com/home
www.card-maker.com/profile
www.card-maker.com/login
```

위와 같이 경로에 따라 어떤 페이지를 보여줄지 결정하는 것이 라우터이다.



### SPA란?

SPA(single page application)은 하나의 URL로 한번 페이지가 로딩되고 나면 그 안에서 사용자가 다른 페이지를 클릭햇을 때 새로운 페이지가 열리는 것이 아닌(전체적인 페이지가 리프레쉬 되는것이 아닌) 부분적인 내용만 업데이트 된다.

 SPA의 단점으로는 내비게이션이 불가능하다. 즉 앞으로가기 뒤로가기가 불가능하고 특정 원하는 화면을 북마크 할 수 없다.



### React Router란?

SPA를 유지하면서 URL을 지정하여 네비게이션과 북마크가 가능하도록 도와주는것이 리엑트  라우터이다.

즉 SPA의 하나의 페이지를 재사용하는 장점과 라우팅을 결합시키는 것이다.



## 리엑트 라우터 사용하기



### 프로젝트에 리엑트 라우터 추가

```shell
$ yarn add react-router-dom
```





- home.jsx

```jsx
import React from 'react';
import { useHistory } from 'react-router-dom';

const Home = props => {
    const history = useHistory();
    return (
        <>
            <h1>Home</h1>
            <button
                    onClick={() => {
						history.push('/profile');
                    }}
                >
            	Go to Profile
        	</button>
        </>
    );
};

export default Home;
```



- profile.jsx

```jsx
import React from 'react';
import { useHistory } from 'react-router-dom';

const Profile = props => {
    const history = useHistory();
    return (
        <>
            <h1>Profile</h1>
            <button
                    onClick={() => {
						history.push('/home');
                    }}
                >
            	Go to Home
        	</button>
        </>
    );
};

export default Profile;
```



- app.jsx

```jsx
import {BrowserRouter, Link, Route, Switch} from 'react-router-dom';
import Home from './components/home';
import Home from './components/profile';

function App() {
    return (
        <nav>
            <Link to="/">Home</Link>
            <Link to="/profile">Profile</Link>
        </nav>
    	<BrowserRouter>
            <Switch>
                <Route path={['/', ' /home']} exact>
                    <Home />
                </Route>
                <Route path='/profile'>
                    <Profile />
                </Route>
            </Switch>
        </BrowserRouter>
    )
}

export default App;
```



### Link

`Switch` 밖에서도 자유롭게 경로를 이동할 수 있는 방법을 제공한다.

올바른 동작을 위해서는 컴포넌트의 파라미터에 `exact=true` 로 설정하여 한다. (**궁금증**)



### eaxat

컴포넌트에 `exact` 파라미터를 전달하면 `exact=true` 가 된다.

|  path  | location.pathname |  exact  | matches? |
| :----: | :---------------: | :-----: | :------: |
| `/one` |    `/one/two`     | `true`  |    no    |
| `/one` |    `/one/two`     | `false` |   yes    |

라우트의 path를 `/one` 이라고 설정하였을 때

 `exact=true` : `/one/two` 이라는 경로 처럼 `/one` 경로 뒤에 부가적인 경로가 추가되면 match가 되지 않는다.

 `exact=flase` : `/one/two` 이라는 경로 처럼 `/one` 경로 뒤에 부가적인 경로가 추가되어도 `/one` 이라는 경로가 포함되어 있기 때문에 match가 된다.



> **궁금증**
>
> 실습 예제에서 
>
> ```jsx
> <nav>
>     <Link to="/">Home</Link>
>     <Link to="/profile">Profile</Link>
> </nav>
> ```
>
> 이부분이 작동하지 않아서 `exact` 파라미터를 추가로 전달하니 잘 작동이 되었다.
>
> 저 링크를 눌러도 경로는 `/home`, `/profile` 로 경로가 나와서 굳이 exact 파라미터를 전달하지 않아도 되는것 같은데 왜 그런걸까?



### useHistory

`history`의 기능을 사용할 수 있다. 뒤로가기(`goBack()`), 앞으로가기(`goForward()`), 특정경로로 가기(`push()`) 등을 이용할 수 있는 Hook이다.



> 이전에는 아래와 같이 자식 컴포넌트를 라우트의 파라미터로 전달하여 리렌더링 하는방식을 사용했다.
>
> ```jsx
> <Route path='/profile' component={Home}>
> </Route>
> ```
>
> 이렇게 파라미터로 받아와서 `Home` 컴포넌트의 props에 histoty, location, match와 같은 아이들이 전달되어 `Home` 의 자식 컴포넌틍 안에서 사용할 수 있었다.
>
> 하지만 이런 방식을 사용하여 `history` 를 사용하면 자식컴포넌트가 리렌더링 되기 때문에 성능이 떨어진다.
>
> 하지만 `useHistory` 를 사용하면 리렌더링이 되지않는다. 따라서 `useHistory` 를 사용하여 네비게이션 기능을 사용하는 것이 성능에 더 좋다.