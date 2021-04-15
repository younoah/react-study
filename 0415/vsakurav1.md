# React Components

리액트는 두가지 형식으로 컴포넌트를 생성한다

- class 기반의 컴포넌트
- functional 기반의 컴포넌트

리액트 진영에서 functional 컴포넌트를 추천하고 있지만 아직 legacy한 프로젝트들은 class 기반의 프로젝트가 많기 때문에 알아야 한다.

### JSX ?

JSX는 HTML과 흡사해 보이지만, 사실은 JavaScript다.

```js
import React from "react";

class App extends React.Component {
  state = {
    number: 0,
  };

  render() {
    return (
      <div className="App">
        {this.state.number}
        <button
          onClick={() => this.setState({ number: this.state.number + 1 })}
        >
          +1
        </button>
      </div>
    );
  }
}

export default App;
```

여기서 HTML, JSX의 차이점을 알 수 있다.

- className, htmlFor, onClick 등 JavaScript의 키워드는 사용 할 수 없다.
- JSX 안에서 {} 를 이용하여 JavaScript를 사용 할 수 있다.
- JSX 는 하나의 요소만 return 할 수 있기 때문에 빈껍데기 (<></>) 태그나 <React.Fragment> 태그를 최상위에서 사용해 주면 쓸데 없는 태그 사용을 줄일 수 있다.

### State

state 값은 절대 직접적으로 수정하면 안된다.
react에서 제공하는 setState를 이용하여 state를 변경함으로써 변화를 감지하고 reRendering을 통해 UI가 변경된다.

#### setState는 비동기 처리이다.

비동기 함수 라는것은 조금의 지연이라도 발생하거나, 동시에 여러값을 수정하게 될 경우
원하는대로 동작하지 않을 수 있다는걸 명심해야 하고, 그렇기 때문에 함수형 업데이트가 필요하다.

```js
import { useState } from "react";

function App() {
  const [number, setNumber] = useState(0);

  const handleIncrement = () => {
    setNumber((prevNumber) => prevNumber + 1);
    // 함수형 업데이트
  };

  return (
    <div className="App">
      {number}
      <button onClick={handleIncrement}>+1</button>
    </div>
  );
}

export default App;
```

### KEY

react에서 map을 이용한 반복을 굉장히 많이 사용하는데
이때 필요한 속성에 key가 있다.

```js
render() {
  return (
    <ul>
      {this.state.habits.map((habit) => (
        <Habit
          key={habit.id}
          ...
        />
      ))}
    </ul>
  );
}
```

이 때 중요한 것은 key는 unique한 값이어야 한다!

### props

props는 부모에서 자식으로 넘겨주는 값들을 뜻한다. (properties)
이 때 꼭 명심해야 할 것은 props로 받아온 값을 변경하면 안된다는 점이다.
만약 props로 받아오는 데이터를 수정하고 싶다면
자식 컴포넌트의 state에 그 값을 담아서 자식 컴포넌트 내에서 자신의 state 값을 수정하거나
부모에게서 받는 메서드를 활용해서 부모의 state값을 바꾸어 내려오는 props 값이 변경되게 해야 한다.
