#### firebase.js

```js
import firebase from 'firebase';

const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  databaseURL: process.env.REACT_APP_FIREBASE_DB_URL,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
};

// Initialize Firebase
const firebaseApp = firebase.initializeApp(firebaseConfig);
export default firebaseApp;
```

`firebase` 는 개발에 필요한 다양한 환경도구를 제공

`firebaseApp` 으로 본격적으로 firebase가 제공하는 API를 사용할 수 있다.



#### auth_service.js

```js
import firebase from 'firebase';
import firebaseApp from './firebase';

class AuthService {
  login(providerName) {
    const authProvider = new firebase.auth[`${providerName}AuthProvider`]();
    return firebaseApp.auth().signInWithPopup(authProvider);
  }

  logout() {
    firebase.auth().signOut();
  }

  onAuthChange(onUserChanged) {
    firebase.auth().onAuthStateChanged(user => {
      onUserChanged(user);
    });
  }
}

export default AuthService;

```

`firebaseApp.auth().signInWithPopup(authProvider)` 의 반환값은 `user` 라는 객체를 반환받는다. `user` 객체안에 uid 등의 유저의 로그인 정보를 담고 있다.

`onAuthChange()` 메서드는 유저의 정보가 바뀌었을 때 .... 



#### index.js

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.module.css';
import App from './app';
import AuthService from './service/auth_service';

const authService = new AuthService();
ReactDOM.render(
  <React.StrictMode>
    <App authService={authService} />
  </React.StrictMode>,
  document.getElementById('root')
);

```



#### app.jsx

```jsx
import React from 'react';
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import styles from './app.module.css';
import Login from './components/login/login';
import Maker from './components/maker/maker';

function App({ authService }) {
  return (
    <div className={styles.app}>
      <BrowserRouter>
        <Switch>
          <Route exact path="/">
            <Login authService={authService} />
          </Route>
          <Route path="/maker">
            <Maker authService={authService} />
          </Route>
        </Switch>
      </BrowserRouter>
    </div>
  );
}

export default App;

```

리엑트라우터로 경로 분기처리, `login` 과 `Maker`  에 `authService`객체를 props로 전달



#### login.jsx

```jsx
import React, { useEffect } from 'react';
import { useHistory } from 'react-router-dom';
import Footer from '../footer/footer';
import Header from '../header/header';
import styles from './login.module.css';

const Login = ({ authService }) => {
  const history = useHistory();
  const goToMaker = userId => {
    history.push({
      pathname: '/maker',
      state: { id: userId },
    });
  };

  const onLogin = event => {
    authService //
      .login(event.currentTarget.textContent)
      .then(data => goToMaker(data.user.uid));
  };

  useEffect(() => {
    authService.onAuthChange(user => {
      user && goToMaker(user.id);
    });
  });

  return (
    <section className={styles.login}>
      <Header />
      <section>
        <h1>Login</h1>
        <ul className={styles.list}>
          <li className={styles.item}>
            <button className={styles.button} onClick={onLogin}>
              Google
            </button>
          </li>
          <li className={styles.item}>
            <button className={styles.button} onClick={onLogin}>
              Github
            </button>
          </li>
        </ul>
      </section>
      <Footer />
    </section>
  );
};

export default Login;

```

- `goToMaker() `: `userId` 를 가지고 `maker` 로 이동
- `onLogin()` : 선택한 버튼(텍스트)에 따라 로그인, `user` 객체 정보로 maker로 이동
- `useEffect` : 컴포넌트의 변화가 있을 때마다 유저정보가 바뀌었는지 확인, 유저정보가 있다면 maker로 이동. (로그인이 되었을 때 로그인 페이지 접근을 차단하기 위함.)



#### maker.jsx

```jsx
import React, { useEffect } from 'react';
import { useHistory } from 'react-router-dom';
import Footer from '../footer/footer';
import Header from '../header/header';
import styles from './maker.module.css';

const Maker = ({ authService }) => {
  const history = useHistory();
  const onLogout = () => {
    authService.logout();
  };

  useEffect(() => {
    authService.onAuthChange(user => {
      if (!user) {
        history.push('/');
      }
    });
  });
  return (
    <section className={styles.maker}>
      <Header onLogout={onLogout} />
      <Footer />
    </section>
  );
};

export default Maker;

```

- `useEffect` : 컴포넌트가 변할때마다 로그인정보가 바뀌었는지 확인, 만약 유저정보가 없다면 로그아웃 된 경우이므로 로그인화면(`/`) 으로 이동한다.