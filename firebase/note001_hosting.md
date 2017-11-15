# 파이어베이스 하기
-----
2017.11.14 임규산 작성

> 파이어베이스 인증을 완료한 상태에서 웹을 호스팅 올리는 방법이다.


#### 1. 개발시작

아래의 명령어를 실행하면 자동으로 로컬 서버가 돌아가게 된다. 기본 포트는 3000번으로 작동한다.

```shell
> firebase serve
```

<br/><br/>
이제 기본 HTML 문서를 작성해보자

이름은 index.html 이다.
```HTML
<!DOCTYPE>
<html>
    <head>
        <title>Welcome to Firebase home</title>
    </head>
    </body>
        <h1>it's running now!</h1>
        <!-- 기본 파이어베이스 기본(필수 나머지는 옵션) -->
        <script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-app.js"></script>
        <!-- 파이어베이스 인증 -->
        <script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-auth.js"></script>
        <!-- 파이어베이스 데이터베이스 -->
        <script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-database.js"></script>
        <!-- 파이어베이스 메시징(푸시) -->
        <script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-messaging.js"></script>
        <!-- 파이어베이스 저장소 -->
        <script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-storage.js"></script>

        <!-- 내가 만든 프로젝트 정보를 채우자 -->
        <script>
          var config = {
              apiKey: "<API_KEY>",
              authDomain: "<PROJECT_ID>.firebaseapp.com",
              databaseURL: "https://<DATABASE_NAME>.firebaseio.com",
              storageBucket: "<BUCKET>.appspot.com",
              messagingSenderId: "<SENDER_ID>",
            };
          firebase.initializeApp(config);
        </script>
    </body>
</html>
```

뭐 꾸밀 필요도 없다.

이제 디플로이(Deploy: 웹사이트를 세상에 공개) 해보자


```shell
> firebase deploy
```

끝!
