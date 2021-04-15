# react-study-Day1


### Habit Tracker
___
## 4.1 리액트의 전반적인 개념 정리
> 리액트는 컴포넌트이다
1. Class Component
2. Function Component
### 1) Class component는 Componenet라는 클래를 extends, 상속해서 만들 수 있음.

- 상태, 데이터를 담을 수 있는 state라는 object가 들어 있음 → 상태가 변경이 되면 렌더 함수가 호출이 되면서 업데이트된 내용이 사용자에게 보여짐.
- Component에 state가 있고 정기적으로 update가 되어야한다면 class component 사용.
- lifecycle method: 컴포넌트가 사용자에게 보여질 때, 돔트리에 올라 갔을 때, 돔트리에서 나왔을 때 그리고 컴포넌트가 업데이트 되었을 때 등등.. 이런 다양한 컴포넌트의 상태에 따라서 우리가 함수를 구현해 놓으면 리액트가 알아서 불러주는 것이 lifecycle method라고 한다.

> 👀 Class component가 있음에도 React hook이 생긴 이유? 

1) Class is difficult 
2) 멤버 변수에 접근할 때 this를 붙여야 함
3) binding issue
4) Many people want functional programming.
5) component가 마운트 되었을때, 언마운트 되었을때, 업데이트 되었을 때의 세부적인 함수가 나누어져 있어서 code duplication이 있음. 
=⇒ 그래도 기존에 class로 적힌 게 많으므로 둘 다 아는 것이 중요!

### 2) Fucntion은 간단하게 Function을 사용하여 만듦.

- 정적으로 표시할 시에 씀
- state없음
- lifecycle method없음
- 함수는 한 가지의 기능을 수행하는 단위임.

—> but React Hook을 이용하면 state도 가질 수 있고, lifecycle methods도 사용할 수 있음.

___
## 4.2 템플릿 프로젝트 만들기 
- public에는 static한 아이들 
- src에는 dynamic한 아이들
___
## 4.3 React Dom
- React dom을 이용해 우리가 만든 react의 component들을 브라우저 상에서 이해할 수 있음.
```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.module.css";
import App from "./app";
import AuthService from "./service/auth_service";

const authService = new AuthService();
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);
```
- ReactDom.render()로 그 안에 어떤 Components를 브라우저가 이해할 수 있게 바꿔줄 지 설명.
- document.getElementById("root")를 통해 html이 불러 진 것도 알 수 있음.
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <title>App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```
✨index.js는 상위 component인 app을 id가 root인 div태그로 전달.
___
## 4.5 JSX란? (HTML과의 차이점 정리)
- 자바스크립트 코드위에서 간단하게 HTML처럼 할 수 있도록 만들어진 것. 

##### JSX와 HTML의 다른점은 무엇인가?

> HTML과 비슷하나 엄밀히 말하자면 JSX는 자바스크립트임. 

1) class를 넣을때 className이라고 해줘야하고, onclick도 onClick이라고 해줘야 함.
2) 변수 name을 쓰고 싶으면 {}로 감싼 후 넣으면 된다. 
3) 형제 노드를 만들 수 없음 (It should wrapped in an eclosing tag) ⇒ 이럴때는 <React.fragment>로 감싸주거나 <>이렇게 빈칸으로 두면 됨. 의미있는 것으로 묶어주고 싶으면 div, ul, ol 등 쓰면 됌.
4) JSX 안에서는 자바스크립트 코드가 작성이 가능하다. → 비즈니스 로직도 작성 가능 
5)  {}블록을 이용하여 array를 작성할 수도 있다. 
```js
function App() {
  const name = "youme";
  return (
    <>
    <h1 className="name">Hello, {name}:)</h1>
    <h1>How are you? {name}</h1>
    {name && <h1>{name}</h1>}
    {['🍉', '🍒'].map(item => (
        <h1>{item}</h1>
    ))}
    </>
  )
}
```

여기서 이 부분을 보면...
```js
{['🍉', '🍒'].map(item => (
        <h1>{item}</h1>
    ))}
```
여기서의 ( ), 이 괄호는 코드 블락이 아닌 값들을 묶어서 쓸 수 있는 괄호로, 리액트를 제외하고는 거의 쓰이지 않음. 
___
## 4.6 Habit 만들기

<!-- list -->
<details>
<summary>fontawesome 추가</summary>

1. 콘솔에 yarn add @fortawesome/fontawesome-free 입력
2. index.js에  import '@fortawesome/fontawesome-free/js/all.js' 추가

</details>

___
## 4.7 State 이해하기 

#### State란?

- 컴포넌트 안에서 우리가 정의한 컴포넌트의 state 오브젝트.
- React에는 synthetic event라 해서 일반 DOM 요소에서 발생하는 이벤트 오브젝트와는 약간 다른 개념이다. -> 브라우저에서 발생한 이벤트를 그냥 쓰는 것이 아니라, 리액트는 자신만의 이벤트 클래스를 사용(synthetic event).
- state라는 오브젝트를 통해서 데이터에 업데이트가 발생시 리액트가 자동적으로 우리가 구현한 render 함수 호출.

1) state를 업데이트 할때는 반.드.시 setState() 이용해주기! (부분적 데이터 update 못 함)
2) state에 가지고 있는 오브젝트 안에는 id를 써서 key를 제공해주기(4.8에 설명)
3) 리액트에서는 state를 직접 수정하면 안된다!! 
- 리액트는 shallow comparison을 이용하므로 안에 있는 data를 수정하면 동일한 object이기 때문에 동일하다고 생각하여 업데이트를 하지 않음! → 그러므로 setState();를 써서 업데이트를 해주는 것임!
4) spread operator(...)을 이용해서 copy하기 

🎃 재밌었던 부분
1. 0 이하로 내려가지 않음
```js
handleDecrement = () => {
  const count = this.state.count - 1;
  this.setState({ count: count < 0 ? 0 : count})
}
```
<details>
  <summary> Props란?</summary>
- 컴포넌트 밖에서 주어지는 데이터 입니다.
- 재사용을 높일 수 있음.
</details>

___

## 4.8 Habits component 만들기(State up, list key)
* process 
1. 습관들을 담을 수 있는 Habits라는 컨테이너 component 만듦.
2. Habits component의 state에는 습관들을 담고 있는 array의 data를 가지고 있음. 
3. render 안에서 state에 있는 array를 map을 이용하여 각각의 습관들을 습관의 component에 연결. -> Habits component는 3개의 Habit components를 가지게 됨.
4. 각각의 Habit component 안에는 array 안에 있는 아이템들이 props로 전달. 

🧩Regex 
```js
const { name, count } = this.props.habit;
```

✅ Each child in a list should have a unique "key" prop. -> 각각의 컴포넌트에 아이디를 부여함으로써 아이디가 동일할 시에 자식 요소가 변경된 것이 아님 -> 불필요한 렌더링 하지 않음+ 성능 good
* 배열에 있는 index는 절.대 사용하면 안된다! → 순서가 바뀌게 되면 인덱스가 바뀌게 됨 (고유한 아이디만 가져야 하므로!)

## 4.9 State 업데이트 함수들 구현 

> React에서는 State를 직접 수정하면 안 된다. (spread operator 사용하기) 

- key와 value가 동일한 이름을 가지고 있는 경우, 하나로 생략 가능.
```js
this.setState({ habits: habits });
```
둘은 동일함
```js
this.setState({ habits });
```
