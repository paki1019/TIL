# Cross Site Scripting

크로스 사이트 스크립팅(Cross-Site Scripting, XSS)는 사용자의 입력값에 의해 자신 혹은 타인의 브라우저에 스크립트를 삽입하여 사용자를 공격하는 기법. XSS 취약점은 전사적으로 가장 많이 발생하는 취약점

XSS는 공격 유형에 따라 크게 2가지로 분류할 수 있음.
- 사용자의 입력값이 즉시 나타나는 비 지속적인 유형(Non-persistent), 사용자의 입력이 반사된다는 의미에서 Reflected XSS 라고도 함.
- 사용자의 입력이 데이터베이스(혹은 파일, 브라우저 리소스 등)에 저장되어 지속적(Persistent)으로 발생하는 유형, 사용자의 입력값이 저장되어 나타난다는 젬에서 Stored XSS 라고도 함.

## 예시
XSS에 취약한 웹 페이지(NT11622.scc.svc.ad1.io.navercorp.com/products?keyword=브라운)(패치됨)

유저가 검색할 경우 검색한 입력한 값이 화면에 출력되는 기능입니다.

```
<div class="center mb-2">
    <span th:utext="${productSearch.keyword}"></span>
    에 대한 검색결과 : 총 <span th:text="${pager.getTotalCnt()}"></span> 개 의 상품이 검색되었습니다.
</div>
```
> 유저가 검색창에 입력한 입력값이 출력되고 있는 코드


```
@GetMapping
public String getProductList(@ModelAttribute ProductSearch productSearch, ModelMap modelMap){
    Page<Product> productPage = productService.getProductList(productSearch, 8);
    Pagination pagination = new Pagination((int) productPage.getTotalElements(), 8, productSearch.getPage(), 5);

    modelMap.addAttribute("list", productPage);
    modelMap.addAttribute("pager", pagination);

    return "products/search";
}
```
> 요청을 처리하는 Controller

요청을 처리하고 출력하는 코드에서 별다른 처리 없이 원본 문자열을 그대로 출력하고 있음. 만약, 입력값에 자바스크립트를 삽입한다면 공격자가 원하는 입력값으로 자바스크립트를 실행시킬 수 있게 됨

NT11622.scc.svc.ad1.io.navercorp.com/products?keyword=<script>alert(1);</script>

링크에 접속 시 접속한 유저의 브라우저에 alert창이 뜨게됨.

## 영향력
XSS 취약점의 영향력은 브라우저의 자바스크립트로 할 수 있는 모든 행위. 대표적으로 자바스크립트를 실행해 쿠키를 가로채어 세션을 획득하거나 사용자에게 특정한 행위를 유도 할 수 있음.  CSRF(Cross-site request forgery)취약점과 연계될 수도 있음. 그 외에도 브라우저 취약점을 이용한 악성코드 다운로드, 사용자 브라우징 감시, URL 리다이렉트와 같은 영향력이 있음.

## 대응방안
### 1. HTML Escape : 문자 치환
```
< → &lt; 

> → &gt; 

" → &quot; 

' → &#39;

& → &amp;

기타···
```
Spring의 경우 `HtmlUtils.htmlEscape(String input)` 메소드를 사용하여 문자열 치환을 할 수 있음. 템플릿 엔진을 사용하고 있다면, 대부분의 경우 출력 시 문자열 치환 기능을 제공하고 있음.

### 2. Sanitize untrusted HTML : XSS 필터 사용
유저에게 HTML을 입력받을 때(WYSIWYG 등을 사용할 경우) 주로 사용하는 방법. HTML이 출력돼야 하는 경우, 특정 태그(Tag)를 막는 블랙리스트 방식으로 방어하기에 굉장히 어려움, 유저에게 허용된 안전한 HTML만 입력이 가능하도록 필터를 활용.

```
원본 : <img src='<img src=1\ onerror=alert(1234)>" onerror="alert('XSS')">

필터 : <img src=''><!-- Not Allowed Attribute Filtered ( onerror=alert(1234)) --><img src=1\>" onerror="alert('XSS')"&gt;
```
> Lucy 적용 후 문자열 변화

### 3. Front-End 라이브러리 & 프레임워크를 사용
Front-End 라이브러리 혹은 프레임워크를 사용하는 경우 출력단에서 대부분의 HTML Escape를 지원함.
서버에서 API를 응답할 때 Content-Type이 text/plain로 설정되면 안 됨. text/plain의 경우 응답값에 자바스크립트가 존재할 경우 자바스크립트를 브라우저에서 해석해서 실행하게 됨. 항상 application/json와 같이 자바스크립트가 실행되지 않는 응답값을 사용해야함.

### 4. Security Response Header 사용
다양한 목적의 보안 헤더중 Content Security Policy (CSP) 헤더를 사용해서 XSS가 자체를 차단하거나, XSS가 발생하더라도 위험성을 감소시킬 수 있음.
```
A primary goal of CSP is to mitigate and report XSS attacks. 
XSS attacks exploit the browser's trust of the content received from the server.
```

### 5. 적절한 Content-Type Header 사용
적절한 Content-Type Header를 지정해 적절한 MIME Type을 지정하여 스크립트가 실행될 필요가 없는 경우, 스크립트가 실행되지 않게 할 수 있음.
```
text/html
text/xml
text/javascript
application/javascript
```
> 스크립트가 실행될 수 있는 MIME Type 예시 중 일부

API의 응답 결과를 json을 내려주는 경우 html/text이 아닌 json/application type을 지정하여 스크립트가 실행되지 않도록 함.

### 6. 쿠키 속성 HTTP Only 사용
쿠키의 속성 중 HttpOnly 속성은 자바스크립트에서 해당 쿠키에 접근 하지 못하도록하는 속성.
```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2021 07:28:00 GMT; Secure; HttpOnly
```
> Set-Cookie를 할 때 HttpOnly 속성을 사용할 수 있다

XSS의 영향력을 조금 낮출 수 있는 보조 수단

## 올바르지 않은 대응 방안
### 1. 잘못된 치환
<, >와 같이 특정한 특수문자만 치환하는 경우
```
String replaceString=inputString.replace("<script","");
```
만약 입력값이 <<scriptscript>alert(1);</script>로 입력된다면 블랙리스트에 정의된 문자열이 공백으로 치환되면서 <script>alert('test');</script>라는 정상적인 공격구문을 완성됨.