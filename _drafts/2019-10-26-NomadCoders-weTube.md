---
title: "Nomad Coders : 유튜브 클론 코딩 (1)"
classes: wide
categories:
  - Dev
tags:
  - 클론코딩
  - 프로젝트
---

## NodeJS Theory

- NodeJS 역할?
  - 브라우저 상에서 사용하던 언어인 Javascript를 브라우저 밖에서 사용할 수 있도록 해줌

- NodeJS는 언제 사용하는 것이 좋을까?
  - 많은 데이터를 움직여야 하는 경우에 NodeJS 사용을 추천. 반면 하드웨어 자원을 많이 사용해야 되는 경우에는 추천하지 않음

## ExpressJS

- ExpressJS?
  - 서버 작성을 도와주는 프레임워크

- npm?
  - nodeJS와 관련된 모든 패키지를 관리하는 프로그램
  - nodeJS를 받으면, npm도 같이 설치됨(패키지 관리를 위해)
  - npm으로 프로젝트를 시작하려면 npm이 정한 방식으로 프로젝트를 시작해야됨. 그렇기에 프로젝트 디렉터리에서 `npm init`으로 프로젝트를 시작하자. 이 명령어를 통해 package.json을 만들 수 있음.
  - 그 다음에 필요한 패키지를 npm을 통해 설치 및 관리할 수 있음. ex) `npm i express`
  - 유의사항은 npm을 package.json이 있는 곳에서 실행해야 한다는 점. 만약 없는곳에서 실행한다면, package.json을 읽지못해 새로운 package.json을 해당 디렉터리에 생성함
  - 협업자와 파일 공유시, node_modules 및 package-lock.json은 따로 공유하지 않아도 됨. 해당 디렉터리 및 파일들은 package.json이 있다는 가정하에, `npm i`를 실행하면, 자동으로 생성됨

- git
  - gitignore의 표준 [참고](https://github.com/github/gitignore/blob/master/Node.gitignore)

- nodeJS로 가장 간단한 서버 생성

```javascript
  const express = require("express");
  const app = express();

  app.listen(4000);
```

- nodeJS에서의 Route 설정 및 res 응답의 가장 간단한 예시

```javascript
  const express = require("express");
  const app = express();

  function handleHome(req, res) {
    res.send("Hello from home"); // 여기서 html, css를 리턴하면, 페이지가 보이게 되는 것
  }

  app.get("/", handleHome);

  app.listen(4000);
```

- Babel을 이용해 mordern JS 사용하기
  - Babel이란? : Javascript 문법 변환 도구
  - Babel 설치(for node) : `npm install @babel/core @babel/node`
  - Babel에는 여러개의 Presets가 있는데, 어떤 preset을 선택하느냐에 따라 mordern JS를 변환해 주는 범위가 달라짐. 수업에서는 `@babel/preset-env`를 사용
  - 설치 후, .babelrc 파일을 만들어줘서, babel 설정을 해준다.
  
  ```javascript
  // .babelrc 설정을 해주는 방법
  {
    "presets": ["@babel/preset-env"]
  }
  ```

  ```javascript
  // package.json 부분
  "scripts": {
    "start": "babel-node index.js" // node -> babel-node로 변경해서, 바벨이 트랜스파일링 할 수 있게 함
  }
  ```

- 코드 변경사항이 있을때, 자동으로 서버를 재실행 시키는 방법
  - nodemon을 활용할 것
  - 하지만 nodemon은 프로젝트에 필요한 패키지라기보다, 개발시 개발자의 편의를 위한 패키지이므로 설치시 -D 옵션을 줘서 설치하자.
    ex) `npm i nodemon -D`
    이렇게 설치해주면 아래와 같이 새로운 entrypoint(devDependencies)가 생긴것을 볼 수 있다
  
  ```json
  {
    "name": "wetube",
    "version": "1.0.0",
    "description": "Cloning Youtube with VanillaJS and NodeJS",
    "main": "index.js",
    "author": "Ross Jo",
    "license": "ISC",
    "dependencies": {
      "@babel/node": "^7.6.3",
      "@babel/preset-env": "^7.6.3",
      "express": "^4.17.1"
    },
    "scripts": {
      "start": "babel-node index.js"
    },
    "devDependencies": {
      "nodemon": "^1.19.4"
    }
  }
  ```
  
  그리고 스크립트의 명령어를 다음과 같이 수정한다.
  `nodemon --exec babel-node index.js`

  이 후 명령어를 실행하면, 코드의 수정이 있을때마다 서버가 다시 시작된다.

