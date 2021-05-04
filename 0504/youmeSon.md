## Upload the image
1. service폴더에 image_uploader.js파일 만들기

```jsx
class ImagaUploader {
  async upload(file) {
    return "file";
  }
}

export default ImagaUploader;
```

2. index 파일에 인스턴스 

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.module.css";
import App from "./app";
import AuthService from "./service/auth_service";
import ImagaUploader from "./service/image_uploader";
import ImageFileInput from "./components/image_file_input/image_file_input";

const authService = new AuthService();
const imageUploader = new ImagaUploader();
const FileInput = (props) => (
  <ImageFileInput {...props} imageUploader={imageUploader} />
);
ReactDOM.render(
  <React.StrictMode>
    <App authService={authService} />
  </React.StrictMode>,
  document.getElementById("root")
);
```

- 그리고 계속 props로 전달해야하는 상황에서는 위에서처럼 해도 된다!
- ImageFileInput을 한 단계 감싸는 컴포넌트로 사용자가 이 FileInput을 사용할 때 원하는 props를 전달하면 그 props를 그대로 전달해줄 것임

💥 주의

```jsx
const FileInput = <ImageFileInput imageUploader={imageUploader} />
```

- 만약에 fileInput을 위처럼 하면 확장성이 떨어짐

3. 이제 만들어 놓은 FileInput을 app 컴포넌트에 props로 전달! 

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.module.css";
import App from "./app";
import AuthService from "./service/auth_service";
import ImagaUploader from "./service/image_uploader";
import ImageFileInput from "./components/image_file_input/image_file_input";

const authService = new AuthService();
const imageUploader = new ImagaUploader();
const FileInput = (props) => (
  <ImageFileInput {...props} imageUploader={imageUploader} />
);
ReactDOM.render(
  <React.StrictMode>
    <App authService={authService} FileInput={FileInput}/>
  </React.StrictMode>,
  document.getElementById("root")
);
```

- 컴포넌트 props일 경우 대문자로 시작!

4. App.jsx에서도 maker로 전달 

```jsx
import React from "react";
import { BrowserRouter, Route, Switch } from "react-router-dom";
import styles from "./app.module.css";
import Login from "./components/login/login";
import Maker from "./components/maker/maker";

function App({ FileInput, authService }) {
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
            <Maker FileInput={FileInput} authService={authService} />
          </Route>
        </Switch>
      </BrowserRouter>
    </div>
  );
}

export default App;
```

5. maker에서 editor로!

6. editor에 가서 받아온 후 쓰기! 

7. card_edit_form

- ImageFileInput 컴포넌트 지우고 → FileInput으로 변경

8. add_form

✨ 이렇게 쓰는 것의 장점! 

✅ 쓸데없이 많은 서비스를 전달하지 않아도 된다. 

✅ forward-thinking!! FileInput에서 더 많은 서비스가 필요로 한다면 index.js에서만 수정하면 된다.

✅ 심플하게 dependency injection을 할 수 있다! 

### UI 파트

1. image_file_input.jsx에서 필요한 props 생각하기

✅ imageUploader 

✅ 이미지 이름

✅ 이미지 파일이 바꼈을 때 사용할 콜백함수 "onFileChange"

```jsx
import React from "react";
import styles from "./image_file_input.module.css";

const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  return <input type="file" accept="image/*" name="file" />;
};
export default ImageFileInput;
```

💥 실제로 html에 있는 input type="file" 을 꾸며주기는 힘듦 

→ 그러므로 누르는 button이라는 것을 만들어서 꾸며줄 것임~! 실제로 user가 누르게 될 것 ! 

```jsx
import React from "react";
import styles from "./image_file_input.module.css";

const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  return (
    <div className={styles.container}>
      <input
        className={styles.input}
        type="file"
        accept="image/*"
        name="file"
      />
      <button>{name || "No file"}</button>
    </div>
  );
};
export default ImageFileInput;
```

2. 평상시에는 input이 보이지 않고 button이 클릭이 되면 input이 클릭된 것처럼 수동적으로 만들어주기 

```jsx
import React, { useRef } from "react";
import styles from "./image_file_input.module.css";

const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  const inputRef = useRef();
  const onButtonClick = () => {
    inputRef.current.click();
  };
  return (
    <div className={styles.container}>
      <input
        ref={inputRef}
        className={styles.input}
        type="file"
        accept="image/*"
        name="file"
      />
      <button className={styles.button} onClick={onButtonClick}>
        {name || "No file"}
      </button>
    </div>
  );
};
export default ImageFileInput;
```

