문제점

1. firebase SDK → 필요한 것만 import 해오기 (auth, database)
2. initialize된 firebase앱을 이용했는데, 또 firebase를 import해온 것

해결하기 

- 첫 번째 문제 (firebase SDK → 필요한 것만 import 해오기 (auth, database))
1. initializeApp을 보면 firebase 안에 있는 .app 안에 있는 app이라는 interface를 리턴 받음 

    ```jsx
    import firebase from "firebase/app";
    import "firebase/auth";
    import "firebase/database";
    ```

- 두 번째 문제 (initialize된 firebase앱을 이용했는데, 또 firebase를 import해온 것)
    1. export default firebaseApp을 수정하기

    ```jsx
    import firebase from "firebase/app";
    import "firebase/auth";
    import "firebase/database";

    const firebaseConfig = {
      apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
      authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
      databaseURL: process.env.REACT_APP_FIREBASE_DB_URL,
      projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
    };
    // Initialize Firebase
    const firebaseApp = firebase.initializeApp(firebaseConfig);

    export const firebaseAuth = firebaseApp.auth();
    export const firebaseDatabase = firebaseApp.database();
    ```

    2. 이제 필요한 곳에서 export한 애들은 import해서 쓰기

    ✅ 먼저 card_repository.js

    ```jsx
    import { firebaseDatabase } from "./firebase";

    class CardRepository {
      syncCards(userId, onUpdate) {
        const ref = firebaseDatabase.ref(`${userId}/cards`);
        ref.on("value", (snapshot) => {
          const value = snapshot.val();
          value && onUpdate(value);
        });
        return () => ref.off();
      }
      saveCard(userId, card) {
        firebaseDatabase.ref(`${userId}/cards/${card.id}`).set(card); //사용자 id 안에 있는 cards의 안에 있는 id 저장~!
      }

      removeCard(userId, card) {
        firebaseDatabase.ref(`${userId}/cards/${card.id}`).remove(); //사용자 id 안에 있는 cards의 안에 있는 id 저장~!
      }
    }

    export default CardRepository;
    ```

    원하는 단어 더블클릭한 후에 ctrl + d 누르면 다 선택됨 

    ✅ auth_service.js

    1. 먼저 provide는 안 들어가 있으니 따로 firebase.js에서 export해주기

        ```jsx
        import firebase from "firebase/app";
        import "firebase/auth";
        import "firebase/database";

        const firebaseConfig = {
          apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
          authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
          databaseURL: process.env.REACT_APP_FIREBASE_DB_URL,
          projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
        };
        // Initialize Firebase
        const firebaseApp = firebase.initializeApp(firebaseConfig);

        export const firebaseAuth = firebaseApp.auth();
        export const firebaseDatabase = firebaseApp.database();
        export const googleProvider = new firebase.auth.GoogleAuthProvider();
        export const githubProvider = new firebase.auth.GithubAuthProvider();
        ```

    2. auth_service.js 에서 authProvider부분 수정 

        ```jsx
        class AuthService {
          login(providerName) {
            const authProvider = this.getProvider(providerName); //아직 getProvider함수 안 만듦
            return firebaseAuth.signInWithPopup(authProvider);
          }
        ```

    3. getProvider 함수 만듦 

        ```jsx
        getProvider(providerName) {
            switch (providerName) {
              case "Google":
                return googleProvider;
              case "Github":
                return githubProvider;
              default:
                throw new Error(`not supported provider: ${providerName}`);
            }
          }
        ```

        ❤ 또 다른 성능 개선 

        1. edit form의 이름만 수정해도 전체가 다 update 되고 있음 🤢

         * 카드의 오브젝트가 변화하지 않으면 업데이트 되지 않아도 됨! 

        🌞 memo 사용! 

        ```jsx
        const Card = memo(({ card }) => {
          const { name, company, title, email, message, theme, fileURL } = card;
          const url = fileURL || DEFAULT_IMAGE;
          return (
            <li className={`${styles.card} ${getStyles(theme)}`}>
              <img className={styles.avatar} src={url} alt="profile" />
              <div className={styles.information}>
                <h1 className={styles.name}>{name}</h1>
                <p className={styles.company}>{company}</p>
                <div className={styles.line}></div>
                <p className={styles.title}>{title}</p>
                <p className={styles.email}>{email}</p>
                <p className={styles.message}>{message}</p>
              </div>
            </li>
          );
        });
        ```

        e.g. card_add, card_edit, header, footer, file_input, button 

        2. onLogout 함수 

        onLogout은 Maker에서 지역변수로 만든 아이임 

        → 즉, re-render가 발생될 때마다 계속해서 새로운 함수가 만들어짐

        ✨ useCallback사용하기! - 함수 컴포넌트에서 함수가 계속 호출이 되어서 동일한 데이터를 쓸 수 있음 

        👺 주의 : authService가 변경이 되어도 한 번 저장된 authService를 사용하게 됨 → 해결 방법: authService가 변경되면 새로운 콜백을 만들 거야라고 지정해주기 

        ```jsx
        const onLogout = useCallback(() => {
            authService.logout();
          }, [authService]);
        ```

        - authService가 업데이트 되면 콜백 불러줘!
