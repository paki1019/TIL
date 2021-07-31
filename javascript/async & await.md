# async & await

기존의 비동기 처리 방식인 콜백 함수와 프로미스의 단점을 보완하고 개발자가 읽기 좋은 코드를 작성할 수 있게 도와줌.

```
async function logName() {
  var user = await fetchUser('domain.com/users/1');
  if (user.id === 1) {
    console.log(user.name);
  }
}
```

# async & await 기본 문법

```
async function 함수명() {
  await 비동기_처리_메서드_명();
}
```

동기 처리 메서드가 꼭 프로미스 객체를 반환해야 await가 의도한 대로 동작함.

```
function fetchItems() {
  return new Promise(function(resolve, reject) {
    var items = [1,2,3];
    resolve(items)
  });
}

async function logItems() {
  var resultItems = await fetchItems();
  console.log(resultItems); // [1,2,3]
}
```

# async & await 예외 처리

async & await에서 예외를 처리하는 방법은 바로 try catch.

```
async function logTodoTitle() {
  try {
    var user = await fetchUser();
    if (user.id === 1) {
      var todo = await fetchTodo();
      console.log(todo.title); // delectus aut autem
    }
  } catch (error) {
    console.log(error);
  }
}
```

> 출처
>
> - https://joshua1988.github.io/web-development/javascript/js-async-await/
