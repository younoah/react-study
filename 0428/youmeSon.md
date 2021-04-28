## Firebase 

✅ 1. Build better web - 어플리케이션을 만들때 굉장히 빠르게 만들 수 있게 하는 기능
  * e.g. Authentication, Cloud Firestore(사용자의 정보 저장), Cloud Storage(사용자의 정보 저장), RealTime Database
  
✅ 2. Improve app quality 
  * e.g. Crashlytics(에러, crash로 프로그램이 죽으면 발생한 경로같은 것을 개발자들에게 보여주는 crash reporting system) - 에러 발생율 🔻, 안정성있는 개발 🔺
  * e.g. Performance Monitoring(성능 모니터링), Test Lab, App Distribution(만든 버전대로 배포) 

✅ 3. Grow your business
  * e.g. Google Analytics(사용자가 웹페이지에서 쓰는 기능 추적)
  * e.g. in-app messaging(사용자에게 마케팅같은 것 관련 메시지)

#### SDK란? 

 ⭕ Software development kit (개발할때 필요한 kit)
  
😍 장점: 
 파이어베이스에서 제공하는 SDK를 라이브리처럼 포함해 놓으면 SDK에서 제공하는 API함수들만 이용시 SDK가 알아서 파이어베이스와 통신을 함. 
 
 😨 단점: 
 dependence가 하나 더 추가! 
 
 ## Cloudinary 
 
 ✅ 사용자의 사진 upload, Resizing, Transformation, Filtering
 
 * Cloudinary에 업로드 되면, 서버에 이미지가 저장되고 -> 다른 PC나 다른 브라우저에서 로그인 됐을 때 확인 가능.
 
 ✨파이어베이스의 cloud storage와 다른점? 
  * 파이어베이스 - 오리지널 사진
  * Cloudinary - 사이즈 재조정, 다양한 edit 가능, 좀 더 효율적이고 personalized하게 할 수 있음! 
 
 🌞Transformation을 이용하여 업로드 시킬 사진의 사이즈 부터 다양한 것 수정 가능! 
 
 💥REST.API -> GET, POST, PUT, DELETE
 
 ✅ HTTPS POST request 이용 
 ```js
const url = "https://api.cloudinary.com/v1_1/demo/image/upload"; //1. demo : cloud name, imgage: resource type 
const form = document.querySelector("form");

form.addEventListener("submit", (e) => {
  e.preventDefault(); //2. submit이 되면 

  const files = document.querySelector("[type=file]").files;
  const formData = new FormData(); //3. formData라는 오브젝트를 이용해서 POST request로 보낼 body 부분 생성 

  for (let i = 0; i < files.length; i++) {
    let file = files[i];
    formData.append("file", file); // 4. file과 upload preset 추가(docs 읽어보면 나와 있음)
    formData.append("upload_preset", "docs_upload_example_us_preset");

    fetch(url, { // POST method를 이용하여 fetch 
      method: "POST",
      body: formData
    })
      .then((response) => {
        return response.text();
      })
      .then((data) => {
        document.getElementById("data").innerHTML += data;
      });
  }
});
```

## Post CSS 변수 선언 

1. src폴더 안에 common이나 base라는 폴더/파일 만들기 
2. @value key: value; 로 정의. 
```css
@value makerBlack: black;
```
3. 사용하고자 하는 CSS파일에서 사용법 
```css
@value makerBlack from "./common/colors.css";
```
> 파일을 찾지 못할때는 간단히 /src 로 시작하기 
```css
@value makerBlack from "/src/common/colors.css";
```
