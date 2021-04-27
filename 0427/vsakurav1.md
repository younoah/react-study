### Routing

웹에서 통상적으로 말하는 routing 이란 client가 url을 요청했을 때 어떤 특정 페이지로 연결할 건지 결정하는 메커니즘이다.

_e.g_

- `https://velog.io/`
- `https://velog.io/recent`
- `https://velog.io/@velog`

### SPA

> 한 번 페이지가 로딩되고 나면 부분적인 내용만 업데이트하면서 사용자가 필요한 부분적인 내용만 업데이트되는 애플리케이션이다.

**SPA의 문제점 💥**

- 바뀌는 화면은 실제로는 동일한 url이기 때문에 북마크를할 수가가 없다.

-뒤로 가기, 앞으로 가기 등 브라우저의 내비게이션에 추가가 되지 않는다.

### react-router

React에서 Routing을 사용할 수 있게 도와주는 library이다.

e.g) 기본적인 라우팅 사용

```jsx
import { BrowserRouter as Router, Link, Route, Switch } from 'react-router-dom';
...

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li>
            <Link to="/">HOME</Link>
          </li>
          <li>
            <Link to="/profile">Profile</Link>
          </li>
        </ul>
      </nav>
      <Switch>
        <Route path="/" exact>
          <Home />
        </Route>
        <Route path="/profile">
          <Profile />
        </Route>
      </Switch>
    </Router>
  );
}
```

라우팅을 웹에서 사용하기 위해서는 BrowserRouter로 해당 컴포넌트들을 감싸준 후에 Link나 Switch 등을 이용하여 url을 변경하고, 그에 따른 component를 렌더링 해준다.

**❕❕ Switch 사용 시 주의점**

Switch는 조건문처럼 속해 있는 Route들을 돌면서 path가 조건에 부합하면 그 component를 렌더링 한다.

이때, `path="/"`일 경우 꼭 `exact`를 선언해 주어야 한다.
그렇지 않으면 모든 조건에 "/" 가 걸리기 때문에 계속해서 메인 페이지가 렌더링 된다.

🌈 새롭게 알게 된 점

```jsx
<Route path="profile" component={Profile}>
```

- 기존에 사용할 때는 이런 식으로 인라인으로 사용했었는데 이제는 Hook에 있는 useHistory 등을 이용해서 훨씬 더 간편하게 사용할 수 있다.
  (기존의 방법은 쓸데없는 컴포넌트의 연산이 일어나고, 화면이 깜빡일 수도 있다고 한다.)

✅ 추후에 생각할 점
`<Link>` 뿐만 아니라 `<NavLink>` 등도 잘 활용해 보면 좋을 것 같다.
