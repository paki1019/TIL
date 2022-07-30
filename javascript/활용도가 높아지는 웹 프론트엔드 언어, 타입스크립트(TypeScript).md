# 타입스크립트(TypeScript)

자바스크립트(JavaScript)를 기반으로 정적 타입 문법을 추가한 프로그래밍 언어.

## 타입스크립트의 특징

### 컴파일 언어, 정적 타입 언어

- 자바스크립트가 동적 타입의 인터프리터 언어로 런타임에서 오류를 발견할 수 있음에 반해, 타입스크립트는 정적 타입의 컴파일 언어이며 타입스크립트 컴파일러 또는 바벨(Babel)을 통해 자바스크립트 코드로 변환됨.
- 코드 작성 단계에서 타입을 체크해 오류를 확인할 수 있고 미리 타입을 결정하기 때문에 실행 속도가 매우 빠르다는 장점이 있음.
- 하지만 코드 작성 시 매번 타입을 결정해야 하기 때문에 번거롭고 코드량이 증가하며 컴파일 시간이 오래 걸린다는 단점 있음.

### 자바스크립트 슈퍼셋(Superset)

- 타입스크립트는 자바스크립트의 슈퍼셋, 즉 자바스크립트 기본 문법에 타입스크립트의 문법을 추가한 언어.
- 유효한 자바스크립트로 작성한 코드는 확장자를 .js에서 .ts로 변경하고 타입스크립트로 컴파일해 변환할 수 있음.

### 객체 지향 프로그래밍 지원

- 타입스크립트는 ES6(ECMAScript 6)에서 새롭게 사용된 문법을 포함하고 있으며 클래스, 인터페이스, 상속, 모듈 등과 같은 객체 지향 프로그래밍 패턴을 제공.

## 타입스크립트 사용을 고려해야 하는 이유

### 높은 수준의 코드 탐색과 디버깅

- 코드에 목적을 명시하고 목적에 맞지 않는 타입의 변수나 함수들에서 에러를 발생시켜 버그를 사전에 제거함.
- 코드 자동완성이나 실행 전 피드백을 제공하여 작업과 동시에 디버깅이 가능해 생산성을 높일 수 있음.

### 자바스크립트 호환

- 자바스크립트와 100% 호환됨.
- 프론트엔드 또는 백엔드 어디든 자바스크립트를 사용할 수 있는 곳이라면 타입스크립트도 사용 가능.

### 강력한 생태계

- 대부분의 라이브러리들이 타입스크립트를 지원하며 마이크로소프트의 비주얼 스튜디오 코드(VSCode)를 비롯해 각종 에디터가 타입스크립트 관련 기능과 플러그인을 지원.

### 점진적 전환 가능

- 추가 기능이나 특정 기능에만 타입스크립트를 도입함으로써 프로젝트를 점진적으로 전환할 수 있음.

프로젝트의 규모가 크고 복잡할수록, 유지보수가 중요한 장기 프로젝트일수록 타입스크립트의 이점이 부각됨.

## 프론트엔드 프레임워크와 타입스크립트

### 리액트(React)

리액트와 타입스크립트의 호환성은 비교적 좋은 편. 페이스북에서 공식적으로 배포하는 리액트 웹 개발용 보일러 플레이트인 Create React App은 간단한 옵션 추가만으로 타입스크립트를 사용할 수 있도록 지원.

### 뷰(Vue.js)

뷰 2.0에서는 타입스크립트를 사용할 수 있지만 몇몇 라이브러리의 도움을 받아야 하거나 구현 자체가 안 되는 문제도 다수 있음. 뷰 3.0부터는 타입스크립트를 공식 지원. 뷰 3.0 CLI(Command Line Interface)는 타입스크립트 도구화 지원을 기본으로 제공함.

### 앵귤러(Angular)

자바스크립트 기반인 앵귤러 버전 1과 달리 버전 2부터는 타입스크립트 기반으로 만들어졌고 타입스크립트를 권장.

## 타입스크립트로 어떻게 전환하면 좋을까?

### 1. 비주얼 스튜디오 코드(VSCode)로 자바스크립트 페어링

- 비주얼 스튜디오 코드에는 편집기 또는 특정 작업 영역(프로젝트)에 대해 자바스크립트에서 타입스크립트 검사를 활성화 할 수 있는 설정이 포함되어 있음.
- .vscode/setting.json에 다음 코드를 추가, 타입이 안전하지 않은 줄에는 에디터에서 해당 코드를 에러로 표시 됨. 이러한 에러 메시지는 에디터에만 표시되며 코드에는 영향을 주지 않음.
  ```
  {
   "Javascript.implicitProjectConfig.checkJs": true
  }
  ```
- 아래 코드를 추가하여 타입 체크를 선택적으로 비활성화 할 수도 있음.
  ```
  @ts-ignore - 해당 줄
  @ts-nocheck - 전체
  ```

### 2. 자바스크립트용 타입스크립트 컴파일러 사용

- 타입스크립트 컴파일러를 설치하고 스크립트를 실행하거나 설정. tsconfig.json 파일을 사용하여 컴파일러 설정을 세팅.

  ```
  {
   "compilerOptions": {
    "allowJs": true //타입스크립트 컴파일러를 통해 실행할 자바스크립트 파일을 선택
    "checkJs": true //자바스크립트 파일에 대한 타입 체크
    "outDir": "./dist"
    "rootDir": "./src"
    "strict": false
   }
  }
  ```

