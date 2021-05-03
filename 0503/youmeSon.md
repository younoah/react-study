## 10.11 Maker EditForm

👀 table로 생각했으나, 입력해야 하는 form과 input이 들어가므로 주의! 

1. input 넣기

```jsx
import React from "react";
import styles from "./card_edit_form.module.css";

const CardEditForm = ({ card }) => {
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
  return (
    <form>
      <input type="text" name="name" value={name} />
      <input type="text" name="company" value={company} />
      <input type="text" name="title" value={title} />
      <input type="text" name="email" value={email} />
    </form>
  );
};

export default CardEditForm;
```
- text로 입력하는 것은 4가지

2. select로 theme 넣기 

- select는 태그 안에 옵션을 넣어줘야 한다.

```jsx
import React from "react";
import styles from "./card_edit_form.module.css";

const CardEditForm = ({ card }) => {
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
  return (
    <form>
      <input type="text" name="name" value={name} />
      <input type="text" name="company" value={company} />
      <select name="theme" value={theme}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
        <option value="colorful">Colorful</option>
      </select>
      <input type="text" name="title" value={title} />
      <input type="text" name="email" value={email} />
    </form>
  );
};

export default CardEditForm;
```

3. textarea로 message 부분 넣어주기 

```jsx
return (
    <form>
      <input type="text" name="name" value={name} />
      <input type="text" name="company" value={company} />
      <select name="theme" value={theme}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
        <option value="colorful">Colorful</option>
      </select>
      <input type="text" name="title" value={title} />
      <input type="text" name="email" value={email} />
      <textarea name="message" value={message}></textarea>
    </form>
  );
```

4. Button 추가 → 자주 쓸 수 있으므로 → 컴포넌트 작성 

```jsx
import React from "react";
import styles from "./button.module.css";

const Button = ({ name, onClick }) => (
  <button className={styles.button} onClick={onClick}>
    {name}
  </button>
);

export default Button;
```

5. card_editor_form.jsx에 추가 

```jsx
import React from "react";
import Button from "../button/button";
import styles from "./card_edit_form.module.css";

const CardEditForm = ({ card }) => {
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
  const onSubmit = () => {};
  return (
    <form>
      <input type="text" name="name" value={name} />
      <input type="text" name="company" value={company} />
      <select name="theme" value={theme}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
        <option value="colorful">Colorful</option>
      </select>
      <input type="text" name="title" value={title} />
      <input type="text" name="email" value={email} />
      <textarea name="message" value={message}></textarea>
      <Button name="Delete" onClick={onSubmit} />
    </form>
  );
};

export default CardEditForm;
```

6. imgage넣는 부분도 컴포넌트로 만들기 

7. 디자인 

- className 넣어주기

    ```jsx
    import React from "react";
    import Button from "../button/button";
    import ImageFileInput from "../image_file_input/image_file_input";
    import styles from "./card_edit_form.module.css";

    const CardEditForm = ({ card }) => {
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
      const onSubmit = () => {};
      return (
        <form className={styles.form}>
          <input className={styles.input} type="text" name="name" value={name} />
          <input
            className={styles.input}
            type="text"
            name="company"
            value={company}
          />
          <select className={styles.select} name="theme" value={theme}>
            <option value="light">Light</option>
            <option value="dark">Dark</option>
            <option value="colorful">Colorful</option>
          </select>
          <input className={styles.input} type="text" name="title" value={title} />
          <input className={styles.input} type="text" name="email" value={email} />
          <textarea
            className={styles.textarea}
            name="message"
            value={message}
          ></textarea>
          <div className={styles.fileInput}>
            <ImageFileInput />
          </div>
          <Button name="Delete" onClick={onSubmit} />
        </form>
      );
    };

    export default CardEditForm;
    ```

- card preview와 editor 사이에 선 → border-right 이용

1. form 디자인 

    ```css
    .form {
      display: flex;
      width: 100%;
      flex-wrap: wrap;
      border-top: 1px solid makerBlack;
      margin-bottom: 1em;
    }
    ```

