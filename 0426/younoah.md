## useCallback

상위 컴포넌트와 하위 컴포넌트 모두 memo를 사용했음에도 불구하고 상위 컴포넌트에서 하위 컴포넌트의 props로 전달하는 콜백함수가 상위 컴포넌트가 호출될 때 마다 새로 생성되기 때문에 리렌더링 되는 현상을 확인할 수 있따.

`useCallback()` 을 사용하면 이런 콜백함수가 메모리에 저장되기 때문에 콜백함수가 새로 생성되어 리렌더링 되는것을 막을수 있다.

다만 `useCallback()` 을 사용한다는 것은 메모리에 콜백함수를 저장하는 것이기 때문에 꼭 필요한 경우에만 사용하여 메모리 효율성을 고려해야한다.



## axios

fetch web api를 사용하면 `fetch()` 함수로 간단하게 네트워크 통신을 할 수 있다.

하지만 `fetch()` 함수는 우리가 일일이 url, param, json변환을 해줘야 하기 때문에 반복되는 작업이 필요하다.

axios라이브러리는 이런 반복작업을 줄이고 좀 더 직관적으로 통신요청을 할 수 있도록 돕는다.

> **axios**
>
> https://github.com/axios/axios
>
> - axios 추가
>
> ```shell
> $ yarn add axios
> ```
>



- fetch API

```jsx
class Youtube {
  constructor(key) {
    this.key = key;
    this.getRequestOptions = {
      method: 'GET',
      redirect: 'follow',
    };
  }

  async mostPopular() {
    const response = await fetch(
      `https://www.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&maxResults=25&key=${this.key}`,
      this.getRequestOptions
    );
    const result = await response.json();
    return result.items;
  }

  async search(query) {
    const response = await fetch(
      `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=25&q=${query}&type=video&key=${this.key}`,
      this.getRequestOptions
    );
    const result = await response.json();
    return result.items.map(item => ({ ...item, id: item.id.videoId }));
  }
}
```



- axios

```jsx
import axios from 'axios';

class Youtube {
  constructor(key) {
    this.youtube = axios.create({
      baseURL: 'https://www.googleapis.com/youtube/v3',
      params: {key: key}
    });
  }

  async mostPopular() {
    const response = await this.youtube.get('videos', {
      params: {
        part: 'snippet',
        chart: 'mostPopular',
        maxResults: 25,
      },
    });
    return response.data.items;
  }

  async search(query) {
    const response = await this.youtube.get('search', {
      params: {
        part: 'snippet',
        maxResults: 25,
        type: 'video',
        q: query,
      },
    });
    return response.data.items.map(item => ({ ...item, id: item.id.videoId }));
  }
}
```

포스트맨에서 요청 옵션을 직관적으로 설정할 수 있고, 반환값을 json으로 받는다.



## dependency injection

https://medium.com/@jang.wangsu/di-dependency-injection-%EC%9D%B4%EB%9E%80-1b12fdefec4f



내가 만든 모듈이 현재 진행하는 프로젝트뿐만 아니라 다른 곳에서도 사용할 수 있도록 결합도를 낮춘다.

결합도를 낮추기위해서 핵심으로 동작하는 기능은 모듈에 전달하는 식으로 구현한다. (**dependency injection**)



```jsx
class Youtube {
  constructor(httpClient) {
    this.youtube = httpClient;
  }

  async mostPopular() {
    const response = await this.youtube.get('videos', {
      params: {
        part: 'snippet',
        chart: 'mostPopular',
        maxResults: 25,
      },
    });
    return response.data.items;
  }

  async search(query) {
    const response = await this.youtube.get('search', {
      params: {
        part: 'snippet',
        maxResults: 25,
        type: 'video',
        q: query,
      },
    });
    return response.data.items.map(item => ({ ...item, id: item.id.videoId }));
  }
}
```

