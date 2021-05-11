### 개발의 마무리

- 기능 점검 : 기능이 정삭적으로 작동하는지
- 디자인 점검 : 디자인이 의도한대로 잘 작성되었는지
- 코드 점검 : 성능, 개선사항 확인
- 배포
- 테스트



## pureComponent, memo 다시보기

### React.PureComponent

리액트는 state나 관련된 props가 변경이 되면 render함수가 호출이 된다.

pure.Component는 컴포넌트의 상태나 props에 변화가 없다면 render함수가 호출되지 않게 해준다.

단, state와 props를 얕게(shallow) 검사하기 때문에 주의가 필요하다. state의 **참조값**이 변하지 않으면 render가 되지 않기 때문에 `setState()` 를 사용할 때 새로운 참조값의 객체를 생성하여 넘겨주도록 하여 사용하자.

- 기존 Component

```jsx
onClickIncrement = habit => {
    const habits = [...this.state.habits];
    const index = habits.indexOf(habit);
    habits[index].count++; // 기존 객체 활용
    this.setState({ habits }); // 기존 객체를 업데이트
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
    this.setState({ habits }); // 새로운 객체를 업데이트
  };
```



### memo(function)

memo는 클래스 컴포넌트에서 pure.Component와 비슷한 역할을 한다.

컴포넌트의 상태나 props에 변화가 없다면 render함수가 호출되지 않게 해준다.

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



> **포인트**
>
> `props` 와 `state` 의 **참조값**이 변하면 리렌더링
>
> `props` 와 `state` 의 **참조값**이 변하지 않는다면 리렌더링 하지않는다.



## 주의!



함수형 컴포넌트 안에서 생성된 콜백함수를 다른 컴포넌트의 `props` 로 넘겨줄 때 주의가 필요하다.



- header.jsx

```jsx
const Header = memo(({ onLogout }) => ( // 메모 사용
  <header className={styles.header}>
    {onLogout && (
      <button className={styles.logout} onClick={onLogout}>
        Logout
      </button>
    )}
    <img className={styles.logo} src="/images/logo.png" alt="logo" />
    <h1 className={styles.title}>Business Card Maker</h1>
  </header>
));
```

`Header`의 컴포넌트에 `memo`  를 사용하여 ` onLogout` 의 참조값이 변하지 않는다면 리렌더링하지 않게한다.

하지만 계속 해서 리렌더링 되는 현상을 확인할 수 있는데 

이는 `Header` 컴포넌트를 사용하는 `Maker` 컴포넌트에서 리렌더링될 때마다 매번  `onLogout` 을 새롭게 생성하여 전달하기 때문이다.

> `Maker` 컴포넌트는 함수로 작성되어있다.
>
> 함수는 종료가 되면 콜스택에서 사라지고 메모리에서 사라진다.
>
> 따라서 함수는 실행될 때마다 콜스택에 추가되고 함수 내의 지역변수를 새로 생성한다.



- maker.jsx

```jsx
const Maker = ({ FileInput, authService, cardRepository }) => {
  ...
  
  // 일반적인 함수로 작성하면 Maker 컴포넌트 함수가 생성되면서
  // 새로운 onLogout함수를 생성하게 된다.
  const onLogout = () => { 
    authService.logout();
  };
  
  // useCallback을 사용하여 함수가 메모리에 저장되도록 한다.
  // 메모리에 저장된 함수는 메모리에서 해제되기 전까지 계속 동일한 참조값을 유지한다.
  // 단, authService의 값이 변경되었을 때는 새로운 authService의 값으로 logout해야하기 때문에
  // authService를 dependency로 하여 authService가 변경될 때마다 새로 생성되도록한다.
  const onLogout = useCallback(() => {
    authService.logout();
  }, [authService]);

  ...

  return (
    <section className={styles.maker}>
      <Header onLogout={onLogout} />
      ...
    </section>
  );
};

export default Maker;
```



## useCallback 다시보기

상위 컴포넌트와 하위 컴포넌트 모두 memo를 사용했음에도 불구하고 상위 컴포넌트에서 하위 컴포넌트의 props로 전달하는 콜백함수가 상위 컴포넌트가 호출될 때 마다 새로 생성되기 때문에 리렌더링 되는 현상을 확인할 수 있따.

`useCallback()` 을 사용하면 이런 콜백함수가 메모리에 저장되기 때문에 콜백함수가 새로 생성되어 리렌더링 되는것을 막을수 있다.

다만 `useCallback()` 을 사용한다는 것은 메모리에 콜백함수를 저장하는 것이기 때문에 꼭 필요한 경우에만 사용하여 메모리 효율성을 고려해야한다.



```jsx
const callBack = useCallback(() => {
    실행할 내용
  }, [의존할 변수(값)]);
```

deps에 의존성 변수를 설정하여 해당 변수의 값이 변경되면 새로운 함수를 생성하도록 할 수 있다.



## 성능개선

> `memo` 와 `useCallback` 불필요하게, 과하게 사용하면 성능이 저하된다.

`useCallback` 은 함수를 계속해서 메모리에 저장해두고 `memo` 는 계속해서 컴포넌트의 `props`와 `state` 를 memoization하고 있다.

따라서 메모리를 추가적으롤 사용하는 것이다.

메모이제이션을 할 필요가 없는 상황인데 굳이 사용하면 쓸데없이 메모리를 사용하기 때문에 메모리 성능면에서 좋지 않다.



- props와 state가 빈번하게 바뀌는 컴포넌트는 memo를 사용해도 어차피 계속 값이 바뀌어서 리렌더링 되기 때문에 사용을 안하는 것을 고려해볼 수 있다. (100에 95이상은 리렌더링 되는 케이스인데 5를 위해 메모를 사용하여 메모리를 낭비하는게 오히려 더 손해이지 않을까?)
- memo는 빈번하게 컴포넌트가 호출은 되지만 값의 거희 동일하게 유지되는 컴포넌트에 사용하는게 효율적이다.

