# MongoDB
MongoDB는 Json 타입의 Document 방식의 NoSQL
- JSON 형식의 데이터구조
- CRUD위주의 다중 트랜잭션 처리 가능
- Sharding(분산) / Replica(복제) 기능 제공
- Memory Mapping기술을 기반으로 빅데이터 처리에 성능이 탁월

## 관계형 데이터베이스(Relational Database)와 MongoDB의 논리적 구조 비교
| Relational Database | MongoDB |
|:-:|:--:|
| Table | Collection |
| Row | Document |
| Column | Field |
| Primary Key | Object_ID Field |
| Relationship | Embedded & Link |

# MongoDB 설치 및 실행

```
# 백그라운드로 mongo 서버 컨테이너 생성
docker run --name mongo -d mongo

# mongo 서버 접속
docker exec -it mongo bash

# mongo 클라이언트 실행
mongo
```

# Database 생성 및 Collection 생성

## Database 생성
존재하지 않는 Database를 생성하기위해 명시적으로 생성하는 명령어가 따로 필요하지 않음. Database를 전환하는 명령어를 입력하고,  데이터를 insert하면 자동으로 Database와 Collection이 자동으로 생성됨.
```
use testDB 

db.testcollection.insertOne( { x : 1 } )
```

## Collection 생성
Collection 또한 Document나 Index를 하나 생성함으로 생성할 수 있음.
```
db.test2.insertOne({ x : 2})

db.test3.createIndex({ x : 1}) 
```

## 명시적인 Collection 생성
```
db.createCollection(<Collection 명칭>, { 
    capped: <boolean>,
    autoIndexId: <boolean>,
    size: <number>,
    max: <number>,
    storageEngine: <document>,
    validator: <document>,
    validationLevel: <string>,
    validationAction: <string>,
    indexOptionDefaults: <document>,
    viewOn: <string>,
    pipeline: <pipeline>,
    collation: <document> 
} )
```

## Capped Collection

Cappped Collection 이란
- 최초 제한된 크기로 생성된 공간 내에서 만 데이터를 저장할 수 있고, 최초 공간이 모두 사용되면 다시 처음으로 돌아가서 기존 공간을 재사용하는 타입의 Collection
- 로그 데이터 처럼 일정한 기간 내에서 만 저장, 관리할 필요가 있는 데이터에 효과적

```
# 100000 바이트 크기의 log 라는 명칭의 capped collection Collection 을 생성
db.createCollection( "log", { capped: true, size: 100000 } )

# 20000 바이트 한계치와 5000 Document 한계치를 가진 log2 라는 명칭의 Collection을 생성
db.createCollection("log2", { capped : true, size : 20000, max : 5000 } )

# 해당 Collection이 capped 속성을 가지고 있는지 체크
db.log2.isCapped()

# capped collection 이 아닌 collection을 capped collection 으로 변경
db.runCommand({"convertToCapped": "test2", size: 100000});
```

# 데이터 입력(insert)

## insert
데이터를 insert 하기전에 MongoDB는 자바스크립트처럼 변수를 할당할 수도 있고 function 을 만들 수도 있음.
```
m = { ename : "김몽고", depart : "개발팀", status : "A", height: 170  } 

arr = [{ ename : "최몽고", depart : "개발팀", status : "C", height: 185 }
    , { ename : "박몽고", depart : "개발팀", status : "B", height: 167} ]
```

기본적인 Insert 명령어 3가지
1. insert: 단일 또는 다수의 Document를 입력
2. insertOne: 단일 Document를 입력
3. insertMany: 다수의 Document를 입력

```
# m 변수로 입력
db.employee.insert(m)

# 조회
db.employee.find()

# arr 변수로다수의 Document 입력
db.employee.insert(arr)

# 조회
db.employee.find()
```

## insert 동작의 특징
- Collection이 존재하지 않으면, Insert 동작이 Collection 을 생성
- Collection에 저장된 각 문서에는 Primary key 역할을하는 고유 한 _id 필드가 필요. _id 필드를 생략하면 위와 같이 ObjectId 타입으로 키가 자동으로 생성됨
- update 를 수행하는 명령어의 upsert : true 옵션을 주면 Insert 동작을 수행