- middleware 사용하기
  middleware란? : 요청과 응답사이에 거치게 되는 함수. 아래는 아주 간단한 middleware의 모습이다.

  ```javascript
  import express from "express";
  const app = express();

  const betweenHome = (req, res, next) => {
      console.log("Between");
      next(); // 다음 함수로 요청을 넘겨주는 역할을 함
      // 하지만 여기서 next()와 같음 함수를 써서 연결을 진행시키지 않고, res를 이용해 응답을 해버리면, middleware를 통해 연결을 끊는 것도 가능해짐
  }

  const handleHome = (req, res) => {
      res.send("Hello from home");
  }

  app.get("/", betweenHome, handleHome);

  app.listen(4000);
  ```

  위의 방법은 `app.get("/", betweenHome, handleHome);`을 통해 "/" route에만 middleware를 추가한 것이지만, 다른 방식으로 middleware를 추가할수도 있음. 그 방법은 `app.use(betweenHome)`과 같은 방식으로 사용하는 것.

  ```javascript
  app.use(betweenHome); // 위에서 부터 아래로 실행되기 때문에, middleware를 추가할때는 순서가 중요함
  app.get("/", handleHome);
  app.listen(4000);

  // 만약 아래와 같이 middleware를 삽입한다면, middleware는 profile route에만 영향을 미치게 된다.
  app.get("/", handleHome);
  app.use(betweenHome);
  app.get("/profile", handleProfile);
  app.listen(4000);
  ```

  - morgan : morgan은 middleware중에서도 logging에 도움을 주는 middleware임
  - helmet : helmet은 보안에 도움을 주는 middleware임
  - cookie-parser : 쿠키를 이용해 세션을 다루기 위해 필요함
  - body-parser : body로 부터 정보를 얻을 수 있게 도와줌

  ```javascript
  // 아래 2줄은 bodyParser가 json 및 urlencoded 형식을 파싱해야 한다는 의미이다.
  app.use(bodyParser.json());
  app.use(bodyParser.urlencoded({extended: true}));
  ```

- 모듈을 이용해서 다른 파일에 있는 코드 공유하기

```javascript
// app.js
const app = express();
export default app;

// init.js
import app from "./app";
```

- router 사용하기.
express에서는 router를 이용해, route의 복잡성을 줄여준다

```javascript
// router.js
import express from "express";
export const userRouter = express.Router();
userRouter.get("/", (req, res) => res.send('user index'));

// app.js
import { userRouter } from "./router";
app.use("/user", userRouter);
```

CF) 위의 코드에서 userRouter를 import 할 때, { userRouter } 이와 같이 import 했는데, 이는 router.js 파일에서 export시, default로 export하지 않았기 때문에 발생하는 차이이다. default로 export하는것은 파일을 export하는 것이고, 위의 경우 처럼 export하는 것은 변수 단위 export를 한다는 의미

- MVC 패턴
M(Model) : 데이터
V(View) : 어떻게 데이터가 보여지는지
C(Controller) : 데이터를 보여주는 함수

- controller를 분리시킨 디렉터리 구조
<figure class="align-center">
  <img src="/assets/images/1026/20191026_0756.jpg" alt="controller를 분리시킨 디렉터리 구조">
</figure>

- route들을 일원화 해서 관리하는 방법

```javascript
// routes.js - URL을 관리하기 위한 파일을 별도로 생성
const HOME = "/";

const routes = {
  home: HOME,
}

export default routes;

// app.js
import routes from "./routes";
app.use(routes.home, globalRouter); // "/" -> "routes.home"
```

