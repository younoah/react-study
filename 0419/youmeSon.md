## 1) Github

1. Github에서 repository를 만든다
2. git remote add origin git@github.com: 이렇게 적힌 것 cmder 안에 붙여넣기 
3. (지금까지 작업한 파일이 있다면) git add*
4. git commit -m "작업한 내용 작성하기"
5. git push -u origin master

❗ 만약에 문제가 생기면 git config --list를 쳐서 열어보기 → 그래서 {user} 정보가 정확하게 작성되어 있는지 확인하기(그리고 없다면 이것을 넣어주기)

--> 그 다음 GitHub Pages를 마스터 브랜치로 설정 (주소 받기)
1. 받은 주소로 package.json에 가서 homepage에 넣기 
2. gh-page를 install해야 함 (yarn add gh-pages)
3. add deploy to scripts in package.json
```jsx
"predeploy": "npm run build",
"deploy": "gh-pages -d build",
```
4. yarn build를 한다. -> 사용자에게 배포할 수 있는 소스코드가 만들어짐 (끝나고 나면 build라는 폴더가 만들어짐)


<details>
<summary>5. npm run deploy</summary>
  
  👺  "fatal: remote origin already exists”
  > git remote set-url origin https://[id]:[password]@github.com/youmeSon/[repository이름]
  
 </details>
 
6. project page를 gh-pages branch로 바꾸기 

😈 주의 - 현재는 리액트라우터에서 제공하는 브라우져 히스토리가 지원되지 않음 -> 이럴때는 다른 배포법 이용하는 게 더 나을듯 

## Netflify

1. npm을 이용해서 Netlify 파일을 글로벌적으로 설치 (npm install netlify-cli -g)
2. 프로젝트 폴더 안에서 간단히 netlify deply 호출
3. 승인 요청 페이지 나옴 -> 허락 
4. Link this directory to an existing site or create & configure a new site -> 새로운 것 선택 
5. team은 간단히 이름 적기 : youme's team
6. site name 적기
7. publish directory -> build 폴더이므로 build라고 치기 
8. deploy 됨(제공된 링크 클릭하면 됨)

😈 주의 - 깃헙에서만 동작하는 package.json의 hompage가 들어가 있지 않도록 할 것! 

## PostCSS 

- PostCSS같은 아이들은 CSS의 전처리기라고 부른다 → 반복적이고 중복되는 코드 작성을 최소화
- LESS or SASS 같은 아이들(얘들이 제공하는 것만 써야 함)

1) 많은 사람들이 공통적으로 사용하고 있다.
2) 모듈화가 되어 있기 때문에 BEM같이 복잡한 이름 명을 쓰지 않아도 정말 간편하게 관리할 수 있음 
3) 우리가 필요할 때 더 확장하여 나갈 수 있다. 
4) prefix 필요없음. -> auto prefix
5) 다양한 플러그인 함께 사용 가능 -> 우리가 필요할 때 확장해 나갈 수 있음.

## PostCSS로 모듈화 이용하기 

각각 다른 컴포넌트에 각각 다른 CSS 파일이지만 동일한 클래스 이름을 이용할 경우 !
같은 색으로 나옴 → button2가 나중에 읽혀지면서 기존의 button1의 색상을 오버라이팅함

- 해결책

1) BEM을 이용하여 class이름을 더 자세히 나눠주기.(일반 CSS)
2) PostCSS를 이용하여 모듈화를 만든다. (편리함!)
```js
<div className={styles.container}>
        <Editor cards={cards} />
        <Preview cards={cards} />
      </div>
```
✅ import styles from './button.module.css'; 잊지 않기 

✅ className에도 {styles.container}로 표시 -> postCSS에서 후처리가 되어 자동으로 생성된 이름이기 때문에 styles를 통해서 접근.

🎃 개발툴을 열어서 보면 자동적으로 컴포넌트에 모듈화가 된 것을 확인할 수 있다. 