2. input, textarea, select, button, fileInput 등 디자인

    ```css
    .input,
    .textarea,
    .select,
    .button,
    .fileInput {
      font-size: 0.8rem;
      width: 100%;
      border: 0;
      padding: 0.5em;
      border-bottom: 1px solid makerBlack;
      border-right: 1px solid makerBlack;
      border-left: 1px solid makerBlack;
      background: makerWhite;
    }
    ```

3. input이랑 select 부분 균등하게 나누기 

    ```css
    .input,
    .select {
      flex: 1 1 30%;
    }
    ```

    - 여기서 flex: 1 1 30%(grow, shrink, basis)

4. focus됐을 때 디자인

    [https://developer.mozilla.org/en-US/docs/Web/CSS/:focus](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus)

    The :focus CSS pseudo-class represents an element (such as a form input) that has received focus. It is generally triggered when the user clicks or taps on an element or selects it with the keyboard's Tab key.

    ```css
    .input:focus,
    .textarea:focus,
    .select:focus,
    .button:focus {
      outline: 0;
    }
    ```

5. textarea는 혼자 꽉차게 디자인

```css
.textarea {
  flex-basis: 100%;
}
```

6. button과 fileInput을 공평하게 반반 나누고 싶음

```css
.fileInput {
  padding: 0;
  flex: 1 1 50%;
}
```

## 10.12~ 10.13 Maker addForm 

---

1. card_add_form 컴포넌트 만들기 
2. editor.jsx에서 수정할 것 바꾸기 
    - 🌞 <CardEditForm>은 list이므로 key 넣어주기

    ```jsx
    import React from "react";
    import CardAddForm from "../card_add_form/card_add_form";
    import CardEditForm from "../card_edit_form/card_edit_form";

    import styles from "./editor.module.css";

    const Editor = ({ cards }) => (
      <section className={styles.editor}>
        <h1 className={styles.title}>Card Maker</h1>
        {cards.map((card) => (
          <CardEditForm key={card.id} card={card} />
        ))}
        
      </section>
    );

    export default Editor;
    ```

3. card_add_form.jsx 
    1. value 를 모두 placeholder로 교체
    2. card로 받아올 필요 없음 

    ```jsx
    import React, { useRef } from "react";
    import Button from "../button/button";
    import ImageFileInput from "../image_file_input/image_file_input";
    import styles from "./card_add_form.module.css";

    const CardAddForm = () => {
      
      const onSubmit = () => {};
      return (
        <form className={styles.form}>
          <input
            ref={nameRef}
            className={styles.input}
            type="text"
            name="name"
            placeholder="Name"
          />
          <input
            ref={companyRef}
            className={styles.input}
            type="text"
            name="company"
            placeholder="Company"
          />
          <select className={styles.select} name="theme" placeholder="Theme">
            <option placeholder="light">Light</option>
            <option placeholder="dark">Dark</option>
            <option placeholder="colorful">Colorful</option>
          </select>
          <input
            ref={titleRef}
            className={styles.input}
            type="text"
            name="title"
            placeholder="Title"
          />
          <input
            ref={emailRef}
            className={styles.input}
            type="text"
            name="email"
            placeholder="Email"
          />
          <textarea
            ref={textareaRef}
            className={styles.textarea}
            name="message"
            placeholder="Message"
          ></textarea>
          <div className={styles.fileInput}>
            <ImageFileInput />
          </div>
          <Button name="Add" onClick={onSubmit} />
        </form>
      );
    };

    export default CardAddForm;
    ```

    > alt 누른 상태에서 더블클릭해서 원하는 글자 전체 선택 + shift 누른 후 키보드로 이동 

4. useRef 이용 

    ```jsx
    import React, { useRef } from "react";
    import Button from "../button/button";
    import ImageFileInput from "../image_file_input/image_file_input";
    import styles from "./card_add_form.module.css";

    const CardAddForm = () => {
      const nameRef = useRef();
      const companyRef = useRef();
      const themeRef = useRef();
      const titleRef = useRef();
      const emailRef = useRef();
      const messageRef = useRef();
      const onSubmit = () => {};
      return (
        <form className={styles.form}>
          <input
            ref={nameRef}
            className={styles.input}
            type="text"
            name="name"
            placeholder="Name"
          />
          <input
            ref={companyRef}
            className={styles.input}
            type="text"
            name="company"
            placeholder="Company"
          />
          <select
            ref={themeRef}
            className={styles.select}
            name="theme"
            placeholder="Theme"
          >
            <option placeholder="light">Light</option>
            <option placeholder="dark">Dark</option>
            <option placeholder="colorful">Colorful</option>
          </select>
          <input
            ref={titleRef}
            className={styles.input}
            type="text"
            name="title"
            placeholder="Title"
          />
          <input
            ref={emailRef}
            className={styles.input}
            type="text"
            name="email"
            placeholder="Email"
          />
          <textarea
            ref={messageRef}
            className={styles.textarea}
            name="message"
            placeholder="Message"
          ></textarea>
          <div className={styles.fileInput}>
            <ImageFileInput />
          </div>
          <Button name="Add" onClick={onSubmit} />
        </form>
      );
    };

    export default CardAddForm;
    ```

5. onSumbit (add 눌렀을 때 ) 부분 
    1. 버튼이 클릭될때 마다 페이지 로딩 

        ```jsx
        const onSubmit = (event) => {
            event.preventDefault();
          };
        ```

    2. 새로운 카드를 오브젝트로 만들기 

        1) id 정해주기 

        ```jsx
        const onSubmit = (event) => {
            event.preventDefault();
            const card = {
              id: Date.now(), //
            }
          };
        ```

        2) 나머지 부분도 넣기 

        ```jsx
        const onSubmit = (event) => {
            event.preventDefault();
            const card = {
              id: Date.now(), //
              name: nameRef.current.value || "",
              company: companyRef.current.value || "",
              theme: themeRef.current.value || "",
              title: titleRef.current.value || "",
              email: emailRef.current.value || "",
              message: messageRef.current.value || "",
              fileName: "",
              fileURL: "",
            };
          };
        ```

        3) props로 받아온 onAdd부분도 추가 

        ```jsx
        const onSubmit = (event) => {
            event.preventDefault();
            const card = {
              id: Date.now(), //
              name: nameRef.current.value || "",
              company: companyRef.current.value || "",
              theme: themeRef.current.value || "",
              title: titleRef.current.value || "",
              email: emailRef.current.value || "",
              message: messageRef.current.value || "",
              fileName: "",
              fileURL: "",
            };
            onAdd(card);
          };
        ```

        4) formRef도 만들기 

        ```jsx
        const CardAddForm = ({ onSubmit }) => {
          const formRef = useRef();
          const nameRef = useRef();
          const companyRef = useRef();
          const themeRef = useRef();
          const titleRef = useRef();
          const emailRef = useRef();
          const messageRef = useRef();

          const onSubmit = (event) => {
            event.preventDefault();
            const card = {
              id: Date.now(), //
              name: nameRef.current.value || "",
              company: companyRef.current.value || "",
              theme: themeRef.current.value || "",
              title: titleRef.current.value || "",
              email: emailRef.current.value || "",
              message: messageRef.current.value || "",
              fileName: "",
              fileURL: "",
            };
            formRef.current.reset();
            onSubmit(card);
          };
        ```

    6. editor.jsx에 가서 onAdd라는 이름으로 받은 콜백함수 전달 

    ```jsx
    import React from "react";
    import CardAddForm from "../card_add_form/card_add_form";
    import CardEditForm from "../card_edit_form/card_edit_form";

    import styles from "./editor.module.css";

    const Editor = ({ cards, addCard }) => (
      <section className={styles.editor}>
        <h1 className={styles.title}>Card Maker</h1>
        {cards.map((card) => (
          <CardEditForm key={card.id} card={card} />
        ))}
        <CardAddForm onAdd={addCard}/>
      </section>
    );

    export default Editor;
    ```

    7. maker.jsx에 가서 addCard라는 함수 만들고 전달 

    ```jsx
    const addCard = (card) => {
        const updated = [...cards, card];
        setCards(updated);
      };
      return (
        <section className={styles.maker}>
          <Header onLogout={onLogout} />
          <div className={styles.container}>
            <Editor cards={cards} addCard={addCard} />
            <Preview cards={cards} />
          </div>
          <Footer />
        </section>
      );
    ```

    ❗❗ 주의 :  theme에서 옵션을 줄때 대소문자를 구분하므로 소문자로 다 바꾸기 

    ```jsx
    <select
            ref={themeRef}
            className={styles.select}
            name="theme"
            placeholder="Theme"
          >
            <option placeholder="light">light</option>
            <option placeholder="dark">dark</option>
            <option placeholder="colorful">colorful</option>
          </select>
    ```

    - 이때 card_editor_form.jsx와 card_add_form.jsx 둘 다 변경!

    ❗ 또한 theme option에서 적어도 하나는 선택해야 하므로 || 빼기  

    ```jsx
    const onSubmit = (event) => {
        event.preventDefault();
        const card = {
          id: Date.now(), //
          name: nameRef.current.value || "",
          company: companyRef.current.value || "",
          theme: themeRef.current.value,
          title: titleRef.current.value || "",
          email: emailRef.current.value || "",
          message: messageRef.current.value || "",
          fileName: "",
          fileURL: "",
        };
        formRef.current.reset();
        onAdd(card);
      };
    ```
    
    ## 1.14~10.15 state update
    
    1. card_edit_form.jsx에도 ref 연결해주기 

