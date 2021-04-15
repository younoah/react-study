## 리엑트의 컴포넌트 만드는 방식

리엑트에서 컴포넌트를 만들때 클래스로 만드는 방법과 함수로 만드는방법 2가지가 존재한다.



### 클래스로 만드는 컴포넌트

**컴포넌트에 상태가 존재하고 그 상태에 따라 컴포넌트가 주기적으로 업데이트 되어야 한다면 클래스 컴포넌트를 사용하자.**

- 상태가 존재한다.
- 라이프사이클 메서드가 존재한다.
- 상태의 변화에 따라 렌더함수가 실행된다.

```js
import React from 'react';

class LikeButton extends Component {
    state = { // 라이프사이클 메서드
      numberOfLikes: 0,  
    };
	render() {
        return <button>
            {this.state.numberOfLikes}
        <button>;
    }
}
```

> 라이프 사이클 메서드란?
>
> 컴포넌트가 사용자에게 보여질 때,
>
> 돔트리에 올라갔을 때,
>
> 돔트리에서 나왔을 때,
>
> 컴포넌트가 업데이트 되었을 때, 등등
>
> 다양한 컴포넌트의 상태에 따라가 리액트가 알아서 호출해주는 메서드이다.



### 함수로 만드는 컴포넌트

**컴포넌트에 상태가 없고 항상 정적으로 데이터가 표기가 된다면 함수 컴포넌트를 이용하자.**

- 함수는 한 가지만의 동작만 하는 작은 단위이다. 
- 상태가 없다.
- 라이프사이클 메서드가 없다.
- 단, 리엑트 훅을 도입하면 클래스 컴포넌트처럼 사용이가능하다. (함수 컴포넌트안에서도 상태를 가질 수 있다.)

```js
function App() {
    return <h1>Hello</h1>
}
```

> 리엑트 훅
>
> 함수에 비해 클래스가 어렵기 때문이다. 
>
> this를 항상 사용해야하고 this바인딩 이슈가 있기 때문이다.
>
> 함수형 프로그래밍에 대한 니즈로 탄생했다.
>
> 둘다 같지만 선호도에 따라 선택해서 사용한다. 
>
> 클래스컴포넌트 함수 컴포넌트 둘다 이해하는 것이 중요하다.



## 템플릿 프로젝트 만들기

`create react-app` 이라는 툴을 이용하면 내가 잘 사용하지 않는 부가적인 것들이 많이 생겨 난다. 따라서 내가 딱 필요한 세팅의 템프릿 프로젝트를 미리 만들어 놓고 새로운 프로젝트를 할 때 이 템플릿 프로젝트를 복사해서 사용하면 빠르고 편하게 프로젝트를 시작할 수 있다.



**템플릿 프로젝트 구조**

```shell
public # 정적인 요소(html,이미지 같은 리소스들)
├── favicon.ico
└── index.html

src # 동적인 요소(자바스크립트코드 같은 소스들)
├── app.css
├── app.jsx
├── components
├── index.css
└── index.js
```



- 템플릿 프로젝트를 새로운 프로젝트로 복사하기

```shell
cp -R template 프로젝트명
```



## react-dom

결국 유저에게 보여지는 것은 index.html이다.

우리가 만든 리액트 컴포넌트들을 바벨로 순수 자바스크립트로 바꾸고 HTML과 연결하는 작업이 필요하다.

react-dom은 리액트로 작성한 컴포넌트를 HTML과 연결하는 역할을 한다.

- index.html

```html
<!DOCTYPE html>
<html>
  <head>
	...
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```



- app.js

```jsx
import React from 'react';

function App() {
    return <h1>Hello :)</h1>;
}

export default App;
```



- index.js

```jsx
import ReactDOM from 'react-dom';
import App from './app';

ReactDOM.render(
	<React.StrictMode> //리엑트의 엄격모드, 생략가능
    	<App />
    </React.StrictMode>,
    document.getElementById('root')
);

```

우리가 만든 App이라는 최상위 컴포넌트를 index.html에 있는 root라는 아이디를 가지 요소에 연결하여 렌더링한다.



## 디버깅툴



![2021-04-15_15-32-58](/Users/uno/Desktop/2021-04-15_15-32-58.png)

- 컴포넌트의 구조
- props
- state

등을 확인할 수 있다.



## jsx란?

자바스크립트안에서 html마크업을 사용할수 있는 자바스크립트의 확장버전이다.



- 태그의 속성이 약간 다르다.