- 화살표 함수와 일반 함수의 차이점
화살표 함수 같은 경우는 대괄호가 없는 경우 암시적 리턴이 적용되기 때문에 따로 return을 적어주지 않아도 된다. 만약 대괄호({})를 입력한다면, 명시적으로 return을 써줘야 한다.

- Pug?
  템플릿 언어로서, express의 view engine.
  Pug를 사용하기 위해서는 express의 view engine의 set부분을 pug로 바꿔줘야 함. `app.set("view engine", "pug");`
  한편, pug과 express에는 view 파일들의 위치에 대한 기본 설정이 있는데, 만약 그 설정을 바꾸고 싶다면 'views'의 설정을 바꿔주면 됨.
  html 파일을 저장해야 하는 폴더의 기본값은 "프로젝트의 작업 디렉토리 + /views"
  pug 파일의 사용법은 아래와 같다.
  
  ```javascript
  //views 디렉터리에 home.pug가 있다고 가정
  export const home = (req, res) => res.render("home");
  // 위의 코드에서 render 함수가 views 폴더에서 파일명이 home이고 확장자가 pug인 템플릿 파일을 찾은후에 보여줌
  ```

  pug를 사용하면, 레이아웃 코드를 모듈화해서 관리할 수 있다.
  탭 안쪽에 있는 코드라면, 무언가의 내부에 있는 코드라는 의미
  일반 html 처럼 열고 닫는 태그가 필요하지 않으며, 들여쓰기(탭 or 스페이스 4개)로 태그를 구분함. 들여쓴 칸을 다시 되돌아 오는 식으로 닫는 태그를 표현하기 때문에 앞서 언급한 것처럼 닫는태그가 불필요. 같은 indent 상에 있다면 포함관계가 아님. 아래는 예시 pug 코드

  ```pug
  doctype html
  html
      head
          title Wetube
      body
          header
              h1 WeTube
          main
              block content
          footer
              span &copy; WeTube
  ```

  위쪽을 보면 block 이란 부분이 보이는데, block은 레이아웃에서 실제 내용이 들어가는 부분. layout에 block이 없으면, 내용을 볼 수 있는 창이 없는 것이나 마찬가지기 때문에 block이 없을시 템플릿이 제대로 동작하지 않는다. block에는 이름을 붙일 수 있는데 위의 코드에서는 content라고 이름을 붙인 것

  생성한 레이아웃을 사용하는 방법은 아래와 같다.

  ```pug
  extends layouts/main

  block content
      p hello!
  ```

  만들어진 레이아웃을 확장하여 block의 내용을 작성하는 코드이다. 현재까지의 디렉터리 구조는 아래와 같다.
  <figure class="align-center">
    <img src="/assets/images/1026/20191026_1218.jpg" alt="디렉터리 구조">
  </figure>

- partials 
템플릿을 분리해서 관리하는 방법의 하나. 아래는 partials을 사용한 예시이다.

```pug
//- main.pug
body
  header
      h1 WeTube
  main
      block content
  include ../partials/footer //- include를 활용해 import 함

//- footer.pug
footer.footer
  footer__icon //- div를 추가할때는 클래스명 앞에 dev.와 같이 적지 않아줘도 됨
      i.fab.fa-youtube
  span.footer__text &copy; #{new Date().getFullYear()} WeTube //- pug에서 어떻게 js를 활용했는지 주목
```

- fontawesome에서 폰트를 설치하는 방법

```pug
head
  <script src="https://kit.fontawesome.com/4533bb54b2.js" crossorigin="anonymous"></script>
  script(src="https://kit.fontawesome.com/4533bb54b2.js", crossorigin="anonymous")
```

위의 방식으로 하나, 아래의 방식으로 하나 다 잘 동작한다.

- pug에서 local variable을 사용하는 방법
local middleware를 사용해 준다. 이 방법을 활용하면 local 변수를 global 변수처럼 활용할 수 있게 된다.

