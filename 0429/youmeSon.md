## 10.1 로그인 화면 


   ### ❗ 1. Firebase 설치하기 

  1. cmd 에다가 yarn add firebase하기 

  2. firebase.js 폴더 만들어서 SDK 넣기 

  3. API Keys 깃헙에 올리면 안 되므로 .env 만들어서 gitignore에도 업데이트 

    ```jsx
    const firebaseConfig = {
      apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
      authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
      databaseURL: process.env.REACT_APP_FIREBASE_DB_URL,
      projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
    };
    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    ```

   firebase 를 이용해야 하기 때문에 , 밑에 처럼 수정 

    ```jsx
    import firebase from "firebase"; // import! 
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

   4. auth_service.js라는 파일을 만들어서 AuthService 클래스 만들기 

   : 이 클래스에서 provider(구글, 깃헙)에 따라 로그인 가능하게 할 것임 

    ```jsx
    import firebase from "firebase";
    import firebaseApp from "./firebase";

    class AuthService {
      login(providerName) {
        const authProvider = new firebase.auth[`${providerName}AuthProvider`]();
        return firebaseApp.auth().signInWithPopup(authProvider);
      }
    }

    export default AuthService;
    ```
   * .auth안에 있는 함수를 선택해야 하므로 (.) 아니면 [ ]를 선택할 수 있다. 우리는 백틱 키를 써야하므로 [ ] 를 써서 이용 

   5.이제 버튼이 클릭됐을때 로그인 되게 만들기 → auth 서비스 필요 

   # 그러므로 바로 **Dependency injection**이 필요!!! 
   > (youtube clone강의에도 나옴)


   index.js에서부터 시작! 

    ```jsx
    import React from "react";
    import ReactDOM from "react-dom";
    import "./index.module.css";
    import App from "./app";
    import AuthService from "./service/auth_service";

    const authService = new AuthService(); 
    ReactDOM.render(
      <React.StrictMode>
        <App authService={authService} />
      </React.StrictMode>,
      document.getElementById("root")
    );
    ```

   → 

    ```jsx
    import React from "react";
    import styles from "./app.module.css";
    import Login from "./components/login/login";

    function App({ authService }) {
      return (
        <div className={styles.app}>
          <Login authService={authService} />
        </div>
      );
    }

    export default App;
    ```
   * App component에 전달

   login.jsx로 가서 전달받은 prop이용 

    ```jsx
    import React from "react";
    import Footer from "../footer/footer";
    import Header from "../header/header";
    import styles from "./login.module.css";

    const Login = ({ authService }) => {
      const onLogin = (event) => {
        authService //
          .login(event.currentTarget.textContent)
          .then(console.log);
      };
      return (
        <section className={styles.loginContainer}>
          <Header />
          <section className={styles.login}>
            <h1 className={styles.title}>Login</h1>
            <ul>
              <li className={styles.list}>
                <button className={styles.button} onClick={onLogin}>
                  Google
                </button>
              </li>
              <li className={styles.list}>
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
   * login함수 안에는 provider가 들어가야 함 
   * textcontent는 currentTarget이 있다면, 일반 DOM요소 이기 때문에, DOM 요소 안의 textContent 데이터를 읽어 올 수 있음.

  ## header && footer

    ```jsx
    import React from "react";
    import styles from "./header.module.css";

    const Header = ({ onLogout }) => {
      return (
        <header className={styles.header}>
          {onLogout && ( // props로 받은 onLogout이 있다면 button을 보여주고, 클릭되면 전달받은 onLogout함수 실행해줘!
            <button className={styles.logout} onClick={onLogout}>
              Logout
            </button>
          )}
          <img className={styles.logo} src="./images/logo.png" alt="logo" />
          <h1 className={styles.title}>Business Card Maker</h1>
        </header>
      );
    };

    export default Header;
    ```
    
   ## 10.5 Router로 화면 전환 
  
1. yarn add react-router-dom
2. app.jsx 에서 browser router 추가 

    ```jsx
    import React from "react";
    import { BrowserRouter, Route, Switch } from "react-router-dom";
    import styles from "./app.module.css";
    import Login from "./components/login/login";
    import Maker from "./components/maker/maker";

    function App({ authService }) {
      return (
        <div className={styles.app}>
          <BrowserRouter>
            <Switch>
              <Route exact path="/">
                <Login authService={authService} />
              </Route>
            </Switch>
            <Switch>
              <Route path="/maker">
                <Maker />
              </Route>
            </Switch>
          </BrowserRouter>
        </div>
      );
    }

    export default App;
    ```

3. 이제 로그인하면 maker화면으로 넘어가는 것을 구현 

    로그인이 되면 → goToMaker라는 함수 나오게 하기 

    (화면에서 다른 라우터로 갈때는 history 이용)

    ```jsx
    const Login = ({ authService }) => {
      const history = useHistory();
      const goToMaker = () => {
        history.push("/maker");
      };
    ```

4. 이때 그냥 가는 것이 아닌 사용자가 로그인을 했다면 사용자의 정보도 함께 전달해야 함 

    (그 정보를 이용해서 사용자의 비즈니스 카드를 데이터베이스에 나중에 저장 가능)

5. 추가적인 정보를 함께 전달해야 함 

    ```jsx
    const Login = ({ authService }) => {
      const history = useHistory();
      const goToMaker = (userId) => {
        history.push({
          pathname: "/maker",
          state: { id: userId },
        });
      };
    ```

6. 로그인이 되면 이제 데이터가 받아지고 goToMaker를 호출 

    ```jsx
    const onLogin = (event) => {
        authService //
          .login(event.currentTarget.textContent)
          .then((data) => goToMaker(data.user.uid));
      };
    ```
    * 여기서 data는 원래 .then(console.log)라고 쳤을때 나오던 Dom의 정보 

7. 이제 눌러보면 로그인이 되자마자 정보를 받아서 maker로 가는 것을 확인할 수 있다. 
8. 로그인된 정보가 있다면 자동으로 로그인되게 만들기 
  
    - onAuthStateChanged는 API에 콜백함수를 등록해 놓으면 사용자의 로그인 상태가 바뀔 때마다 우리의 콜백함수가 호출

9. login이라는 컴포넌트에 useEffect()사용하기 
    - useEffect를 사용해서 컴포넌트가 마운트가 되거나 업데이트 될때 onAuthStateChanged사용
    
10. Service 레이어로 다 분리했으므로 auth_service.js로 가서 해보자 
    - auth_service.js에서 로그인을 담당한 클래스 안에서 추가

    ```jsx
    import firebase from "firebase";
    import firebaseApp from "./firebase";

    class AuthService {
      login(providerName) {
        const authProvider = new firebase.auth[`${providerName}AuthProvider`]();
        return firebaseApp.auth().signInWithPopup(authProvider);
      }

      onAuthChange(onUserChanged) {
        firebase.auth().onAuthStateChanged((user) => {
          onUserChanged(user); //여기 onUserChange는 콜백함수! 
        });
      }
    }

    export default AuthService;
    ```

11. 다시 login.jsx 컴포넌트로 가서 useEffect에다 사용하기 

    ```jsx
    useEffect(() => {
        authService //
          .onAuthChange((user) => {
            user && goToMaker(user.uid);
          });
      });
    ```

12. 이제 Maker에서 header와 Footer 그리고 로그아웃 추가하기 

    ```jsx
    const Maker = ({ authService }) => {
      return (
        <section className={styles.maker}>
          <Header onLogout={onLogout} />
          <Footer />
        </section>
      );
    };
    ```

13. 로그아웃 처리해주기 

    ```jsx
    const Maker = ({ authService }) => {
      const onLogout = () => {
        authService.logout();
      };

      return (
        <section className={styles.maker}>
          <Header onLogout={onLogout} />
          <Footer />
        </section>
      );
    };
    ```
    * 정말 간단하게 authService라는 props를 받아서 logout( ) 붙이면 된다. 

14. 아직 logout( )을 안 만들었으니 이제 다시 app.jsx에 가서 maker에다가 props 전달 

    ```jsx
    import React from "react";
    import { BrowserRouter, Route, Switch } from "react-router-dom";
    import styles from "./app.module.css";
    import Login from "./components/login/login";
    import Maker from "./components/maker/maker";

    function App({ authService }) {
      return (
        <div className={styles.app}>
          <BrowserRouter>
            <Switch>
              <Route exact path="/">
                <Login authService={authService} />
              </Route>
            </Switch>
            <Switch>
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

15. 이제 다시 auth_service.js에 가서 logout 하는 함수 구현하기

    ```jsx
    logout() {
    	firebaseApp.auth().signOut();
    }
    ```

16. 로그아웃을 클릭했을 때 , 홈으로 가는 것 구현 
    - Maker 컴포넌트로 가서 추가하는데 수동적으로 로그아웃될 때 가게 하는 것보다 , 사용자의 auth state가 변경이 되면 이동하게 하기
    - useEffect 사용

    ```jsx
    import React, { useEffect } from "react";
    import { useHistory } from "react-router";
    import Footer from "../footer/footer";
    import Header from "../header/header";
    import styles from "./maker.module.css";

    const Maker = ({ authService }) => {
      const history = useHistory();
      const onLogout = () => {
        authService.logout();
      };

      useEffect(() => {
        authService.onAuthChange((user) => {
          if (!user) {
            history.push("/");
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
   