```jsx
// in html
<h1 class="title" onclick="">hello!</h1>

// in jsx
<h1 className="title" onClick="">hello!</h1>
```

class는 className으로, onclick은 onClick으로 작성해야한다.



- 형제 노드를 사용하려면 묶어주어야한다.

```jsx
// 리턴 값으로 2개 이상의 요소를 반환할 수 없다
function App() {
    return (
        <h1></h1>
        <h1></h1>
    );
}

// 아래와 같인 묶어서 하나의 요소로 반환해야 한다.
function App() {
    return (
        <div>
            <h1></h1>
            <h1></h1>
        </div>
    );
}

// 또는

function App() {
    return (
        <React.Fragment>
            <h1></h1>
            <h1></h1>
        </React.Fragment>
    );
}

// 또는

function App() {
    return (
        <>
            <h1></h1>
            <h1></h1>
        </>
    );
}
```



- 마크업과 자바스크립트를 자유롭게 사용할 수 있다.

```jsx
function App() {
    const name = younoah;
    return (
        <>
            <h1>hello</h1>
        	{name && <h1>{name}</h1>}
        </>
    );
}
```



## SyntheticEvents

리액트에서의 event는 일반 DOM요소에서 발생하는 이벤트 객체와는 약간 다르다.

리액트는 리액트 나름대로 이벤트를 한 단계 더 감싸는 자신만의 이벤트 클래스를 사용한다. 그것이 SyntheticEvents 이다.

일반적인 DOM요소에서 발생하는 이벤트를 사용하는것처럼 사용할 수 있다.



## setState()

리액트의 Component에서 제공하는 setState함수를 사용하여 상태를 변경해주어야 비로소 상태가 변경된 것을 인식하고 렌더링을 한다.

```jsx
import React, { Component } from 'react';

class Habbit extends Component {
  state = {
    count: 0,
  };

  handleIncreament = () => {
    // state 오브젝트 안에 있는 count를 증가 한 뒤 state를 업데이트 해야 함

    // 아래와 같은 방식으로 사용하면 리액트는 상태가 업데이트 되었는지 모른다.
    // this.state.count += 1;

    // 리액트의 Component에서 제공하는 setState함수를 사용하여 상태를 변경해주어야
    // 비로소 상태가 변경된 것을 인식하고 렌더링을 한다.
    this.setState({ count: this.state.count + 1 });
  };


  render() {
    return (
      <>
        <li className="habit">
		  ...
          <button
            className="habit-button habit-increase"
            onClick={this.handleIncreament}
          >
          ...
        </li>
      </>
    );
  }
}

export default Habbit;

```

setState()에 객체를 넘겨주는 형태로 사용하는 점을 눈여겨 보자.



## state와 props

### state

컴포넌트 안에서 정의한 state 객체이다.

컴포넌트의 데이터를 보관하기 위한 객체로, state 객체를 통해서 데이터 업데이트가 발생하면 render함수가 호출된다.

> 리액트에서 state객체는 불변객체로 사용함으로써 얘기치 못한 에러를 방지하자.



### props

컴포넌트 밖에서 주어지는 데이터이다.

재사용을 높이기 위해 데이터를 외부에서 받아 데이터에 맞게 UI를 보여주기 위해 사용된다.

아래와 같이 부모 컴포넌트에서 `LikeButton` 컴포넌트를 사용할 때 title, onClick 과 같은 인자로 전달해주면  `LikeButton` 컴포넌트의 props라는 객체로 묶여서 전달된다.

```jsx
<LikeButton title={'Like'} onClick={this.handleClick} />
```



## 자바스크립트 문법

### 구조분해 할당

```js
// 배열
let arr = ["Bora", "Lee"];
let [firstName, surname] = arr;
console.log(firstName); // "Bora"
console.log(surname); // "Lee"
console.log(arr); // ["Bora", "Lee"], 원본 보존

// 객체
let options = {
  title: "Menu",
  width: 100,
  height: 200
};
let {title, width, height} = options;
console.log(title); // "Menu"
console.log(width); // 100
console.log(height); // 200
```



### 전개문법

```js
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

console.log([...arr1, ...arr2]); // [1, -2, 3, 4, 8, 3, -8, 1]
```





## 키워드

### 클래스 컴포넌트

- React.Component
- React.PureComponent



### 함수형 컴포넌트

- function
- memo(function)
- React Hook



### 기타

- JSX
- State
- Props
- SyntheticEvents
- refs
- Lifecycle methods
- List and keys
- developer tools
- techniques