```javascript
// app.js
app.use(localsMiddleware); // 미들웨어의 위치에 주목하자. 미들웨어의 위치에 따라 사용을 못할수도 있다.

app.use(routes.home, globalRouter);
app.use(routes.users, userRouter);
app.use(routes.videos, videoRouter);

// middlewares.js
import routes from "./routes";

export const localsMiddleware = (req, res, next) => {
    res.locals.siteName = "WeTube";
    res.locals.routes = routes;
    next();
}
```

```pug
//- main.pug
  head
    script(src="https://kit.fontawesome.com/4533bb54b2.js", crossorigin="anonymous")
    title #{siteName}

//- footer.pug
header.header
  .header__column
      a(href=routes.home)
      i.fab.fa-youtube
```
  
- pug에서의 템플릿 변수사용하기
render함수를 이용하면 된다. render 함수의 첫번째 인자는 템플릿이고, 두번째인자는 템플릿에 추가할 정보가 담긴 객체이기 때문

```javascript
// videoController.js
export const home = (req, res) => res.render("home", {pageTitle : "home"});
```

```pug
//- main.pug
title #{pageTitle} | #{siteName}
```

- GET method를 이용해 입력한 검색어를 다른 페이지로 넘기는 방법

```pug
//- header.pug
.header__column
  form(action=routes.search, method="get") //- get method를 써야, url에 정보를 넘긴다. .
      input(type="text", placeholder="Search by term...", name="term")

//- search.pug
extends layouts/main

block content
    .search__header
        h3 Searching for: #{searchingBy}
```

```javascript
// videoController.js
export const search = (req, res) => {
    const {query : { term : searchingBy }} = req; // = const searchingBy = req.query.term;
    res.render("search", {pageTitle : "Search", searchingBy});
};
```

`const {query : { term : searchingBy }} = req;` 는 es6 방식의 표현방법이다.

- HTML을 재활용하는 방법 : mixin
mixin은 pug에서 html을 재활용하기 위한 방법. mixin은 pug함수의 일종. 아래는 mixin 활용법

```pug
//- mixins/videoBlock.pug
mixin videoBlock(video = {})
  .videoBlock
      video.videoBlock__thumbnail(src=video.videoFile, controls=true)
      h4.videoBlock__title=video.title
      h6.videoBlock__views=video.views

//- home.pug
extends layouts/main.pug
include mixins/videoBlock

block content
    .videos
        each item in videos
            +videoBlock({
                title: item.title,
                views: item.views,
                videoFile: item.videoFile
            })
```

- HTML에서 값에 따라 변경되는 부분(ex: /:id)에 실제 값 넣어주기

```javascript
// routes.js
const routes = {
  user_detail: id => {
      if (id) {
          return `/users/${id}`;
      } else {
          return USER_DETAIL; // const USER_DETAIL = "/:id";
      }
  }
}

// userRouter.js
userRouter.get(routes.user_detail(), userDetail);
```

```pug
//- header.pug
a(href=routes.user_detail(user.id)) Profile
```

위와 같이 해줘야 브라우저에서 제대로 된 링크를 볼 수 있음

- 특정 input 태그에 값이 필수적으로 있어야 하는 경우
`input(type="text", name="name", placeholder="Full name", required=true)` 와 같이 required=true를 사용해준다.

- 특정 파일 형식만 받고자 하는 경우 아래의 예와 같이 accept 필드를 활용
`input(type="file", id="file", name="file", required=true, accept="video/*")`

- 파일을 전송해야 해서 인코딩 타입이 달라야 하는 경우의 한가지 예시
`form(action=``/videos${routes.upload}``, method="post", enctype="multipart/form-data")`

## MongoDB

- Mongoose
mongoDB와 JS를 연결시키는 Adapter. 아래는 mongoDB와의 커넥션 부분.

```javascript
  import mongoose from "mongoose";

  mongoose.connect("mongodb://localhost:27017/we-tube",
      {
          useNewUrlParser: true,
          useFindAndModify: false
      }
  );

  const db = mongoose.connection;

  const handleOpen = () => console.log("Connected to DB");
  const handleError = error => console.log(`Error on DB Connection: ${error}`)

  db.once("open", handleOpen);
  db.on("error", handleError);
```