CSS 

```jsx
@value makerLightGrey from "/src/common/colors.css";

.container {
  width: 100%;
  height: 100%;
  display: flex;
}

.input {
  display: none;
}

.button {
  width: 100%;
  background-color: makerLightGrey;
  font-weight: bold;
  cursor: pointer;
  border: 0;
  outline: 0;
  font-size: 0.8rem;
}

.button:hover {
  opacity: 0.8;
}
```

3. 사용자가 사진을 선택했을 때, input data가 변경 됐을 때 .. 사진을 업로드하고 URL을 받아오기 

```jsx
const onChange = async event => {
    const uploaded = await imageUploader.upload(event.target.files[0]);
    onFileChange({
      name: "fileName",
      url: "url"
    })
  };
```

4. image_uploader.js

```jsx
class ImagaUploader {
  async upload(file) {
    const data = new FormData();
    data.append("file", file);
    data.append("upload_preset", "pdzaoz52");
    const result = await fetch(
      "https://api.cloudinary.com/v1_1/demo/image/upload",
      {
        method: "POST",
        body: data,
      }
    );
    return await result.json();
  }
}

export default ImagaUploader;
```

5. onChange부분 

```jsx
const onChange = async (event) => {
    const uploaded = await imageUploader.upload(event.target.files[0]);
    console.log(uploaded);
    onFileChange({
      name: uploaded.original_filename,
      url: uploaded.url,
    });
  };
```

6. card edit, card add 컴포넌트에 전달 

```jsx
<FileInput onFileChange={onFileChange} />
```

7. 이제 각각 onFileChange 함수 지정 - card edit component 

```jsx
const onFileChange = (file) => {
    updateCard({
      ...card,
      fileName: file.name,
      fileURL: file.url,
    });
  };
```

8. card add component는 state지정하고 onFileChange 만들기

```jsx
const CardAddForm = ({ FileInput, onAdd }) => {
  const formRef = useRef();
  const nameRef = useRef();
  const companyRef = useRef();
  const themeRef = useRef();
  const titleRef = useRef();
  const emailRef = useRef();
  const messageRef = useRef();
  const [file, setFile] = useState({ fileName: null, fileURL: null });

  const onFileChange = (file) => {
    setFile({
      fileName: file.name,
      fileURL: file.url,
    });
  };
```

9. 새로운 카드, 카드 초기화 

- onSubmit 부분에서 수정

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
      fileName: file.name,
      fileURL: file.url,
    };
    formRef.current.reset();
    setFile({ fileName: null, fileURL: null });
    onAdd(card);
  };
```

## Loading spinner 
1. image_file_input

```jsx
const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  const [loading, setLoading] = useState(false);
  const inputRef = useRef();
  const onButtonClick = (event) => {
    event.preventDefault();
    inputRef.current.click();
  };

  const onChange = async (event) => {
    setLoading(true);
    const uploaded = await imageUploader.upload(event.target.files[0]);
    setLoading(false);
    onFileChange({
      name: uploaded.original_filename,
      url: uploaded.url,
    });
  };
```

2. 로딩이 되면 보여주고, 안 되면 안 보여주는 것 만들기

```jsx
return (
    <div className={styles.container}>
      <input
        ref={inputRef}
        className={styles.input}
        type="file"
        accept="image/*"
        name="file"
        onChange={onChange}
      />
      {!loading && (
        <button className={styles.button} onClick={onButtonClick}>
          {name || "No file"}
        </button>
      )}
      {loading && <div className={styles.loading}></div>}
    </div>
```

3. 로딩 스피너 자체 제작!!!

```css
.loading {
  width: 1.5em;
  height: 1.5em;
  border-radius: 50%;
  border: 3px solid makerLightGrey;
  border-top: 3px solid makerPink;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
```

4. 완료가 되면 pink색이 되게 하기

```css
{!loading && (
        <button
          className={`${styles.button} ${name ? styles.pink : styles.grey}`}
          onClick={onButtonClick}
        >
```

CSS에서 button자체 백그라운드 지우고 나누기 

```css
.button {
  width: 100%;
  height: 100%;
  font-weight: bold;
  cursor: pointer;
  border: 0;
  outline: 0;
  font-size: 0.8rem;
}

.button.grey {
  background-color: makerLightGrey;
}

.button.pink {
  background-color: makerLightPink;
}
```