## Autoincrement Id 만들어 입력하기
관계형 데이터베이스에서는 대부분 연속적인 자동 증가값을 구현할수 있는 방법을 제공함. MongoDB에서는 그러한 기능이 기본적으로 제공되지는 않지만 다음과 같이 함수를 이용하여 직접 생성할 수 있음.

```
function seq_no(name) {     
    var ret = db.seq_no.findAndModify({
        query : {_id : name},
        update :{ $inc:{next:1} },
        "new":true,
        upsert:true
    });     
    return ret.next; 
}

db.ord_no.insert({_id:seq_no("ord_no"), name: "스타워즈레고"})
db.ord_no.insert({_id:seq_no("ord_no"), name:"닌자고레고"})
db.ord_no.find()
```

# 데이터 읽기(find)

## find
find는 MongoDB에서 기본적인 읽기 명령어. 관계형 데이터 베이스에서 SELECT

**db.collection.find( query, projection )**

```
# employee collection 전체 검색
db.employee.find({}) 
```

## 동등 비교
```
# status가 "C" 인 것만 조회
db.employee.find({status : "C"}) 

# status 가 C 인 사람 중 이름만 조회(_id 값은 같이 조회됨)
db.employee.find({status : "C"}, {ename : 1}) 

# status 가 C 인 사람 중 이름만 조회(_id 값도 제외)
db.employee.find({status : "C"}, {_id: 0, ename : 1}) 
```

## 연산자
```
{ <field1>: { <operator1>: <value1> }, ... }
```

### 비교 연산자
```
# status 값이 C 인 직원을 조회
db.employee.find({status : { $eq : "C"}}, {_id: 0, ename : 1}) 

# height 160 보다 큰 직원을 조회
db.employee.find({height : { $gt : 160}}) 

# height  170보다 크거나 같은 직원을 조회
db.employee.find({height : { $gte : 170}}) 

# height가 170인 사람과 185인 직원을 조회
db.employee.find({height : { $in : [170, 185]}})

# height가 170보다 작은 직원을 조회
db.employee.find({height : { $lt : 170}})

# height가 170보다 작거나 같은 직원을 조회
db.employee.find({height : { $lte : 170}})

# height가 170이 아닌 직원을 조회
db.employee.find({height : { $ne : 170}})

# height가 170인 사람과 185가 아닌 직원을 조회
db.employee.find({height : { $nin : [170, 185]}})

# height가 170이상 185이하인 직원을 조회
db.employee.find({height : {$gte : 170, $lte:185}})
```

### 논리 연산자
```
# height가 170이상을 만족하고 status 가 C인 직원을 조회
db.employee.find({ $and : [{height : {$gte : 170}}, { status : {$eq : "C"}}] })

# height가 170이상을 만족 하거나 status가 C인 직원을 조회
db.employee.find({ $or : [{height : {$gte : 170}}, { status : {$eq : "C"}}] })

# height 가 180이하가 아닌 사람을 조회
db.employee.find({height : {$not : {$lte : 180}}})
```
$not 연산자는 단순히 연산자의 값의 반대값뿐만 아니라 존재하지 않는 값까지 결과에 포함

### Embbedded Document(내장형문서) 조회
```
# 예제 데이터
db.inventory.insertMany( [     
    { item: "journal", qty: 25, size: { h: 14, w: 21, uom: "cm" }, status: "A" } , 
    { item: "notebook", qty: 50, size: { h: 8.5, w: 11, uom: "in" }, status: "A" } , 
    { item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" } , 
    { item: "planner", qty: 75, size: { h: 22.85, w: 30, uom: "cm" }, status: "D" } , 
    { item: "postcard", qty: 45, size: { h: 10, w: 15.25, uom: "cm" }, status: "A" } 
]);
```

