# DTO 깔끔하게 관리하기

## DTO

DTO(Data Transfer Object)는 계층 간 데이터 교환을 하기 위해 사용하는 객체로, 로직을 가지지 않는 순수한 데이터 객체(getter & setter 만 가진 클래스)를 의미합니다.

개발을 하다보면 흔히 추가/변경될 데이터를 DAO로 전달할 때, Controller에서 Request를 받아올 때, Response를 내려줄 때 등 다양한 용도로 사용하곤 합니다.

### Over-Fetching

DTO 내 필드가 실제 데이터보다 더 많아 불필요한 필드까지 Return 하는 경우를 의미합니다.

```
// User 실제 데이터
[name: 'lee', age: 28]

// DTO
public class UserDto {
    private Stirng name;
    private int age;
    private String address;
    private String phoneNumber;
}
```

위 예시를 보면 실제 데이터는 name, age 값밖에 존재하지 않지만, 전달하는 DTO는 name, age, address, phoneNumber까지 없는 필드들도 존재하고 있습니다. 이 경우 데이터를 DTO에 담아서 전달하면 address, phoneNumber는 null로 전달되게 되는데요, 이 경우를 Over-Fetching이라고 합니다.

Over-Fetching 같은 경우는 잘못된 데이터 인터페이스를 제공하는 것이기 때문에 바람직하지 못한 DTO 사용이라고 볼 수 있습니다.

### Under-Fetching

반대로 DTO 내 필드가 실제 데이터보다 더 적은 경우를 Under-Fetching이라고 합니다.

```
// User 실제 데이터
[id: 103, name: 'lee', age: 28, password: 'p@sswoRd']

// DTO
public class UserDto {
    private Stirng name;
    private int age;
}
```

Over-Fetching과는 다르게 Under-Fetching은 공개되지 않아야할 데이터를 걸러낸다는 관점에서 적절한 DTO 사용이라고도 볼 수 있습니다.

## 사용처에 따라 DTO 구분하기

```
public class UserController {
    ...

    public UserDto find(@RequestParam long id) {
        User user = userService.findOne(id);
        UserDto response = UserDto.of(user);
        return response;
    }

    public UserDto save(UserDto request) {
        User user = userService.save(request);
        UserDto response = UserDto.of(user);
        return response;
    }
}

```

위 예시에서는 같은 UserController의 request, response 처리에 있어 동일한 DTO를 사용하고 있습니다.

같은 도메인 데이터를 전달함에 있어 DTO를 공통으로 쓰는것은 위에서 얘기했듯이 과도한 Over-Fetching이며, 의도치 않은 데이터 노출이 될수 있습니다. (위의 경우 password까지 노출 됨)

위와 같이 같은 DTO를 공유해서 사용하기 보단 아래와 같이 사용처에 따라 DTO를 분리, 따로 class를 만들어 관리하는것이 바람직한 코드가 되겠습니다.

```
public class UserController {
    ...

    public UserResponseDto find(@RequestParam long id) {
        User user = userService.findOne(id);
        UserResponseDto response = UserResponseDto.of(user);
        return response;
    }

    public UserResponseDto save(UserRequestDto request) {
        User user = userService.save(request);
        UserResponseDto response = UserResponseDto.of(user);
        return response;
    }
}
```

### 너무 과도하게 많은 DTO

그러나 위와 같이 사용처에 따라 DTO를 분리하는것은 너무 많은 DTO가 생성된다는 단점이 존재합니다. DTO가 많아지면 class 파일이 많아지게 되고 결국 package 구조가 더러워지는 문제도 생기게 됩니다... 위 예시에서도 단순히 한두개의 API를 만드는데 있어서 2개의 Dto가 생성되었으며, VO, DAO로 넘기는 DTO까지 생각하면 훨씬 더 많은 DTO가 생기게 됩니다.

### Inner Class로 DTO 관리

그러나 이 점을 개선하는 방법도 있습니다. 아래 블로그에서 제시하는 방법으로 Inner Class(Nested Class)로 DTO를 관리하는 방법인데요.(https://velog.io/@p4rksh/Spring-Boot%EC%97%90%EC%84%9C-%EA%B9%94%EB%81%94%ED%95%98%EA%B2%8C-DTO-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0)

```
public class UserDto {
    public static class Request {
        private String name;
        private int age;
        private String password;
        ...
    }

    public static class Response {
        private String name;
        private int age;
        ...
    }
}
```

위의 예시와 같이 context별로 DTO를 하나씩 두고 inner class로 DTO를 생성해두면 하나의 class 파일로 깔끔하게 관리할 수 있습니다.

```
public class UserController {
    ...

    public UserResponseDto find(@RequestParam long id) {
        User user = userService.findOne(id);
        UserDto.Response response = UserDto.Response.of(user);
        return response;
    }

    public UserResponseDto save(UserDto.Request request) {
        User user = userService.save(request);
        UserDto.Response response = UserDto.Response.of(user);
        return response;
    }
}
```

일각에서는 해당 클래스 파일이 너무 비대해져 보인다는 단점도 있지만, context별로 잘 구분하고 필드가 많지 않으면 잘 활용해볼만한것 같습니다.

> 출처
>
> - https://velog.io/@p4rksh/Spring-Boot%EC%97%90%EC%84%9C-%EA%B9%94%EB%81%94%ED%95%98%EA%B2%8C-DTO-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0
> - https://velog.io/@kmdngmn/Spring-DTO-Inner-Class-%EC%99%80-Over-Fetching
