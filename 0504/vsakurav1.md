### Add Service

authService와 마찬가지로 통신하는 로직은 별도의 파일로 분리해서 관리해 준다.

```jsx
const FileInput = props => (
  <ImageFileInput {...props} imageUploader={imageUploader} />
);
```

위와 같이 한번 감싸줌으로써 prop을 전달하고 컴포넌트를 사용함에 확장성이 더 좋아질 수 있다.

### input

```jsx
function ImageFileInput({ imageUploader, name, onFileChange }) {
  const inputRef = useRef();
  const onButtonClick = (e) => {
    e.preventDefault();
    inputRef.current.click();
  };
  return (
    <div className={styles.container}>

// 엘리님 코드
<input
ref={inputRef}
  className={styles.input}
  type="file"
  accept="image/*"
  name="file"
/>
<button className={styles.button} onClick={onButtonClick}>
  {name || 'No file'}
</button>

// 내가 생각하는 코드
<input
  className={styles.input}
  type="file"
  accept="image/*"
  name="file"
  id="file"
/>
<label className={styles.button} htmlFor="file">
  {name || 'No file'}
</label>
...
```

> `input[type="file"]`

사실 input:file 을 사용해 볼 일이 없어서 잘 몰랐는데, 해당 change 이벤트를 통해 실시간으로 file 정보를 알 수 있었다.

_개인적으로 웹 접근성 측면에서 input을 감싸려면 label을 사용하는 게 맞지 않을까 생각되었다. 하지만 검색하면서 찾아보고, 조그마한 지식에 갇혀있었다는 걸 알게 되었다. 이번 강의에서는 배울 점이 많았던 것 같다._