```jsx
const CardEditForm = ({ card }) => {
  const nameRef = useRef();
  const companyRef = useRef();
  const themeRef = useRef();
  const titleRef = useRef();
  const emailRef = useRef();
  const messageRef = useRef();
```

2. input(or 다른 section)에서 변경 사항이 생기면 onChange함수 호출하게 만들기! 

```jsx
<input
        ref={titleRef}
        className={styles.input}
        type="text"
        name="title"
        value={title}
        onChange={onChange}
      />
```

3. onChange라는 함수 만들기 

```jsx
const onChange = (event) => {
    
  }
```

4. props로 updateCard, deleteCard 받아오기 

```jsx
const CardEditForm = ({ card, **updateCard, deleteCard** }) => {
  const nameRef = useRef();
  const companyRef = useRef();
  const themeRef = useRef();
  const titleRef = useRef();
  const emailRef = useRef();
  const messageRef = useRef();
```

5. onChange 함수 

```jsx
const onChange = (event) => {
    if (event.currentTarget == null) {
      return;
    }
    event.preventDefault();
    updateCard( {
      ...card, [event.currentTarget.name]: event.currentTarget.value,
    })
  }
```

6. CardEditForm으로  props보낸 것 , 상위 컴포넌트에도 까먹지 않고 넣어주기 