- dotenv
노출되지 말아야 할 정보들을 보통 이 곳에 기록함
dotenv.config 함수로 .env 파일 안에 있는 정보를 불러올 수 있음
그리고 찾은 모든 variable들을 process.env.key에 저장.
아래는 dotenv 사용 예시이다
(gitignore에 dotenv파일을 올려서 dotenv에 있는 정보들이 깃허브에 올라가지 않도록 할 것)

```javascript
// db.js
import dotenv from "dotenv";
dotenv.config();

mongoose.connect(
    process.env.MONGO_URL,
    {
        useNewUrlParser: true,
        useFindAndModify: false
    }
);
```

- mongoDB 사용법에 대하여
몽고 DB에 데이터를 저장하기 위해서는 데이터의 형태를 정의해야 한다. 아래는 그 예시이다.

```javascript
// Video.js
import mongoose from "mongoose";

const VideoSchema = new mongoose.Schema({
    fileUrl: {
        type: String,
        required: "File URL is required", // 해당 필드값을 입력하지 않았을때, 나타나는 ERROR 메시지
    },
    title: {
        type: String,
        required: "Title is required"
    },
    description: String, // 옵션이 아니면 object로 만들지 않아도 됨. 옵션이 있는 경우 아래와 같이 object로 만들 것
    views: {
        type: Number,
        default: 0
    },
    createdAt: {
        type: Date,
        default: Date.now // Date.now는 현재시간을 알려주는 함수이다. 이렇게 적어 놓으면, 데이터 저장시 해당 함수가 실행되어 현재 시간이 저장된다.
    }
})

const model = mongoose.model("Video", VideoSchema);
export default model;

// init.js
import "./models/Video"; // 그리고 만든 스키마를 이렇게 불러와야(import) 몽고 디비에서 인식한다.
```

몽고DB 에서는 데이터 간 관계를 표시하는 방식이 조금 독특한데, 그 방식은 아래와 같다.

```javascript
const VideoSchema = new mongoose.Schema({
    // ...
    comments: [{ // 아이디의 배열이 들어갈 것이기 때문에 이렇게 사용
        type: mongoose.Schema.Types.ObjectId,
        ref: "Comment"
    }]
    // ...
})
```

- async 함수를 사용례

```javascript
  export const home = async (req, res) => {
      try {
          const videos = await Video.find({}); // await에 의해 기다리다가 성공적으로 끝나던, 실패하던 일단 끝나면 다음으로 넘어간다.
          res.render("home", {pageTitle : "home", videos});
      } catch (error) { // 이와 같이 catch 블록이 없으면, ERROR가 발생하면 제대로 처리를 해주지 못하게 되어, 제대로 사이트를 표시할 수 없게 된다.
          console.log(error);
          res.render("home", {pageTitle : "home", videos: []});
      }
  }
```

- multer
파일을 업로드 하고 업로드한 곳의 URL을 반환해 주는 패키지. 해당 도구를 사용하기 위해서는 middleware에 multer를 활용한 코드를 작성해준 뒤

```javascript
  // middlewares.js
  import routes from "./routes";
  import multer from "multer";

  const multerVideo = multer({dest: "videos/"}); // multer가 올린 파일의 저장 위치가 되는 곳이다.

  export const localsMiddleware = (req, res, next) => {
      res.locals.siteName = "WeTube";
      res.locals.routes = routes;
      res.locals.user = {
          isAuthenticated: true,
          id: 1
      }
      next();
  }

  export const uploadVideo = multerVideo.single('videoFile'); // multer가 한번에 하나의 파일만 올린다는 의미. videoFile은 해당 파일을 업로드 하는 HTML필드의 name
```

이후 만들어준 미들웨어를 라우터에 넣어 활용하는데, 아래와 같이 해준다.

```javascript
// videoRouter.js
videoRouter.post(routes.upload, uploadVideo, postUpload);
```

이후 multer가 넘겨준 정보를 통해, 해당 정보중 필요한 부분을 취사선택하여 DB에 저장한다.

