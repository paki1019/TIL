# Promise

프로미스는 자바스크립트 비동기 처리에 사용되는 객체.

```
function getData(callback) {
  // new Promise() 추가
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      // 데이터를 받으면 resolve() 호출
      resolve(response);
    });
  });
}

// getData()의 실행이 끝나면 호출되는 then()
getData().then(function(tableData) {
  // resolve()의 결과 값이 여기로 전달됨
  console.log(tableData); // $.get()의 reponse 값이 tableData에 전달됨
});
```

# 프로미스의 3가지 상태(states)

new Promise()는 프로미스를 생성하고 종료될 때까지 3가지 상태를 가짐.

- Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

## Pending(대기)

아래와 같이 new Promise() 메서드를 호출하면 대기(Pending) 상태가 됨.

```
new Promise();
```

new Promise() 메서드를 호출할 때 콜백 함수를 선언할 수 있음, 콜백 함수의 인자는 resolve, reject.

```
new Promise(function(resolve, reject) {
  // ...
});
```

## Fulfilled(이행)

콜백 함수의 인자 resolve를 아래와 같이 실행하면 이행(Fulfilled) 상태가 됨.

```
new Promise(function(resolve, reject) {
  resolve();
});
```

이행 상태가 되면 아래와 같이 then()을 이용하여 처리 결과 값을 받을 수 있음.

```
function getData() {
  return new Promise(function(resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

// resolve()의 결과 값 data를 resolvedData로 받음
getData().then(function(resolvedData) {
  console.log(resolvedData); // 100
});
```

## Rejected(실패)

reject를 아래와 같이 호출하면 실패(Rejected) 상태가 됨.

```
new Promise(function(resolve, reject) {
  reject();
});
```

실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 catch()로 받을 수 있음.

```
function getData() {
  return new Promise(function(resolve, reject) {
    reject(new Error("Request is failed"));
  });
}

// reject()의 결과 값 Error를 err에 받음
getData().then().catch(function(err) {
  console.log(err); // Error: Request is failed
});
```

# 여러 개의 프로미스 연결하기 (Promise Chaining)

```
new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  }, 2000);
})
.then(function(result) {
  console.log(result); // 1
  return result + 10;
})
.then(function(result) {
  console.log(result); // 11
  return result + 20;
})
.then(function(result) {
  console.log(result); // 31
});
```

# 프로미스의 에러 처리 방법

에러 처리 방법에는 2가지 방법이 있음.

1. then()의 두 번째 인자로 에러를 처리하는 방법
   ```
   getData().then(
   handleSuccess,
   handleError
   );
   ```
2. catch()를 이용하는 방법

   ```
   getData().then().catch();
   ```

> 가급적 catch를 사용하는것이 더 유리 - then 안의 오류도 잡아낼 수 있기 때문

> 출처
>
> - https://joshua1988.github.io/web-development/javascript/promise-for-beginners/
