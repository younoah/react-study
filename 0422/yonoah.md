## MVC(S) 디자인패턴

![mvcs](/Users/uno/Desktop/mvcs.png)

- store -> model
- **뷰**는 뷰와 관련된 처리를 담당한다 (렌더링)
- **모델**은 데이터의 엔티티 정의 혹은 store역할을 한다.
- **컨트롤러** 는 뷰와 모델을 중개하며 조작한다.
- **서비스**는 컨트롤러에 필요한 부가적인 기능을 정의한다.



## 리엑트의 Environment Variables

리엑트에서 프로젝트를 하다보면 API키와 같은 중요한 정보를 깃헙에 업로드 하고 싶지 않을때가 있다.

이 경우 리엑트에서 제공하는 Environment Variables를 활용해서 전역변수를 관리하고 해당 전역변수를 담고 있는 파일을 깃헙에 올리지 않는 방식으로 중요한 정보를 지킬수 있다.

- 프로젝트의 최상위에서 `.env` 파일 생성

- 아래와 같은 형태로 전역변수를 설정한다.

```
REACT_APP_YOUTUBE_API_KEY=키
```

`REACT_APP_` 은 고정적으로 앞에 붙어서 사용하는것이 약속이다.

- 아래와 같이 전역변수를 사용한다.

```jsx
// in js or jsx
process.env.REACT_APP_YOUTUBE_API_KEY
```

- gitignore에 `.env` 파일을 추가하여 중요한 변수를 업로드하지 않게 한다.



## 기타

- API요청과 관련된 메서드는 `async await` 키워드를 사용하면 가독성이 높아진다. 또한 이 메서드를 비동기적으로 사용할 때도 유용할 것같다.

	