```javascript
// videoController.js
export const postUpload = async (req, res) => {
    const {
        body: { title, description },
        file: { path }
    } = req;
    console.log(title, description, path);
    const newVideo = await Video.create({
        fileUrl: path, // 파일 자체가 아니라, location을 저장한다는 점에 주의
        title,
        description
    });
    res.redirect(routes.video_detail(newVideo.id));
}
```

- 디렉터리에서 파일을 보내주는 middleware로서 express.static()이 존재

```javascript
app.use("/uploads", express.static("uploads")); // 하지만 이런식으로 직접 로컬 서버에 업로드 하는 방식이 좋은 방식은 아님
```

보통 static 파일은 front-end에서 쓰이는 javascript, css, logo file 등
유저가 생성한 파일은 별도의 서버에 저장되어야 함

- mongo DB에서 데이터를 검색하는 방법

```javascript
    videos = await Video.find({
      title: { $regex: searchingBy, $options: 'i' }, // $options: 'i'는 검색조건을 대소문자에 민감하지 않도록 설정한다는 의미
    });
```

- webpack
모듈 번들러

수많은 파일들을 가져와서 웹펙에게 던져주면 웹팩은 그것을 완전한 호환성을 가지는 static 파일들로 변환해서 줌

실행 스크립트에서 옵션을 줘서 그 옵션을 웹펙에 불러오기 위한 방법

```json
  "scripts": {
    "dev:server": "nodemon --exec babel-node init.js",
    "dev:assets": "WEBPACK_ENV=development webpack -w", // -w 파일의 변화를 지켜보며 변화가 있을 경우 반영한다는 의미이다
    "build:assets": "WEBPACK_ENV=production webpack"
  }
```

```javascript
  // webpack.config.js
  const MODE = process.env.WEBPACK_ENV;
```

웹펙으로 static 파일들을 생성한 다음에는 반드시 서버에 해당 디렉터리 (static)에 대해서 알려줘야 한다.

```javascript
  // app.js
  app.use('/static', express.static('static'));
```

웹펙 파일 예시

```javascript
// 100% 클라이언트 코드기 때문에(바벨을 쓸 수 없음), 옛날 자바 스크립트 코드를 써야 함(모던 자바스크립트가 아님)

const path = require('path'); // 이전 방식의 파일 임포트 방법
const autoprefixer = require('autoprefixer');
const ExtractCSS = require('extract-text-webpack-plugin');

const MODE = process.env.WEBPACK_ENV;
const ENTRY_FILE = path.resolve(__dirname, 'assets', 'js', 'main.js'); // __dirname : 프로젝트 디렉터리 이름(언제 어디서든 접근 가능한 nodeJS 전역변수)
const OUTPUT_DIR = path.join(__dirname, 'static');

const config = {
  entry: ['@babel/polyfill', ENTRY_FILE],
  mode: MODE, // 개발모드인지 프로덕션모드인지 구분
  module: {
    rules: [
      {
        test: /\.(js)$/,
        use: [
          {
            loader: 'babel-loader',
          },
        ],
      },
      { // 웹펙에서는 아래에서 부터 위로 실행함
        test: /\.(scss)$/,
        use: ExtractCSS.extract([
          {
            loader: 'css-loader',
          },
          {
            loader: 'postcss-loader',
            options: {
              plugins() {
                return [autoprefixer({ browsers: 'cover 99.5%' })];
              },
            },
          },
          {
            loader: 'sass-loader',
          },
        ]),
      },
    ],
  },
  output: {
    path: OUTPUT_DIR,
    filename: '[name].js',
  },
  plugins: [new ExtractCSS('styles.css')], // 저장할 파일의 이름을 넣어줌
};

module.exports = config;

```

- passport를 통한 인증
passport 가 하는 일 : 쿠키 생성, 브라우저 저장, 유저에게 쿠키 줌

serialization : 어떤 정보를 쿠키에게 줄꺼냐. 하지만 쿠키는 작으면 작을수록 좋고, 민감한 정보는 담지 않는편이 좋다

deserialization : 어떤 유저인지 어떻게 찾는거냐에 관한 얘기
