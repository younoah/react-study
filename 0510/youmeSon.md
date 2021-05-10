## 10.20~ 10.21 Firebase realtime database set, remove

1. UI

✨ flex 아이템의 사이즈는 기본적으로 안의 컨텐츠 사이즈보다 작아질 수 없다!

✅ Flex box defaulting setting의 min-width와 min-height가 auto이기 때문임!

[Why don't flex items shrink past content size?](https://stackoverflow.com/questions/36247140/why-dont-flex-items-shrink-past-content-size)

그래서 min-height:0 또는 overflow:hidden을 이용해서 해결해 주면 좋다!

2. Realtime 

    1) Go to console 

    2) Realtime Database (service 폴더에서 새로 파일 만들기)

3. Maker.jsx 

    1. state에 넣어 놓은 test 정보 지우기 
    2. 사용자의 id를 이용해서 카드들이 사용자 id 별로 저장될 수 있도록 해야 함 → 사용자 id를 통해 카드를 불러오기 위함

    ```jsx
    const Maker = ({ FileInput, authService }) => {
	const history = useHistory(); // 밑에 것 가져오기
    const historyState = history?.location?.stata
    const [cards, setCards] = useState({});
    const [userId, setUserId] = useState(historyState && historyState.id);
    ```
    - history 안에 있는 location 안에 state가 들어 있음

    3. 로그인에 관련된 정보가 변경되면 콜백함수가 호출되므로 , 사용자가 변경될때 마다 setUserId 호출해주면 됨 

    ```jsx
    useEffect(() => {
        authService.onAuthChange((user) => {
          if (user) {
            setUserId(user.uid);
          } else {
            history.push("/"); // user id가 없다면~ 
          }
        });
      });
    ```

4. cardRepository 전달 (거꾸러 흘러 가기)
    - maker.jsx
    - app.jsx
    - index.js
5. maker.jsx에서 사용해보기

    ```jsx
    const createOrUpdateCard = (card) => {
        setCards((cards) => {
          const updated = { ...cards };
          updated[card.id] = card;
          return updated;
        });
        cardRepository.saveCard(userId, card); // 만들어지거나 변경되면 세이브해줘!
      };
    ```

6.  이제 card_repository.js에 가서 saveCard라는 함수 만들기 

  1. import firebaseApp from './firebase' // 같은 폴더에 있는 ~~
  2.  database().ref(경로 지정(우리가 지정하기에 나름) ).set(card);

```jsx
saveCard(userId, card) {
    firebaseApp.database().ref(`${userId}/cards/${card.id}`).set(card); //사용자 id 안에 있는 cards의 안에 있는 id 저장~!
  }
```

7. delete 

    1. remove() 만 넣으면 됌 

  💥 rmove( ) 안에 카드 넣지 말기! 

  ```jsx
removeCard(userId, card) {
    firebaseApp.database().ref(`${userId}/cards/${card.id}`).remove(); //사용자 id 안에 있는 cards의 안에 있는 id 저장~!
  }
  ```

## 10.22 Firebase relatime databse sync
1. syncCards 함수 만들기

    ```jsx
    syncCards(userId, onUpdate) {
        const ref = firebaseApp.database().ref(`${userId}/cards`);
        ref.on("value", (snapshot) => {  //snapshot이라는 이 데이터가 인자로 들어옴 
          const value = snapshot.val();
          value && onUpdate(value); // -> value를 받아서 콜백함수 호출 
        });
        return () => ref.off();
      }
    ```

- syncCards 함수에 sync를 끊을 수 있는 함수를 return 하기!

2. useEffect 사용 

- useEffect의 장점 중 하나! : 해당하는 로직 별로 여러 개를 만들 수 있다

```jsx
useEffect(() => {
    if (!userId) {
      return;
    }
    cardRepository.syncCards(userId, (cards) => {
      setCards(cards);
    });
    
  }, [userId]);
```

🎃 재밌는 점 

- useEffect에서 특정 함수를 리턴하게 되면, 리액트가 알아서 컴포넌트가 unmount됐을 때 우리가 리턴한 함수를 호출 해 준다! (리소스 , 메모리 정리에 좋음)

```jsx
useEffect(() => {
    if (!userId) {
      return;
    }
    const stopSync = cardRepository.syncCards(userId, (cards) => {
      setCards(cards);
    });
    return () => stopSync();
  }, [userId]);
```

여기서 stopSync라는 함수는 card_repository.js에서 return () ⇒ ref.off( ) 
