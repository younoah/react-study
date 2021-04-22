**✨ ico tip**

[ConvertICO](https://convertico.com/)

이 사이트에서 png를 drag 하면 변환된 ico를 받을 수 있다.

#### 👁‍🗨 search

강의에서는 search form에서 버튼을 클릭했을 때와 Enter key를 눌렀을 때 search 동작을 하게 구현하였다.
개인적인 생각으로는 form 태그를 사용해서 submit을 제어하는 게 웹 표준 측면에서 더 맞지 않나라는 생각이 들어서 아래와 같이 코드를 변경하였다.

_개인적인 생각이고, 실제로 SPA 웹앱에서는 form태그를 사용하지 않는 서비스도 많은것으로 알고있다._

```jsx
function SearchHeader({ search }) {
  const [text, setText] = useState('');

  const onChange = e => {
    setText(e.target.value);
  };

  const onSubmit = e => {
    e.preventDefault();
    if (!text) {
      alert('검색어가 올바르지 않습니다.');
      return;
    }
    search(text);
    setText('');
  };
  return (
    <header className={styles.header}>
      <div className={styles.logo}>
        <img className={styles.img} src="/images/logo.png" alt="logo" />
        <h1 className={styles.title}>Youtube</h1>
      </div>
      <form onSubmit={onSubmit} className={styles.form}>
        <input
          type="text"
          value={text}
          onChange={onChange}
          placeholder="Search.."
          className={styles.input}
        />
        <button type="submit" className={styles.button}>
          <img src="/images/search.png" alt="search" />
        </button>
      </form>
    </header>
  );
}
```

### 오늘의 핵심 ✔

- 사이드 프로젝트 주의할 점

  > API Key 등은 외부에 노출되면 안 되기 때문에 코드 안에서 직접 기입하는 행위는 좋지 않다.

- 컴포넌트는 최대한 멍청하게 ❕❔
  > 컴포넌트는 view만 담당하는 것이 좋기 때문에 필요한 로직들은 따로 분리를 하고, DI를 통해서 TDD 개발에서도 활용할 수 있다.

#### service ( network 통신 )

service 폴더를 별도로 생성하여 그 안에서 필요한 network 로직들을 처리해 준다.

이때는 JSX를 사용하지 않기 때문에 순수한 JS 파일로 생성하여 index.js에 연결해 준다.
index는 최초 한번 실행되기 때문에 반복되는 생성을 피할 수 있다.

#### ENV

- gitignore에 정보가 담겨있는 env 파일을 설정해서 git 관리에서 제외될 수 있게 설정해 주어야 한다
- CRA로 만든 프로젝트는 정해진 규칙에 따라 _REACT_APP_\_변수명 과 같은 컨벤션을 지켜줘야 한다.

---

##### 힘들었던 부분💦

오늘은 익숙했던 react의 기본 패턴에서 조금 더 나아가서 사이드 프로젝트를 하는 방법을 배운 것 같다

```jsx
const search = query => {
  ...
    .then(response => response.json())
    .then(result =>
      setVideos(result.items.map(item => ({ ...item, id: item.id.videoId }))),
    )
    ...
  };
```

**item의 키값이 객체 형태로 받아온 경우에서 videoId를 꺼내는 작업을 생각하는 발상 자체가 쉽지 않았다**
