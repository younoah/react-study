## CSS 스크롤

- 컨테이너 최소 높이 지정하기

```css
.container {
    ...
    min-height: 0;
    ...
}
```

플렉스 아이템의 사이즈는 기본적으로 컨텐츠 사이즈보다 작아질 수 없다.

컨테이너 안의 컨테츠가 커지면 그에 맞춰서 컨테이너의 높임도 커진다. 따라서 스크롤의 기능을 넣기위해서는 컨테이너의 높이를 고정시킬 필요가 있다.



- 수직 스크롤 

```css
.preview {
    ...
    overflow-y: auto;
    ...
}
```



## CRUD



- card_reopsitory.js

```js
import firebaseApp from './firebase';

class CardRepository {
  syncCards(userId, onUpdate) { // 데이터 리스닝, for 실시간 반영
    const ref = firebaseApp.database().ref(`${userId}/cards`);
    ref.on('value', snapshot => { // value가 변경시 콜백함수 호출, value는 예약어인듯!
      const value = snapshot.val(); // ref에 있는 card 데이터(객체)가 snapshot.val();담겨있다.
      value && onUpdate(value); // value가 정상적으로 받아와젔을 때 전달한 콜백함수를 실행
    });
    return () => ref.off(); 
   	// 포인트!, 리턴결과를 데이터 리스닝을 종료하는 함수를 뱉도록 하여
    // 언마운트시 리스닝을 종료하기 위함으로 사용
  }
  saveCard(userId, card) { // 데이터 생성 및 수정
    firebaseApp.database().ref(`${userId}/cards/${card.id}`).set(card);
  }

  removeCard(userId, card) { // 데이터 삭제
    firebaseApp.database().ref(`${userId}/cards/${card.id}`).remove();
  }
}

export default CardRepository;

```



### 생성/수정/읽기/삭제

```jsx
const Maker = ({ FileInput, authService, cardRepository }) => {
  const history = useHistory();
  const historyState = history?.location?.state; // 옵셔널 체이닝
  const [cards, setCards] = useState({});
  const [userId, setUserId] = useState(historyState && historyState.id);

  const history = useHistory();
  const onLogout = () => {
    authService.logout();
  };

  // 데이터베이스 리스너용
  useEffect(() => { // 포인트! useEffect는 중복되서 선언할수 있고, 기능별로 분리하여 가독성을 높일수 있다.
    if (!userId) {
      return;
    }
    const stopSync = cardRepository.syncCards(userId, cards => {
      setCards(cards);
    });
    return () => stopSync();
    // 리엑트에서는 언마운트시 useEffect의 return의 반환값인 함수를 호출해준다.
    // 클로저를 활용하여 데이터베이스 리스너를 종료한다.
  }, [userId]);
    
  // 유저 로그인 확인용
  useEffect(() => { 
    authService.onAuthChange(user => {
      if (user) {
        setUserId(user.uid);
        console.log(userId);
      } else {
        history.push('/');
      }
    });
  });

  const createOrUpdateCard = card => {
    setCards(cards => {
      const updated = { ...cards };
      updated[card.id] = card;
      return updated;
    });
    cardRepository.saveCard(userId, card); // 데이터베이스 생성 및 수정 요청
  };

  const deleteCard = card => {
    setCards(cards => {
      const updated = { ...cards };
      delete updated[card.id];
      return updated;
    });
    cardRepository.removeCard(userId, card); // 데이터베이스 삭제 요청
  };

  return (
    ...
  );
};

export default Maker;
```