## 리액트 컴포넌트의 종류

### 클래스 컴포넌트

- React.Component
- React.PureComponent



### 함수 컴포넌

- function
- memo(function)
- React Hook



## Ref

Ref는 render 메서드에서 생성된 DOM 노드나 React 엘리먼트에 접근하는 방법이다.

일반적으로 자바스크립트에서 다른 DOM요소의 값을 참고할 때

```js
const value = document.querySelector()
```

를 사용한다.

리액트에서는 DOM요소를 직접적으로 사용하지 않기 때문에 다른 리액트의 요소에 접근하고 싶다면 Ref를 사용하면된다.

```jsx
class HabitAddForm extends Component {
  formRef = React.createRef();
  inputRef = React.createRef();

  onSubmit = event => {
    event.preventDefault();
    const name = this.inputRef.current.value;
    name && this.props.onClickAdd(name);
    // this.inputRef.current.value = '';
    this.formRef.current.reset();
  };

  render() {
    return (
      <form ref={this.formRef} className="add-form" onSubmit={this.onSubmit}>
        <input
          ref={this.inputRef}
          type="text"
          className="add-input"
          placeholder="Habit"
        />
        <button className="add-button">Add</button>
      </form>
    );
  }
}
```



## pure.Component



리액트는 state나 관련된 props가 변경이 되면 render함수가 호출이 된다.

pure.Component와 memo는 컴포넌트의 상태나 props에 변화가 없다면 render함수가 호출되지 않게 해준다.



단, state와 props를 얕게 검사하기 때문에 주의가 필요하다. state의 **참조값**이 변하지 않으면 render가 되지 않기 때문에 `setState()` 를 사용할 때 새로운 참조값의 객체를 생성하여 넘겨주도록 하여 사용하자.



- 기존 Component

```jsx
onClickIncrement = habit => {
    const habits = [...this.state.habits];
    const index = habits.indexOf(habit);
    habits[index].count++; // 기존 객체 활용
    this.setState({ habits });
  };
```



- pure.Component

```jsx
handleIncrement = habit => {
    const habits = this.state.habits.map(item => {
      if (item.id === habit.id) {
        return { ...habit, count: habit.count + 1 }; // 새로운 객체 생성
      }
      return item;
    });
    this.setState({ habits });
  };
```





## Lifecycle 함수들



- `componentDidMount` : 컴포넌트가 UI상에 등록되어 보여질 때 호출
- `componentWillUnmount` : 컴포넌트가 UI상에 해제되어 사리지기 전에 호출
- ...



## function 컴포넌트와 memo

memo는 클래스 컴포넌트에서 pure.Component와 비슷한 역할을 한다.



```jsx
const HabitAddForm = memo(props => { 
    //props가 변경되지 않으면 호출되지 않는다.
  const formRef = React.createRef();
  const inputRef = React.createRef();

  const onSubmit = event => {
    event.preventDefault();
    const name = inputRef.current.value;
    name && props.onAdd(name);
    formRef.current.reset();
  };

  return (
    <form ref={formRef} className="add-form" onSubmit={onSubmit}>
      <input
        ref={inputRef}
        type="text"
        className="add-input"
        placeholder="Habit"
      />
      <button className="add-button">Add</button>
    </form>
  );
});
```





## 리엑트훅

리액트 훅은 기존의 함수형 컴포넌트에서 state와 라이프사이클 메서드를 잘 이용할 수 있도록 도와준다.



- `useState()` : 클래스 컴포넌트에서 state와 setState()

```jsx
const [count, setCount] = useState(0); // count의 초기값 0
```



- `useRef` : 클래스 컴포넌트에서 React.createRef()

```jsx
const spanRef = useRef();

<span ref={spanRef}></span>
```



- `useCallback` : 콜백함수를 기억하기 위해 사용, 콜백함수가 매번 실행되어 렌더링이 되는 사이드 이펙트를 막기 위해 사용

```jsx
const handleIncrement = useCallback(()=>{
    ...
});

<button onClick={handleIncrement}></button>
```



- `useEffect(callBack, state)` : 컴포넌트가 마운트 되었을 때 혹은 업데이트가 될 때 마다 호출된다. 특정 state가 변경되었을 때마 호출 되도록 하고싶으면 두번째 인자로 전달해준다.



> 클래스는 멤버변수가 딱 한번 생성되는 반면에 function 컴포넌트는 매번 실행되기 때문에 계속 반복해서 생성된다.
>
> 단, useState를 사용하면 리액트가 상태를 계속 기억하고 있는다.

