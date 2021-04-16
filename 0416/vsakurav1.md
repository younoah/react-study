### DOM Access

> 리액트에서는 직접적으로 DOM선택을 하지 않는다.

> dom을 조작해야 할때는 ref를 이용하고, ref안에 있는 current를 이용해서 접근 한다.

```js
class HabitAddForm extends React.Component {
  inputRef = React.createRef();
}
onSubmit = (e) => {
  e.preventDefault();
  console.log(inputRef.current.value);
};
```

### Components

#### class component

- React.Component
- React.PureComponent

#### function component

- memo(function)
- React Hook

부모 컴포넌트의 state가 변경되면 자식 컴포넌트가 모두 리랜더링 된다.
virtual dom을 사용해서 간단한 동작은 성능상으로 크게 문제가 되지 않지만 api 호출등의 무거운 작업을 하게 된다면 성능저하 문제가 일어난다.

### class component

#### PureComponent

> props 와 state 안에 들어 있는 데이터가 변하지 않으면 re-rendering을 하지 않는다.

> 리액트 공식 홈페이지에 의하면 React.Component와 React.PureComponent의 차이는 shouldComponentUpdate 라는 LifeCycleMethod 구현의 차이 라고 한다. (shouldComponentUpdate 는 특정 조건에 component를 업데이트 하는 동작을 한다. 여기서는 props와 state를 비교한다.)

#### shallow comparison

> reference 만 비교한다.

> 이렇게 reference 값들을 수정해도 참조 값이 같기 때문에 데이터가 변경됨을 감지하지 못한다.

> 그래서 불변성을 지키면서 새로운 reference를 반환하는 map이나 filter등을 이용하여 데이터를 업데이트 한다.

### Life Cycle Method

#### componentDidMount

- 컴포넌트가 UI상에 등록이 되었을 때 호출한다
- 주로 api 호출 등을 처리한다.

#### componentWillUnmount

- 컴포넌트가 UI상에서 삭제 되기 직전에 호출한다.

- clearInterval 등, 메모리 누수를 막는다.

### function component

class component와 function component 예제

> class component

```js
import React, { Component, createRef } from "react";

class HabitAddForm extends Component {
  formRef = createRef();
  inputRef = createRef();

  onSubmit = (e) => {
    e.preventDefault();
    const name = this.inputRef.current.value;
    name && this.props.onAdd(name);
    this.formRef.current.reset();
  };

  render() {
    return (
      <form ref={this.formRef} className="add-form" onSubmit={this.onSubmit}>
        <input
          className="add-input"
          type="text"
          placeholder="Habit"
          ref={this.inputRef}
        />
        <button className="add-button" type="submit">
          Add
        </button>
      </form>
    );
  }
}

export default HabitAddForm;
```

> function component

```js
import { useState } from "react";

function HabitAddForm({ onAddForm }) {
  const [name, setName] = useState("");

  const onChange = (e) => {
    setName(e.target.value);
  };

  const onSubmit = (e) => {
    e.preventDefault();
    if (name) {
      onAddForm(name);
    }
    setName("");
  };

  return (
    <form className="add-form" onSubmit={onSubmit}>
      <input
        className="add-input"
        type="text"
        placeholder="Habit"
        onChange={onChange}
        value={name}
      />
      <button className="add-button" type="submit">
        Add
      </button>
    </form>
  );
}

export default HabitAddForm;
```

#### memo == PureComponent

props의 데이터가 변경 될때에만 re-rendering 한다.

### React Hook

**function component는 전체가 반복되어 진다.**

> useState는 리액트 내부에서 따로 처리 하기 떄문에 반복적으로 실행이 되어도 기억되어진다.

- useRef
  == React.createRef
- useCallback
  처음 컴포넌트가 랜더링 될때 useCallback으로 만들어진 함수를 캐시에 담아 기억하고 있는다. 그러면 리랜더링 되어져도 이 함수는 다시 생성되지 않는다.
- useEffect
  componentDidMount + componentDidUpdate