1) editor.jsx

```jsx
import React from "react";
import CardAddForm from "../card_add_form/card_add_form";
import CardEditForm from "../card_edit_form/card_edit_form";

import styles from "./editor.module.css";

const Editor = ({ cards, addCard, **updateCard, deleteCard** }) => (
  <section className={styles.editor}>
    <h1 className={styles.title}>Card Maker</h1>
    {cards.map((card) => (
      <CardEditForm key={card.id} card={card} />
    ))}
    <CardAddForm onAdd={addCard} />
  </section>
);

export default Editor;
```

2) state가 있는 maker.jsx까지 ! 

```jsx
import React, { useEffect, useState } from "react";
import { useHistory } from "react-router";
import Footer from "../footer/footer";
import Header from "../header/header";
import styles from "./maker.module.css";
import Editor from "../editor/editor";
import Preview from "../preview/preview";

const Maker = ({ authService }) => {
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
      fileURL: null,
    },
    {
      id: "2",
      name: "Nick",
      company: "Nimbus",
      theme: "dark",
      title: "Software Developer",
      email: "rosydarling@naver.com",
      message: "Youme is the best",
      fileName: "nick",
      fileURL: null,
    },
    {
      id: "3",
      name: "Laura",
      company: "Filates",
      theme: "colorful",
      title: "Filates Instructor",
      email: "laura12@naver.com",
      message: "Go for it",
      fileName: "laura",
      fileURL: null,
    },
  ]);

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

  const addCard = (card) => {
    const updated = [...cards, card];
    setCards(updated);
  };
  return (
    <section className={styles.maker}>
      <Header onLogout={onLogout} />
      <div className={styles.container}>
        <Editor
          cards={cards}
          addCard={addCard}
          updateCard={updateCard}
          deleteCard={deleteCard}
        />
        <Preview cards={cards} />
      </div>
      <Footer />
    </section>
  );
};

export default Maker;
```

