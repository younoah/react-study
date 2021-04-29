### Add Firebase

✅ `yarn add firebase`

#### service > firebase.js

```js
import firebase from 'firebase';

const firebaseConfig = {
  apiKey: process.env.REACT_APP_FIREBASE_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  databaseURL: process.env.REACT_APP_FIREBASE_DB_URL,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
};
// Initialize Firebase
const firebaseApp = firebase.initializeApp(firebaseConfig);

export default firebaseApp;
```

> import 한 firebase에 정보를 담아서 초기화해준다.

#### service > auth_service.js

```js
import firebase from 'firebase';
import firebaseApp from './firebase';

class AuthService {
  login(providerName) {
    const authProvider = new firebase.auth[`${providerName}AuthProvider`]();
    return firebaseApp.auth().signInWithPopup(authProvider);
  }

  logout() {
    firebaseApp.auth().signOut();
  }

  onAuthChange(onUserChanged) {
    firebaseApp.auth().onAuthStateChanged(user => onUserChanged(user));
  }
}

export default AuthService;
```

> firebase와 firebaseApp을 import 해서 각각의 method를 구현해 준다.

**참조 [Firebase Auth](https://firebase.google.com/docs/auth)**

#### [filename].module.css

common 에 정의 해 놓은 변수들을

_@value '변수명' from 'import 경로'_

#### Routing

```jsx
const history = useHistory();

const goToMaker = userId => {
  history.push({
    pathname: '/maker',
    state: { id: userId },
  });
};
```

_`history.push()`를 이용해 추가적인 정보를 넘길 때는 객체 형태로, state를 이용해서 데이터를 전달해야 한다._

**공식 문서만 잘 활용해도 구글링으로 구현 가능할 것 같지만 공식 문서를 참조해서 프로젝트 진행한 경험이 많이 부족해서 이런 부분은 좀 더 많은 연습이 필요할 것 같다.**

**그리고 크롬의 한글 번역으로는 오역되는 부분이 많아서 이해하기가 더 힘들었다...😭😭😭**