### 3. 자바스크립트 파일을 타입스크립트 파일로 변환

- 자바스크립트에서 타입이 안전한 파일인지 확인한 후 하나씩 타입스크립트 파일로 변환. (.js → .ts)

### 4. 엄격한 타입 체크

- 타입 체크를 더 엄격하게 하는 몇 가지 옵션.
  ```
  {
   "compilerOptions": {
   "noImplicitAny": true,
   "strictNullChecks": true,
   "strictFunctionTypes": true,
   "strictBindCallApply": true,
   "strictPropertyInitialization": true,
   "noImplicitThis": true,
   "alwaysStrict": true
  }
  ```

## 한눈에 보는 타입스크립트 기본 문법

### 기본 타입

Boolean, Number, String, Object, Array, Tuple, Enum, Any, Void, Null, Undefined, Never

- 변수에 타입 설정

  ```
  let str: string = 'hi';
  let num: number = 100;

  let arr: Array = [1, 2, 3];
  let arr2: number[] = [1, 2, 3];

  let obj: object = {};
  let obj2: { name: string, age: number} = {
   name: 'hoho',
   age: 22
  };
  ```

- 함수에 타입 설정

  ```
  function add(a: number, b: number): number {
  return a+b;
  }
  //옵셔널 파라미터
  function log(a: string, b?: string, c?: string) {
   console.log(a);
  }
  ```

기본 타입 중 자바스크립트에 존재하지 않는 타입

- Tuple: 배열의 타입 순서와 배열 길이를 지정할 수 있는 타입.

  ```
  var arr: [string, number] = ['aa', 100];
  ```

- Enum: Number 또는 String 값 집합에 고정된 이름을 부여할 수 있는 타입.

  ```
  enum Shoes {
   Nike = '나이키',
   Adidas= '아디다스'
  }
  ```

- Any: 모든 데이터 타입을 허용.

- Void: 변수에는 undefined와 null만 할당하고 함수에는 리턴 값을 설정하지 않는 타입.

- Never: 특정 값이 절대 발생할 수 없음

### 인터페이스

타입을 정의한 규칙을 의미

```
interface User {
 age: number;
 name: string;
}
```

- 변수와 함수에 활용한 인터페이스

  ```
  var person: User = {
   age: 30,
   name: 'aa'
  }

  function getUser(user: User) {
   console.log(user);
  }
  ```

- 인덱싱

  ```
  interface StringArray {
   [index: number]: string;
  }

  var arr2: StringArray = ['a', 'b', 'c'];
  arr[0] = 10 //Error;
  ```

- 딕셔너리 패턴

  ```
  interface StringRegexDictionary {
   [key: string]: RegExp
  }

  var obj: StringRegexDictionary = {
   cssFile: /\.css$/,
   jsFile: 'a' //Error
  }

  obj['cssFile'] = /\.css$/;
  obj['jsFile'] = 'a' //Error
  ```

- 인터페이스 확장

  ```
  interface Person{
   name: string;
   age: number;
  }

  interface User extends Person{
   language: string;
  }
  ```

### 오퍼레이터

- Union 타입: 자바스크립트의 OR 연산자와 같은 의미의 타입. Union 타입으로 지정하면 각 타입의 공통된(보장된) 속성에만 접근 가능.

  ```
  function askSomeone(someone: Developer2 | Person) {
   console.log(someone);
  }
  ```

- Intersection 타입: 자바스크립트의 AND 연산자와 같은 의미의 타입. 각각의 모든 타입이 포함된 객체를 넘기지 않으면 에러가 발생.

  ```
  function askSomeone(someone: Developer & Person) {
   console.log(someone);
  }
  ```

### 제네릭

여러 가지 타입에서 동작하는 컴포넌트를 생성하는데 사용.

```
function logText <T> (text: T):T {
 return text;
}

    logText<string>('aa');
    logText<number>(100);

```

### 타입 추론

타입스크립트가 코드를 해석하는 과정.

```
var a = true;

a = 100; //Error
```

a 변수를 Boolean 타입으로 추론했기 때문에 Number타입을 할당하면 에러가 발생

- 가장 적절한 타입(Best Common Type): 배열에 담긴 값들을 추론하여 Union타입으로 묶어 나가는 것

  ```
  var arr = [1, 2, true];
  ```

  Typescript는 해당 코드의 타입을 Number | Boolean 으로 정의.

- 인터페이스와 제네릭을 이용한 타입 추론 방식

  ```
  interface Dropdown<T>{
   value: T,
   text: 'String'
  }

  var items: Dropdown<boolean> {
   value: true,
   text: 'aa'
  }
  ```

### 타입 단언

타입스크립트가 해석하는 것보다 더 확실한 목적을 가지고 개발자가 해당 코드에 타입을 직접 지정하는 것

```
var b = a as string;

//타입추론시 HTMLDivElementㅣnull로 반환
var div = document.querySelector('div') as HTMLDivElement;
div.innerText;
```

### 타입 호환

특정 타입이 다른 타입에 잘 호환되는지를 의미

- 구조적 타이핑: 코드 구조 관점에서 타입이 서로 호환되는지를 판단하는 것. 구조적으로 더 큰 타입은 작은 타입을 호환할 수 없음.

  ```
  interface Developer {
   name: string;
   age: string;
  }
  interface Person {
   name: string;
  }

  var developer: Developer;
  var person: Person;

  developer = person; //Error
  person = developer;
  ```

> 출처
>
> - https://www.samsungsds.com/kr/insights/typescript.html
