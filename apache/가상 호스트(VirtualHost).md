# 가상 호스트

## IP 주소 한개에 여러 이름기반 웹사이트 운영하기.
서버에 IP 주소가 한개 있고, DNS에서 여러 주소(CNAMES)가 이 컴퓨터를 가리킴. 이 컴퓨터에서 www.example.com과 www.example.org의 웹서버를 실행
```
서버 설정
# 아파치가 포트 80을 사용
Listen 80

# 모든 IP 주소에서 가상호스트 요청을 기다림
NameVirtualHost *:80

<VirtualHost *:80>
    DocumentRoot /www/example1
    ServerName www.example.com

    ...

</VirtualHost>

<VirtualHost *:80>
    DocumentRoot /www/example2
    ServerName www.example.org

    ...

</VirtualHost>
```

www.example.com이 설정파일에 처음으로 나오므로 가장 높은 우선순위를 가지며, 기본 서버가 됨. 어떤 ServerName 지시어에도 해당되지않는 요청은 첫번째 VirtualHost가 서비스 함.

## 여러 IP 주소에서 이름기반 호스트.
서버는 IP 주소가 두개있을 때, 첫번째 주소(172.20.30.40)를 주서버 (server.domain.com)로 서비스하고, 다른 하나(172.20.30.50)에서 여러 가상호스트를 서비스할 것이다.
```
서버 설정
Listen 80

# 172.20.30.40이 주서버가 됨
ServerName server.domain.com
DocumentRoot /www/mainserver

# 다른 주소 등록
NameVirtualHost 172.20.30.50

<VirtualHost 172.20.30.50>
    DocumentRoot /www/example1
    ServerName www.example.com

    ...

</VirtualHost>

<VirtualHost 172.20.30.50>
    DocumentRoot /www/example2
    ServerName www.example.org

    ...

</VirtualHost>
```
172.20.30.50이 아닌 주소에 대해서만 주서버가 서비스함.