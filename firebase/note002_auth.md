# 파이어베이스 하기
-----
2017.11.14 임규산 작성

> 파이어베이스 인증을 완료한 상태에서 웹을 호스팅 올리는 방법이다.


#### 2. 인증하기

파이어베이스로 인증을 해보자 인증에는 여러가지가 있다.
인증의 방법에는 여러가지가 있다.
- 아이디/이메일
- 핸드폰
- 익명
- 제공업체(구글, 페이스북, 트위터, 깃헙)

파이어베이스는 이런 여러가지를 알아서 로그인과 회원가입, 관리를 해준다.
그리고 기본적으로는 하나의 이메일은 하나의 ID만을 가질 수 있다.
> 예를 들어 지메일로 페이스북도 관리하는 경우에 페이스북으로 로그인 했다. 그러면 구글 인증도 같은걸로 되있고, 그 이메일로 로그인 하려고 해도 이미 있는 사용자이다.

회원가입부터 해보자
```javascript
firebase.auth().createUserWithEmailandPassword(email, password).catch(function(error) {
    // 에러처리
    // 약한 패스워드(6자 이하), 이미 있는 회원의 경우, 이메일 형식이 아님 등
})
```

로그인은
```javascript
firebase.auth().signInWithEmailAndPassword(email, password).catch(function(error) {
  // 에러처리
  // 비밀번호 틀린경우, 없는 사용자의 경우
});
```
로그아웃
```javascript
firebase.auth().signOut().then(function() {
  // 로그아웃 성공
}).catch(function(error) {
  // 오류가 발생 했다.
});
```

지금 까지 봤을 때 이상한게 로그인과 회원가입은 결과값이 없다. 이를 감지하는 콜백함수가 존재하기 때문이다.
```javascript
firebase.auth().onAuthStateChanged(function(user) {
  if (user) {
    // User is signed in.
    // 유저가 로그인 한 경우
    // user 객체 안에는 여러가지가 들어있다. json 형식이니 알아서 꺼내 쓰자
    // user.displayName, user.email, user.photoUrl 등이 있다.
    // 제공업체(구글 등) 로그인의 경우는 user.providerData 에 정보가 있다.
    console.log(user)
  } else {
    // 로그아웃을 한 경우
  }
});

// 콜백 말고, 그냥 현재 유저 상태를 가져오고 싶다면,
var user = firebase.auth().currentUser;

```

사용자의 프로필을 업데이트 할 때 는 이렇게 한다.
```javascript
var user = firebase.auth().currentUser;

// 프로필 내용을 바꿀 때
user.updateProfile({
  displayName: "new Name",
  photoUrl: "new Url"
}).then(function() {
  // 성공한 경우
}).catch(function(error) {
  // 유저가 없거나, 오타 등
})

// 이메일을 업데이트 할 때(식별자가 바뀐다.)
user.updateEmail("user@example.com").then(function() {
  // 성공
}).catch(function(error) {
  // 실패
  // 이미 사용중인 이메일의 경우 등
});

```

비밀번호 바꾸기
```javascript


```


사용자 이메일 인증(가짜 이메일을 입력하는 경우 예방)
이메일은 템플릿이 지정되어 있어서 재미없다.

```javascript
var user = firebase.auth().currentUser;

user.sendEmailVerification().then(function() {
  //이메일 보내고 나서 알림창을 띄워주자
}).catch(function(error) {
  // 에러가 났다. 안날아 갔거나 근데 그런 경우는 없다고 본다.
});

// 비밀번호를 바꿀 때(또는 비밀번호 초기화 하기)
var newPassword = getASecureRandomPassword(); //비밀번호 랜덤 생성

user.updatePassword(newPassword).then(function() {
  // 비밀번호가 바꼈다. 바뀐 비밀번호로 하라고 로그아웃 시키자
}).catch(function(error) {
  // 오류 발생 안바꼈다.
});


// 비밀번호 재설정 이메일 보내기
firebase.auth().sendPasswordResetEmail(user.email).then(function() {
  // 이메일 보냈다!
}).catch(function(error) {
  // 오류가 발생 했다.
});
```

사용자 삭제하기
```javascript
var user = firebase.auth().currentUser;

user.delete().then(function() {
  // 유저 삭제 하기전에 한번 더 물어봐 주자
}).catch(function(error) {
  // 오류가 발생 했다.
});
```

보안에 민감한 작업을 할 때
- 비밀번호 재설정
- 계정삭제
- 이메일 주소 수정
- 그외 등등

이런 때에는 재인증 요청을 해주자
```javascript
var user = firebase.auth().currentUser;
var credential = user.credential; // 인증 토큰을 저장

user.reauthenticateWithCredential(credential).then(function() {
  // 재인증 완료
}).catch(function(error) {
  // 에러 발생 실패함
});
```


제공업체 로그인 하기 (구글과 페이스북만 다룬다.)
```javascript
// 구글
var googleProvider = new firebase.auth().GoogleAuthProvider();

// 페이스북
var facebookProvider = new firebase.auth().FacebookAuthProvider();

// 새 창에서 로그인
firebase.auth().signInWithPopup(provider).then(function(result) {
  // 성공
}).catch(function(error) {
  // 오류
})

// 현재 창에서 리다이렉션 로그인
firebase.auth().signInWithRedirect(provider);
firebase.auth().getRedirectResult().then(function(result) {
  // 성공
}).catch(function(error) {
  // 오류
})
```

```javascript

```

```javascript

```

```javascript

```

```javascript

```

```javascript

```

끝!
