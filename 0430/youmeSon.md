## 10.6~10.7 Maker header and footer 

신경 써야 할 부분 

1. Container 부분을 가득 채우기 위해 **flex에 1** 주기 
2. mediaQuery도 이미 정해놓은 @ value에서 가져오기 
3. justify-content를 이용해서 space between을 주지 말고, 선생님처럼 flex-basis를 이용하여 반반 나누기

## 10.8~10.9 Maker layout 

✅1. card라는 오브젝트 만들기 → 각각의 카드가 들어있는 배열의 오브젝트 

✅2. state를 만들어서 editor와 preview로 전달 

✅3. card editor form과 preview 만들기

먼저 cards라는 오브젝트, state로 수동적으로 만들기 

1. Functional component이므로 cards라는 변수와 setCards라는 업데이트하는 것을 만든 후, useState이용 

    ```jsx
    const Maker = ({ authService }) => {
      const [cards, setCards] = useState();
    }
    ```

2. 먼저 수동적으로 스테이트를 만들기 

    ```jsx
    const [cards, setCards] = useState([
        {
          id: "1",
          name: "Youme",
          company: "Good Company",
          theme: "light",
          title: "Frontend Developer",
          email: "rosydarling@naver.com",
          message: "Trust the process",
          fileName: "youme",
          fileURL: "youme.png",
        },
        {
          id: "2",
          name: "Bandal",
          company: "Nimbus",
          theme: "dark",
          title: "Software Developer",
          email: "rosydarling@naver.com",
          message: "Youme is the best",
          fileName: "bandal",
          fileURL: "bandal.png",
        },
        {
          id: "3",
          name: "Laura",
          company: "Filates",
          theme: "light",
          title: "Filates Instructor",
          email: "laura12@naver.com",
          message: "Go for it",
          fileName: "laura",
          fileURL: "laura.png",
        },
      ]);
    ```

3. 이제 `Edior`와 `Preview`에 전달하기 

    ```jsx
    return (
        <section className={styles.maker}>
          <Header onLogout={onLogout} />
          <div className={styles.container}>
            <Editor cards={cards} />
            <Preview cards={cards} />
          </div>
          <Footer />
        </section>
      );
    ```

4. cards.jsx에 가서 받은 props인 cards로 card 넣기

    ```jsx
    const Editor = ({ cards }) => {
      return (
        <section className={styles.editor}>
          <h1 className={styles.title}>Card Maker</h1>
          {cards.map((card) => (
            <Card card={card} />
          ))}
        </section>
      );
    };
    ```
    * 받은 cards는 배열이므로 map으로 하나씩 돌리기 
    ** { }를 써서 <Card card={card} />를 넣어주면 return을 해야하므로 ( )안에 넣어주는 것 주의! 

5. 이제 editor부분도 하기 
    1. card_edit_form 폴더 만들기 (나는 table이라고 만들었었음)
    2. 나머지는 위와 똑같음

## 10.10 Maker Card UI

1. Deconstructing 

```jsx
const {
    name,
    company,
    title,
    email,
    message,
    theme,
    fileName,
    fileURL,
  } = card;
```

* 매번 card.name, card.company 하는 것보다 낫다. 

2. preview.js 에서는 ul 로 코드 작성 수정하기 (카드가 나열되는 게 list이니까)

```jsx
			<ul>
        {cards.map((card) => (
          <Card card={card} />
        ))}
      </ul>
```

3. card.jsx에서 이미지가 null일 경우, default 이미지 쓰기 

```jsx
import React from "react";
import styles from "./card.module.css";

const DEFAULT_IMAGE = "/images/default_logo.png" //의미있는 이름 지정 

const Card = ({ card }) => {
  const {
    name,
    company,
    title,
    email,
    message,
    theme,
    fileName,
    fileURL,
  } = card;
  const url = fileURL || DEFAULT_IMAGE;   // 
  return (
    <li className={styles.card}>
      <img className={styles.image} src={url} alt="logo" />
      <section className={styles.information}>
        <h2 className={styles.name}>{card.name}</h2>
        <span className={styles.company}>{card.company}</span>
        <div className={styles.line}></div>
        <span className={styles.title}>{card.title}</span>
        <span className={styles.email}>{card.email}</span>
        <span className={styles.message}>{card.message}</span>
      </section>
    </li>
  );
};

export default Card;
```

4. theme에 따라 색상 변경 

  1. card 함수 내에 들어갈 필요없음
  2. 따로 함수 만들어주기 

```jsx
import React from "react";
import styles from "./card.module.css";

const DEFAULT_IMAGE = "/images/default_logo.png";

const Card = ({ card }) => {
  const {
    name,
    company,
    title,
    email,
    message,
    theme,
    fileName,
    fileURL,
  } = card;
  const url = fileURL || DEFAULT_IMAGE;
  return (
    <li className={styles.card}>
      <img className={styles.avatar} src={url} alt="profile photo" />
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
};

function getStyles(theme) {
  switch (theme) {
    case "dark":
      return styles.dark;
    case "light":
      return styles.light;
    case "colorful":
      return styles.colorful;
    default:
      throw new Error(`unknow theme: ${theme}`);
  }
}

export default Card;
```

3. card.jsx li에 className 두개 지정 

```jsx
return (
    <li className={`${styles.card} ${getStyles(theme)}`}>
      <img className={styles.avatar} src={url} alt="profile photo" />
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
```

4. css에서 디자인 할 경우 

```css
.card.dark {
  background-color: makerBlack;
  color: makerWhite;
}

.card.light {
  background-color: makerWhite;
  color: makerBlack;
}

.card.colorful {
  background: makerColorful;
}
```

** 엘리 님이랑 조금 다르게 한 부분 

→ 혼자했을 때 information의 display를 flex, align-items: start로 해주지 않으면 글자들이 모두 가운데로 정렬됨. 

→ 이렇게 했을 경우 company::after의 선이 90%로 나오지 않고, 회사 글씨 크기 만큼만 지정 

→ card.jsx에서 div로 빈 칸 하나 만들어주고 따로 디자인 

```css
.information {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: start;
}

.name,
.company,
.title,
.email,
.message {
  margin: 0;
  font-size: 0.8rem;
  margin-bottom: 0.2em;
}

.name {
  font-size: 1.2rem;
}

.line {
  content: "";
  display: block;
  width: 90%;
  height: 2px;
  margin: 0.5em 0;
  background-color: makerWheat;
}
```
