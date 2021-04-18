# SQL Injection

SQL Injection은 입력값에 의해 공격자가 원하는 방향으로 쿼리의 실행 흐름을 바꾸는 공격. SQL Injection 공격에 취약할 경우 데이터 베이스의 내용을 조회하거나, 데이터를 수정할 수 있음. 

injection 관련 공격에는 OS command를 구성하는 명령어에 삽입되는 경우 Command Injection, 그 외에 LDAP, XPath, NoSQL 쿼리, XML 파서, SMTP 헤더, expression languages, ORM 쿼리 등에 삽입되는 경우 발생 할 수 있음.

SQL Injection에 취약한 웹 페이지(NT11622.scc.svc.ad1.io.navercorp.com/orders?orderNo=S2018052056412)(패치됨)

```
public List<Order> getOrderList(OrderSearch orderSearch) {

    String getOrdersByOrderNoQuery = "SELECT * FROM ORDERS a WHERE a.order_no='" + orderSearch.getOrderNo() + "'";
    Query query = entityManager.createNativeQuery(getOrdersByOrderNoQuery, Order.class);

    List<Order> orders = query.getResultList();

    return orders;
}
```
> 주문번호를 조회하는 기능의 코드 일부

만약 쿼리를 SELECT * FROM ORDERS WHERE ID = '1' ='1'가 된다면 모든 row를 조회할 수 있음.

```NT11622.scc.svc.ad1.io.navercorp.com/orders?orderNo=1' or '1'='1``` 해당 링크에 접속하면 해당 테이블 모든 데이터를 조회할 수 있음.

## 영향력
SQL Injection은 데이터베이스 쿼리를 마음대로 수정 할 수 있기 때문에, 데이터베이스 유저 권한의 모든 행위를 할 수 있음.

## 대응방안

### 1. 쿼리를 정적으로 만들기
가장 쉬우면서 일반적인 SQL Injection 대응방안. 쿼리를 템플릿으로 만들고 유저의 입력값을 파라미터로 사용하는 방법. 유저의 입력값 자체가 쿼리문을 수정하지 못하여 SQL Injection을 방어할 수 있음.

```
Employee employee = new Employee();
employee.setFirstName("James");
 
String SELECT_BY_ID = "SELECT COUNT(*) FROM EMPLOYEE WHERE FIRST_NAME = :firstName";
 
SqlParameterSource namedParameters = new BeanPropertySqlParameterSource(employee);
return namedParameterJdbcTemplate.queryForObject(SELECT_BY_ID, namedParameters, Integer.class);
```

### 2. 쿼리를 동적으로 만들어야 하는 경우
성능 혹은 구조적으로 어쩔수 없이 쿼리를 동적으로 생성해야하는 경우. 이때는 유저의 입력값이 아닌 정해진 값을 사용하도록 처리
```
int order = userInputValue; // 유저의 입력값

if(order == 1) {
    orderByKeyword = "DESC";
} else {
    orderByKeyword = "ASC";
}

// orderByKeyword 문자열을 이용해 쿼리를 동적으로 생성
String query = "SELECT * FROM EMPLOYEE order by name " + orderByKeyword;
```
> 유저의 입력값에 따라 쿼리를 동적으로 제어하지만, 유저가 쿼리를 수정할 수 없는 예시

### 3. 최악의 경우를 대비하자
SQL Injection이 발생했을 경우 데이터베이스를 보호할 수 있도록 중요 정보를 암호화 해야함. 취약점을 통해 데이터가 유출되더라도 암호화가 되어있다면 2차 피해를 막을 수 있음.

## 올바르지 않은 대응방안

### 1. 잘못된 입력값 치환
```
if ( searchWord.indexOf('>') >= 0 || searchWord.indexOf('%') >= 0 || searchWord.indexOf(')') >= 0 ) {
    searchWord = "all";
}
```
> 유저가 입력한 쿼리를 생성하는 특수문자가 있을 경우 대응하는 코드

예외적인 상황의 경우에 해당 문자를 사용하지 않고도, SQL Injection이 가능할 수 있음.
