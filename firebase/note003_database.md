# 파이어베이스 하기
-----
2017.11.14 임규산 작성

> 파이어베이스 인증을 완료한 상태에서 웹을 호스팅 올리는 방법이다.


#### 3. 데이터베이스 쓰기

기본으로 사용하던 구조에서 그대로 쓰면 된다.
여기서 데이터베이스를 추가해보자

데이터 베이스 인스턴스 가져오기
```javascript
var database = firebase.database();
```

데이터 쓰기는 크게 set과 update가 있다. 원하는 트리에 글을 쓴다.
```javascript
var database = firebase.database();
database.ref("dir/" + "where").set({
    data1: "data1",
    data2: "data2",
    data3: "data3"
});
```

원하는 위치의 데이터 실시간 확인
```javascript
var connectRef = firebase.database().ref("지정하는 위치");
connectRef.on('value', function(snapshot){
    console.log(snapshot.val());
    // 원하는 위치의 데이터 출력
});

//이러면 실시간 확인 취소됨
connectRef.off()
```

원하는 위치의 데이터 한번만 확인
```javascript
var connectRef = firebase.database().ref("지정하는 위치");
connectRef.once('value').then(function(snapshot){
    console.log(snapshot.val());
    // 원하는 위치의 데이터 출력
});
```

원하는 위치의 데이터 업데이트
```javascript
// 넣을 데이터 만들기
var dataset = {
    uid: uid, // user의 id
    name:"jacob",
    age:10,    
}

// 새로운 행을 추가한다 위치를 추가 하는 것
var newKey = firebase.database().ref().child("child").push().key;

// 어디다 올릴지 정한다. 한번에 여러군데 올릴 수 있다. 이 경우에는 전체의 포스트와 개인 포스트 두군데에 올린다.

var updats = {}
updates['/posts/' + newKey]= dataset;
updates['/users/' + uid + '/' + newKey] = dataset;

firebase.database().ref().update(updates);

```
삭제하기( set과 update는 promise를 반환해서 데이터베이스 커밋 시점을 알 수 있다.)
```javascript
var removeRef = firebase.database().ref();
//삭제1
removeRef.child("어딘가").remove();
removeRef.set({"어딘가":null});
removeRef.update({"어딘가":null});
```

트랜잭션 사용하기
데이터의 현재 상태를 인수로 취하고, 이 데이터에 새로 기록하려는 값을 반환한다. 새 값이 기록되기 전에 다른 클라이언트에서 해당 위치에 기록하면 업데이트 함수가 새로운 현재 값으로 다시 호출되고 쓰기가 다시 시도된다.
```javascript
posRef.transaction(function(post){
    if(post){
        if(post.stars && post.stars[uid]){
            post.starCount--;
            post.stars[uid] = null;
        } else {
            post.startCount++;
            if(!post.stars){
               post.stars = {};
            }
            post.stars[uid] = true;
        }
    }
    return post;
}, function(){
    // 완료된 후에
}, function(){
    // 부분적으로 완료 된다?
});
```


```javascript

```

```javascript

```
끝!
