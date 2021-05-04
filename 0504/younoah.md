## **이미지파일인풋 컴포넌트에 이미지 업로더 서비스클래스 전달하기**

index- app -> maker -> editor -> addform(editform)에서 `ImageFileInput` 컴포넌트가 존재하고

최종적으로 `ImageFileInput` 컴포넌트에 `imageUploader` 서비스를 전달하려고한다.

일반적으로 `index` 에서 서비스들을 생성해서 `App` 의 props에 전달하여 `App` 을 생성한다. 하지만 `ImageFileInput` 에 서비스를 전달하기 위해 `App` 에서부터 서비스를 전달해주게 되면 서비스가 많아질때 props에 너무 많은 서비스들을 전달하게 된다.

그래서 `index` (외부) 에서 `ImageFileInput` 컴포넌트를 생성해서 서비스를 완성시키고 `App` 에서 부터 계속 전달해 가는 방식을 이용하면 여러개의 서비스를 props로 전달하는게 아닌 1개의 완성된 컴포넌트를 전달하기 때문에 훨씬 깔끔하게 사용할 수 있다.

## **ImageFileInput 컴포넌트 생성방식**

```js
// 단순 컴포넌트 할당
const FileInput = <ImageFileInput imageUploader={imageUploder} />;
// props를 미리 완성하더라도, props를 새로 정의할 수 없기 때문에 확장성이 좋지 않다.

// 화살표함수 이용
const FileInput = props => (
  <ImageFileInput {...props} imageUploader={imageUploder} />
);
// props을 새로 전달할 수 있기 때문에 확장성이 용이하다.
// {...props}의 형태를 주의깊게 보자.
```

> **궁금증** > `<ImageFileInput {...props} />` 에서 props 전달을 `{...props}` 이 부분이 구조분해하고 객체로 하는 걸로 착각했었다.
>
> jsx 변수정의를 사용해서 객체의 구조를 분해해서 할당하는 방식으로 해석하면 될 것 같다.
>
> 그래도 이렇게 props를 전달할 수 있는게 신기하다.



## 파일타입 인풋 꾸미기

file타입의 input은 UI가 정해저 있다.

ui를 커스텀하기위해서는 파일타입의 input을 숨기고 버튼을 생성하여 ui를 작성하면된다.

```jsx
const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  const inputRef = useRef();
  const onButtonClick = event => {
    event.preventDefault();
    inputRef.current.click();
    // input을 대체한 버튼을 클릭시, input을 클릭한 효과낸다.
  };

  return (
    <div className={styles.container}>
      <input // display: none;
        ref={inputRef}
        className={styles.input}
        type="file"
        accept="image/*"
        name="file"
        onChange={onChange}
      />
      <button className={styles.button} onClick={onButtonClick}>
        {name || 'No file'}
      </button>
    </div>
  );
};
```

## 로딩구현

```jsx
const ImageFileInput = ({ imageUploader, name, onFileChange }) => {
  const [loading, setLoading] = useState(false);

  ...

  const onChange = async event => {
    setLoading(true);  // 로딩시작
    const uploaded = await imageUploader.upload(event.target.files[0]);
    setLoading(false); // 로딩 끝
    onFileChange({
      name: uploaded.original_filename,
      url: uploaded.url,
    });
  };

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
      {!loading && ( // 로딩이 끝났을 때, 버튼 컴포넌트
        <button
          className={`${styles.button} ${name ? styles.pink : styles.grey}`}
          onClick={onButtonClick}
        >
          {name || 'No file'}
        </button>
      )}
      {loading && <div className={styles.loading}></div>} // 로딩중 일 때, 스피너 컴포넌트
    </div>
  );
};
```