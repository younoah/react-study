Habit tracker 
## 4.11) Navbar Component 만들기 
___
✅의미있는 tag를 이용하기 (div 남용 NO! 😈)

🎃재미있던 부분 
```js
<Navbar 
  totalCount={this.state.habits.filter(item => item.count > 0).length}
  />
```
## 4.12) Add Form 만들기 (Ref 이용)

Refs and the DOM: [https://reactjs.org/docs/refs-and-the-dom.html](https://reactjs.org/docs/refs-and-the-dom.html)

리액트는 바로 DOM 요소에 접근하지 않고, 필요할 때는 이렇게 리액트에서 제공하는 **createRef( )** 를 이용하여 멤버변수를 정의한 다음에 그것을 원하고 _해당하는 리액트 컴포넌트에 ref로 연결_ 하면 됨.

```jsx
class HabitAddForm extends Component {
  formRef = React.createRef();
  inputRef = React.createRef();
  onSubmit = (event) => {
    event.preventDefault();
    const name = this.inputRef.current.value;
    name && this.props.onAdd(name);
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

export default HabitAddForm;
```
> event.preventDefualt(); 는 브라우저의 기본 기능을 취소 -> 더는 불필요한 리프레쉬가 일어나지 않음.
### input에 입력된 데이터를 알기 위해서

1. 일반 js라면 querySElector를 이용해 클래스를 이용해 input 필드를 받아온 다음, input의 value를 받아옴. 
2. React는 Ref를 사용 (class component에서는 creatRef();) -> 해당하는 react component에 Ref 전달 

```jsx
handleAdd = (name) => {
    const habits = [...this.state.habits, { id: Date.now(), name, count: 0 }];
    this.setState({ habits });
  };
```
## 4.14) Rest 처리
재사용되지 않으면 component로 만들 필요 없음 
```js
handleReset = () => {
  const habits = this.state.habits.map(habit => {
    habit.count = 0;
    return habit;
  });
  this.setSTate({ habits });
}
```
## 4.14) PureComponent 정리와 차이점 이해(성능 분석)

PureComponent: [https://reactjs.org/docs/react-api.html#reactpurecomponent](https://reactjs.org/docs/react-api.html#reactpurecomponent)

PureComponent(or memo)없이 써도 밑에 두 글의 이유때문에 괜찮지만...

- React는 Virtual Dom이라는 메모리 상에 이런 트리를 보관해 놓고 있기 때문에 이전과 지금 업데이트 된 것을 비교해서 실제로 필요한 부분만 DOM요소에 업데이트한다.
- ‼️ 만약에 개발자 툴에서 Element를 켜서 웹 페이지를 클릭해보고 하는데 html파트가 계속 깜빡이면서 변화가 많으면 잘못 만든 것임.

그래도 문제가 있을 수 있다!!! 

예를 들어, componentDidUpdate( )라는 React가 Component가 업데이트 될 때마다 호출해주는 이런 함수가 있는데, 우리가 Component가 업데이트 될 때마다 유튜브에 있는 데이터를 받아 온다던지, 조금 무거운 일(로직)을 수행해야 되면 render함수가 호출 될 때마다 얘가 호출 되어서 불필요한 일들이 수행될 수 있음(화면 깜빡임같은) 

- PureComponent ⇒ implements it with a shallow pros and state comparison.
- Component가 업데이트 되기 전에 React에서 **'야, 너 Component 업데이트 되어야 되니?'**라고 물어보는 'shouldComponentUpdate'라는 함수가 있는데... Component는 이 함수를 기본적으로 구현해 놓지 않았지만 PureComponent는 이 함수를 구현해 놓았음!

→ PureComponent는 props와 state 안에 들어 있는 최상위의 데이터가 변하지 않으면 render 함수가 호출되지 않음

### * **두개의 변수(states)가 동일한 오브젝트를 가리키고 있는 경우:**

const ellie1 = { name: 'ellie' };

const ellie2 = ellie1;

1. **ellie1 == ellie2** => true

2. **ShouldComponentUpdate** => false

두개의 참조값(ref)이 같으므로, 업데이트 할 필요가 없어서 ShouldComponentUpdate 함수는 false를 리턴

### ***두개의 변수(states)가 다른 오브젝트를 가리키고 있는 경우:**

(이때, 동일한 값을 가지고 있더라도, 두개는 엄연히 다른 오브젝트이므로 다른 참조값을 가지게 됨)

const ellie1 = { name: 'ellie' };

const ellie2 = { name: 'ellie' };

1. **ellie1 == ellie2** => false

2. **ShouldComponentUpdate** => true

두개의 참조값이 다르므로, 업데이트가 필요함! ShouldComponentUpdate 함수는 true를 리턴

## 4.15)  PureComponent 활용

- 콜백함수는 App에서 전달되는 건데 , App이라는 클래스가 만들어진 이후로는 절대 변경되지 않기때문에 전달받은 props들은 절.대 변하지 않음.
- **항상 object는 불변의 아이로 놔두고 데이터가 변경이 된다면 다시 새로운 object를 만드는 것이 좋음(모든 코딩에서!)**

예로 설명 

```jsx
handleIncrement = habit => {
	const habits = [...this.state.habits]; // object의 ref만 복사해서 가져온 것임. 
	const index = habits.indexOf(habit);
	habits[index].count++; //그러므로 여기서의 habits의 ref는 같으므로 수정해도 shallow comparison
	this.setState({ habits });
};
```
>✅이렇게 하면 habits라고 복사를 해도 ref가 같음.  habits[index].count++ 에서 habits 안에 있는 data만 변경해도 같은 ref이기 때문에 PureComponent에서 동일하다고 판단되므로 작동이 안됨.  
ShouldComponentUpdate(너 바꿀거니?) 함수는 false(아니, 얘네들 ref 같고..ref 변경된 것 없어)를 리턴

그러므로
 
```jsx
handleIncrement = (habit) => {
    const habits = this.state.habits.map((item) => {
      if (item.id === habit.id) {
        return { ...habit, count: habit.count + 1 };
      }
      return item;
    });
    this.setState({ habits });
  };
```
> ✅ 이렇게 map으로 빙빙 돌려주면서 새로 만들어서 다른 object를 생성해서 만들어 주면 됨.
> ✅Object 전체를 변경 
> ✅ShouldComponentUpdate 함수는 true를 리턴

## 4.16) Lifecycle

* 새로운 데이터를 네트워크 통신을 통해서 받아 오거나, 다양한 일들을 해야 한다면? -> Lifecycle 함수가 가능하게 해줌! 
* 원하는 상황에 따라서 특정한 기능을 수행해야 할때 리액트가 알아서 우리가 구현한 함수를 호출.
* 자주 쓰이는 것들 
```js
ComponentDidMount() {
}
```
```js
ComponentWillUnmount() {
}
```
## 4.17) Fucntion과 컴포넌트, memo 정리

rsi 를 친 후 tab을 눌러 function 만들기 (class만들대는 rcc)

- 함수로 만드는 것은 속에 state가 없고 lifecycle이 없다면 쉽다.  → 그냥 rsi로 만들어준 함수에 (여기에서도 함수 value의 첫 글자는 대문자) return 값과 클래스 내부의 값들을 다 가져온다음 const 로 지정되야 할 것 넣어주고, this.를 다 지운다.
- **state가 없다면 이렇게 간단하게 function component쓰면 좋다**

위의 예제
1. 클래스 
```jsx
import React, { PureComponent } from "react";

class HabitAddForm extends PureComponent {
  formRef = React.createRef();
  inputRef = React.createRef();
  onSubmit = (event) => {
    event.preventDefault();
    const name = this.inputRef.current.value;
    name && this.props.onAdd(name);
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

export default HabitAddForm;
```
2. 함수 컴포넌트
```jsx
import React from "react";

const HabitAddForm = (props) => {
  const formRef = React.createRef();
  const inputRef = React.createRef();

  const onSubmit = (event) => {
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
};

export default HabitAddForm;
```
⇒ 위에 처럼 바꿔준 결과!!! PureComponent가 없어지면서 다시 불필요한 전체가 업데이트되고 있음(reading의 increment를 클릭했는데 habitaddform이 업데이트 같이 됨)

> class의 PureComponent처럼 함수에서는 memo를 쓴다. 

* props가 변경되지 않으면 이 안에 있는 함수가 호출되지 않음

## 4.18) React Hook

React Hooks: [https://reactjs.org/docs/hooks-intro.html](https://reactjs.org/docs/hooks-intro.html)

- function component에서 state와 lifecycle을 추가할 수 있게 만듦

```jsx
import React, { Component } from "react";

class SimpleHabit extends Component {
  state = {
    count: 0,
  };

  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <li className="habit">
        <span className="habit-name">Reading</span>
        <span className="habit-count">{this.state.count}</span>
        <button
          className="habit-button habit-increase"
          onClick={this.handleIncrement}
        >
          <i className="fas fa-plus-square"></i>
        </button>
      </li>
    );
  }
}

export default SimpleHabit;
```

- React hook에서는 class component에서 나오는 state대신 useState라는 API를 사용하면 됨

    ```jsx
    const [count, setCount] = useState(초기값);
    ```
> 이렇게 변수를 선언할 때 count와 setCount 두 개를 선언해 놓으면 이 useState를 호출하게 되면 react에서 두 가지를 return해준다.
 
* 그 두가지는

1) 실제의 state값
2) 이 count를 업데이트할 수 있는 함수 setCount
   
```jsx
import React, { Component, useState } from "react";

import React from 'react';

const SimpleHabit = (props) => {
  const [count, setCount] = useState(0); // useState(초기값)
  return (
    <li className="habit">
      <span className="habit-name">Reading</span>
      <span className="habit-count">{this.state.count}</span>
      <button
        className="habit-button habit-increase"
        onClick={this.handleIncrement}
      >
        <i className="fas fa-plus-square"></i>
      </button>
    </li>
  );
};
```

```jsx
import React, { useState } from "react";

const SimpleHabit = () => {
  const [count, setCount] = useState(0);

  const handleIncrement = () => {
    setCount(count + 1);
  };

  return (
    <li className="habit">
      <span className="habit-name">Reading</span>
      <span className="habit-count">{count}</span>
      <button className="habit-button habit-increase" onClick={handleIncrement}>
        <i className="fas fa-plus-square"></i>
      </button>
    </li>
  );
};

export default SimpleHabit;
```

주의할 점은 클래스는 한 번 만들어지면 클래스 안에 있는 멤버 변수들같은 아이들은 클래스가 만들어질 때 딱 한 번 만들어지고 렌던 함수만 반복적으로 호출된다. 

But **함수는 컴포넌트가 변경이 되면 코드 블락 전체가 계속 반복해서 호출이 됨**

그 주의 사항을 해결할 방법~!
```jsx
import React, { useRef, useState } from "react";

const SimpleHabit = () => {
  const [count, setCount] = useState(0);
  const spanRef = useRef();

  const handleIncrement = useCallback(() => {
    setCount(count + 1);
  });

  return (
    <li className="habit">
      <span ref={spanRef} className="habit-name">
        Reading
      </span>
      <span className="habit-count">{count}</span>
      <button className="habit-button habit-increase" onClick={handleIncrement}>
        <i className="fas fa-plus-square"></i>
      </button>
    </li>
  );
};

export default SimpleHabit;
```

- react hook에서 자체로 제공하는게 바로 useState같은 API. 이것은 자동 저장 기능이 있어서 계속 반복되어도 초기화가 안 됨.
- 이것과 비슷한 것이 바로 useRef(); →  createRef처럼 매번 호출할때 마다 새로운 ref를 만드는 것이 아니라 메모리에 저장해 놓고 재사용함
- 함수에는 useCallback();
- componentdidMount (or componentDidUpdate)같은 것이 바로 useEffect();

```jsx
useEffect(() => {
    console.log(`mounted & updated!: ${count}`);
  }, [count]); //[count]라는 변수가 변경될때마다 호출이 됨
```
> [count]가 변경될때마다 함수가 호출이 됨 , [ ]안이 비어지면 처음에만 호출 (처음 마운트 되었을때만 유튜브 API 가져오고 싶을때 유용)

1) [ ] 자체를 넣지 않으면(아예 [ ]이 없는 경우) , 기존에 있는 이 state나 props가 변경될 때마다 호출

2) [ count] 등 무엇을 넣으면, count가 변경될때마다 호출

3) [ ]을 비워두면, 처음에만 호출 

