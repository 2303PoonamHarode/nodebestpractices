# 리버스 프록시나 미들웨어를 사용해서 페이로드 크기를 제한해라

### 한문단 요약

JSON 인코딩 페이로드와 같은 요청구문은 요청의 크기가 클수록 높은 성능이 요구된다.
웹 애플리케이션에서 요청을 처리할 때 페이로드의 크기를 제한해야 한다.
요청의 크기를 제한하지 않으면 서비스 거부 또는 기타 원치 않는 부작용으로 인해 애플리케이션의 성능이 저하되거나 충돌이 생긴다.
Express를 위한`body-parser`와 같은 요청문 분석을 위한 인기 있는 미들웨어의 요청 페이로드 크기 제한 옵션을 사용하여 개발자는 쉽게 요청의 크기를 제한할 수 있다.
당신은 리버스 프록시 또는 웹서버 소프트웨어가 지원한다면 해당 기능을 통해 요청문의 크기제한을 통합할 수 있다.
다음은 `express` 또는 `nginx`를 사용하여 요청문의 크기를 제한하는 예이다.

### `express`를 사용하는 예시

```javascript
const express = require('express');

const app = express();

app.use(express.json({ limit: '300kb' })); // body-parser의 기본 사이즈 제한은 100kb이다

// json body을 포함한 요청
app.post('/json', (req, res) => {

    //body-parser가 content-type을 체크할 수 없기 때문에 content-type이 json인지 확인한다
    if (!req.is('json')) {
        return res.sendStatus(415); // JSON으로 이루어진 요청문이 아닌것은 지원하지 않는다.
    }

    res.send('Hooray, it worked!');
});

app.listen(3000, () => console.log('Example app listening on port 3000!'));
```

🔗 [**express.json()을 위한 Express 문서**](http://expressjs.com/en/4x/api.html#express.json)

### `nginx`를 사용하는 예시

```nginx
http {
    ...
    # 모든 요청에 대해 1MB의 크기 제한
    client_max_body_size 1m;
}

server {
    ...
    # 특정 서버로 들어오는 요청에 대해 1MB의 크기 제한
    client_max_body_size 1m;
}

location /upload {
    ...
    # 특정 경로로 들어오는 요청에 대해 1MB의 크기 제한
    client_max_body_size 1m;
}
```

🔗 [**client_max_body_size 사용을 위한 Nginx 문서**](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size)