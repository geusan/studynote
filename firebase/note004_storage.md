# 파이어베이스 하기
-----
2017.11.14 임규산 작성

> 파이어베이스 인증을 완료한 상태에서 웹을 호스팅 올리는 방법이다.


#### 4. 저장소 연결하기


파이어베이스는 사진, 동영상, 파일 등등 모든걸 저장하는 공간을 제공한다.용량은 5기가까지 무료로 준다. 파일들은 업로드 할 때, 압축되어서 되는 것 같다.

```javascript
// 저장소 인스턴스
var storageRef = firebase.storage().ref();

// 이미지 폴더 찾기
var imageRef = storageRef.child("images");

// space.jpg 사진이 저장된 곳 찾기
var spaceRef = storageRef.child("images/space.jpg");

// Ref(reference) 들은 항상 child 와 parent를 가지고 있고, root는 최상위다.
// root의 parent는 null이고, terminal(끝)의 child는 역시 null이다.

```

참조 Ref 가 가지고 있는 속성
- parent, child, root
- fullPath, name, bucket

대략 모두 연결되어 있으며, 메타 데이터를 가지고 있어서 식별할 수 있다 정도로 이해할수 있다.


업로드하는 방법

```javascript
// 참조 위치 가져옴
var ref = firebase.storage().ref();

// 파일 인스턴스 생성 (Blob, File, Uint8Array 형태)
var file = 업로드한 파일! 또는 바이트(Uint8Array)

// 참조 위치에 파일 저장
ref.put(file).then(function(snapshot) {
    // 파일 업로드!
})


// 만약에 Blob, File, Uint8Array 형태를 못가지는 경우
// raw, base64, base64url, data_url의 경우 모두 저장할 수 있다.
ref.putString(데이터).then(function(snapshot) {
   // raw 데이터 저장
})

ref.putString(데이터, 'base64 또는 base64url 또는 data_url').then(function(snapshot) {
   // 그 외의 형태 저장, 명시적으로 어떤 형식의 데이터인지 말해야 한다.
})



// 저장할 때 메타데이터 추가하기
var metadata = {
    contentType: 'image/jpg',
};

ref.child("파일경로와 이름").put(file, metadata);
// putString 메소드로는 잘 모르겠다 한번 해보자

```


업로드를 시작하게 되면 자동으로 시작되지만 이를 관리해야한다. 이를 이벤트 상태로 관리할 수 있다.

이벤트 종류
- running : 시작 또는 멈췄다가 시작할 때
- progress : 업로드 중에 발생(위 아래 두개 빼곤 항상 이 상태)
- paused : 일시중지 될 때 발생

업로드Task의 snapshot이 가지는 속성
- bytesTransferred : 지금까지 전송한 바이트 수
- totalBytes : 총 보내야 하는 바이트 수
- state : 현재 상태 이벤트
- metadata : 업로드 완료 전에 서버에 전송된 메타데이터, 완료 후에는 서버에서 다시 보내주는 데이터다.
- task : 스냅샷의 대상 작업이다. 작업을 일시 중지, 재개, 취소 를 유발할 수 있다.
- ref : 참조 위치

```javascript

// 업로드를 시작하게 되면 자동으로 업로드를 시작하는 데
var uploadTask = ref.child("파일경로와 이름").put(file, metadata);

// 멈추려면
uploadTask.pause();

// 다시 재개 하려면
uploadTask.resume();

// 업로드 취소하려면
uploadTask.cancel();

// 상태 관리
uploadTask.on('state_changed', function(snapshot) {
    // 진행율
    var progress = (snapshot.bytesTransferred / snapshot.totalBytes * 100);

    switch(snapshot.state){
        case firebase.storage.TaskState.PAUSED: // 'paused'
            // 현재 상태 멈춤
            break;
        case firebase.storage.TaskState.RUNNING: // 'running'
            // 현재 상태 진행 중
            break;
    }   
}, function(error) {
    // 에러 발생
}, function() {
    // 업로드에 성공했다. 다운로드 url을 확인하자
    var downloadURL = uploadTask.snapshot.downloadURL;

})
// 업로드의 생명을 관리해야한다.(잘 올라가는지? 빠르게 올라가는 중인지 그런거)
// running, progress, pause 의 3가지가 있다.


```


업로드를 했으니 다운로드를 해보자
다운로드를 하려면 참조가 필요하다. 아래의 3개의 방법은 결국 똑같은 참조 위치를 반환한다.

```javascript
var storage = firebase.storage();
var pathRef = storage.ref("파일 위치"); // 1
var gsRef = storage.refFromURL("gs://bucket/파일 위치");// 2
var httpsRef = storage.refFromURL("https://firebasestorage.googleapis.com/b/bucket/파일 위치");// 3

```

반환된 참조위치를 가져왔으면 url을 만들어서 다운로드 해보자

```javascript

var ref = 참조위치

ref.getDownloadURL().then(function(url) {

    // 비동기통신으로 가져오거나 링크를 사용자가 누르게 유도한다.
    // 여기서는 비동기 통신을 사용한다.
    var xhr = new XMLHttpRequest();
    xhr.responseType = 'blob';
    xhr.onload = function(event) {
        var blob = xhr.response;
    };
    xhr.open('GET', url);
    xhr.send();

    // 그냥 url로 이미지 보여주기
    var img = document.getElementById('myimg');
    img.src = url;

}).catch(function(error) {
    // 에러 발생
    switch (error.code) {
        case 'storage/object_not_found':
          // 없는 파일!
          break;
        case 'storage/unauthorized':
          // 퍼미션이 없다
          break;
        case 'storage/canceled':
          // 유저가 중간에 업로드를 취소한거
          break;
        case 'storage/unknown':
          // 나도 왜 나는지 몰라, 근데 서버가 이상한게 대다수
          break;
    })

```

이젠 파일을 삭제해보자

```javascript
var ref = 참조 위치
ref.delete().then(function() {
    // 성공
}).catch(function(error) {
    // 에러
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
