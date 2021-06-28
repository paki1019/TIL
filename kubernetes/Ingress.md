# Ingress
![Ingress](https://subicura.com/k8s/assets/img/ingress.d6775ca6.png)
`example.com`, `subicura.com/blog`, `subicura.com/help` 주소로 서로 다른 서비스에 접근하는 모습. `80(http)` 또는 `443(https)` 포트로 여러 개의 서비스를 연결해야 하는데 이럴 때 Ingress를 사용

## Ingress 만들기
`minikube ip`로 테스트 클러스터의 노드 IP를 구하고 도메인 주소로 사용

- v1.echo.192.168.64.2.sslip.io(opens new window)
- v2.echo.192.168.64.2.sslip.io

## minikube에 Ingress 활성화하기
```
minikube addons enable ingress

# ingress 컨트롤러 확인
kubectl -n kube-system get pod
```
```
curl -I http://192.168.64.2/healthz # minikube ip를 입력
```

## echo 웹 애플리케이션 배포
```
# guide/ingress/echo-v1.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: echo-v1
spec:
  rules:
    - host: v1.echo.192.168.64.5.sslip.io
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: echo-v1
                port:
                  number: 3000

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-v1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: echo
      tier: app
      version: v1
  template:
    metadata:
      labels:
        app: echo
        tier: app
        version: v1
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
          livenessProbe:
            httpGet:
              path: /
              port: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: echo-v1
spec:
  ports:
    - port: 3000
      protocol: TCP
  selector:
    app: echo
    tier: app
    version: v1
```
```
# guide/ingress/echo-v2.yml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: echo-v2
spec:
  rules:
    - host: v2.echo.192.168.64.5.sslip.io
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: echo-v2
                port:
                  number: 3000

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-v2
spec:
  replicas: 3
  selector:
    matchLabels:
      app: echo
      tier: app
      version: v2
  template:
    metadata:
      labels:
        app: echo
        tier: app
        version: v2
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v2
          livenessProbe:
            httpGet:
              path: /
              port: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: echo-v2
spec:
  ports:
    - port: 3000
      protocol: TCP
  selector:
    app: echo
    tier: app
    version: v2
```
```
kubectl apply -f echo-v1.yml,echo-v2.yml

# Ingress 상태 확인
kubectl get ingress
kubectl get ing
```
`v1.echo.192.168.64.2.sslip.io`과 `v2.echo.192.168.64.2.sslip.io`로 접속 테스트

## Ingress 생성 흐름
1. Ingress Controller는 Ingress 변화를 체크
2. Ingress Controller는 변경된 내용을 Nginx에 설정하고 프로세스 재시작

동작방식을 보면 YAML로 만든 Ingress 설정을 단순히 nginx 설정으로 바꾸는 것. 이러한 과정을 수동으로 하지 않고 Ingress Controller가 하는 것 뿐

ngress는 도메인, 경로만 연동하는 것이 아니라 요청 timeout, 요청 max size 등 다양한 프록시 서버 설정을 할 수 있음.

> ### 출처
> https://subicura.com/k8s/guide/ingress.html#ingress-%E1%84%89%E1%85%A2%E1%86%BC%E1%84%89%E1%85%A5%E1%86%BC-%E1%84%92%E1%85%B3%E1%84%85%E1%85%B3%E1%86%B7