하위문서와 특정 필드에 조건을 걸어서 조회할 수 있음.
```
# size 아래 문서 중 uom 값이 cm 인 문서 조회
db.inventory.find( { "size.uom": "cm" } )

# size 값 중 h값이 10보다 작은 문서 조회
db.inventory.find( { "size.h": {$lt : 10} } )

# size 값 중 h값이 10보다 작고 status 가 A 인 문서 조회
db.inventory.find( { "size.h": {$lt : 10}, status : "A"} )
```

### 배열값 조회
```
# 예제 데이터
db.inventory.insertMany([
    { item: "journal", qty: 25, tags: ["blank", "red"], dim_cm: [ 14, 21 ] } , 
    { item: "notebook", qty: 50, tags: ["red", "blank"], dim_cm: [ 14, 21 ] } , 
    { item: "paper", qty: 100, tags: ["red", "blank", "plain"], dim_cm: [ 14, 21 ] } , 
    { item: "planner", qty: 75, tags: ["blank", "red"], dim_cm: [ 22.85, 30 ] } , 
    { item: "postcard", qty: 45, tags: ["blue"], dim_cm: [ 10, 15.25 ] } 
]);
```

```
# tags값에 red와 blank 값이 모두 존재하는 문서를 조회
db.inventory.find( { tags: { $all: ["red", "blank"] } } )

# tags 배열의 크기가 2인 문서를 조회
db.inventory.find( { tags : {$size : 2}} )
```

### Embbedded Document내에서 배열값 조회
```
# 예제 데이터
db.inventory.insertMany( [     
    { item: "journal", instock: [ { warehouse: "A", qty: 5 } , { warehouse: "C", qty: 15 } ] } , 
    { item: "notebook", instock: [ { warehouse: "C", qty: 5 } ] } , 
    { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 15 } ] } , 
    { item: "planner", instock: [ { warehouse: "A", qty: 40 }, { warehouse: "B", qty: 5 } ] } , 
    { item: "postcard", instock: [ { warehouse: "B", qty: 15 }, { warehouse: "C", qty: 35 } ] } 
]);
```

```
# instock 필드에서 qty가 5이고 warehouse 값이 A인 문서를 조회
db.inventory.find( { "instock": { $elemMatch: { qty: 5, warehouse: "A" } } } )
```

### 조회 결과에서 필드 표시 여부
find 명령어 두번째 파라미터에서 field 를 표시할지 여부를 지정함. 기본적으로 파라미터가 없으면 모두 표시됨.

document 에  기본적으로 포함된 _id필드를 제외하고 나머지 필드의 경우 '표시할 필드만 입력' 하거나 '표시하지 않을 필드를 표시' 해야함.
```
# 아래의 식은 에러가 발생
db.inventory.find( { status: "A" }, { item: 1, status: 0 } )

#  Embedded(내장) 된 문서의 필드 표시는 아래와 같음
db.inventory.find( { status: "A" }, { item: 1, status: 1, "size.uom": 1 } )
```

### Null 값과 필드 존재여부 조회
```
# 아래의 식은 item 필드가 null 인 값도 조회 하지만, item 필드가 존재하지 않는 문서도 조회가 됨.
db.inventory.find( { item: null }

# item 필드가 존재하지 않는 문서를 조회
db.inventory.find( { item : { $exists: false } } )
```

### Cursor 반복문
find 명령어는 cursor형태의 객체로 반환됨. 변수를 할당하고 아래와 같이 javascript 반복문을 활용할 수도 있음.

```
var myCursor = db.users.find( { type: 2 } ); 
while (myCursor.hasNext()) {     
    printjson(myCursor.next()); 
}
```

cursor객체를 array(배열)로 변환할 수도 있음.
```
var myCursor = db.inventory.find( { type: 2 } ); 
var documentArray = myCursor.toArray(); 
var myDocument = documentArray[3];
```

> 출처
> - https://cionman.tistory.com/43?category=758474
> - https://elfinlas.github.io/2019/02/11/docker-on-mongo/