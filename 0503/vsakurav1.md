### Form

```jsx
// 스스로 해 본 addForm 작성 코드
const initialForm = {
  name: '',
  company: '',
  theme: '',
  title: '',
  email: '',
  message: '',
};
const [form, setForm] = useState(initialForm);
const { name, company, theme, title, email, message } = form;
const onChange = e => {
  const { name, value } = e.target;
  setForm({
    ...form,
    [name]: value,
  });
};
const onSubmit = e => {
  e.preventDefault();
  addCard(form);
  setForm(initialForm);
};

// 엘리님 코드
const formRef = useRef();
const nameRef = useRef();
const companyRef = useRef();
const themeRef = useRef();
const titleRef = useRef();
const emailRef = useRef();
const messageRef = useRef();

const onSubmit = e => {
  e.preventDefault();
  const card = {
    id: Date.now(),
    name: nameRef.current.value || '',
    company: companyRef.current.value || '',
    theme: themeRef.current.value,
    title: titleRef.current.value || '',
    email: emailRef.current.value || '',
    message: messageRef.current.value || '',
    fileName: '',
    fileURL: '',
  };
  formRef.current.reset();
  onAdd(card);
};
```

> React 진영에서도 ref는 focus 등 DOM에 직접 접근해야만 할 때 사용을 권고한다고 한다.  
> 아마 엘리님은 초보자의 입장에서 state 관리로 이루어지는 렌더링 최적화가 힘들기 때문에 ref를 이용하지 않았나 생각된다.

### 성능 최적화

🔅 array 대신 object 활용 방법

`Object.keys()`를 이용해서 배열 형태로 만들어주어서 map을 활용한다.

❔ 궁금 👀

실제로 API에서 배열의 형태로 내려주는 것도 많이 봤는데 여기서 의문이 생긴다.

- 배열 형태의 데이터들은 object로 변환해서 다시 사용을 해야 하나..?
- 그것은 효율적인가?

하는 생각이 든다
뒤의 강의에서 실제 firebase를 이용해서 데이터를 받아오게 되면 그 해답이 나오지 않을까 생각된다.

#### 정리

국내뿐만 아니라 해외의 react 강의들도 접해봤지만 Object를 이용한 state 관리는 처음 접했다.
기존에 알고 있던 react에서의 map은 기본적인 반복 문의 형태보다 성능이 좋다고 알고 있었는데 이번 영상을 계기로 구글링 해보니 오히려 느리다는 의견도 많아서 다시 한번 찾아봐야 할 것 같다는 생각이 들었다.
실제로 `Object.keys()`를 활용한 코드를 본 적은 있는데, 잘 알고 있으면 유용하게 쓰일 것 같다.
