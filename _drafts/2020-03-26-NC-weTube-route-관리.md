- route들을 일원화 해서 관리하는 방법

route 들을 일원화 해서 관리하기 위한 파일을 별도로 생성(route.js)해준 후, 해당 파일을 import 해서 내부 route 값을 불러와 사용하는 방식으로 관리. 이때, route 변경이 일어날 경우 값에대한 수정을 1번만 해주기 위하여, route.js 에서는 route 별 const 변수에 값을 설정해주고, 이것에 대한 json파일(임포트 시 편의성 향상 목적, 통일성을 높여주기도 함)을 만들어 준 후, 임포트 하는 방식으로 관리함. 즉, 수정이 일어나면 const 값을 1번만 수정함으로서 다른부분의 코드에 영향을 미치지 않기위한 방법.

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

그리고 route.js에서 볼 수 있었던 또 하나의 특이사항은 /:id와 값이 변경될 수 있는 값이 자리하는 route 부분의 처리 방식이었는데, 이 때는 아래와 같은 방식으로 처리할 수 있음을 보임. 값의 존재 여부에 대한 확인을 route.js 문서에서 직접 if문을 활용해 처리.

```javascript
const USER_DETAIL = '/:id';

const routes = {
  user_detail: (id) => {
    if (id) {
      return `/users/${id}`;
    }
    return USER_DETAIL;
  },
};

export default routes;
```
