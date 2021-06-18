# Service
Pod은 자체 IP를 가지고 다른 Pod과 통신할 수 있지만, 쉽게 사라지고 생성되는 특징 때문에 직접 통신하는 방법은 권장되지 않음. 쿠버네티스는 Pod과 직접 통신하는 방법 대신, 별도의 고정된 IP를 가진 서비스를 만들고 그 서비스를 통해 Pod에 접근하는 방식을 사용함.
![Service](https://subicura.com/k8s/assets/img/pod-service.1c328260.png)

## Service(ClusterIP) 만들기
ClusterIP는 클러스터 내부에 새로운 IP를 할당하고 여러 개의 Pod을 바라보는 로드밸런서 기능을 제공함. 그리고 서비스 이름을 내부 도메인 서버에 등록하여 Pod 간에 서비스 이름으로 통신할 수 있음.

```
# guide/service/counter-redis-svc.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: counter
      tier: db
  template:
    metadata:
      labels:
        app: counter
        tier: db
    spec:
      containers:
        - name: redis
          image: redis
          ports:
            - containerPort: 6379
              protocol: TCP

---
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  ports:
    - port: 6379
      protocol: TCP
  selector:
    app: counter
    tier: db
```
> 하나의 YAML파일에 여러 개의 리소스를 정의할 땐 "---"를 구분자로 사용합니다.

```
kubectl apply -f counter-redis-svc.yml

# Pod, ReplicaSet, Deployment, Service 상태 확인
kubectl get all

```

실행결과 redis Deployment와 Service가 생성됨.

![Service(ClusterIP) 만들기](https://subicura.com/k8s/assets/img/clusterip.2e0bd0bf.png)

같은 클러스터에서 생성된 Pod이라면 redis(redis.default.svc.cluster.local)라는 도메인으로 redis Pod에 접근 할 수 있음

CluterIP 서비스의 설정
- spec.ports.port: 서비스가 생성할 Port
- spec.ports.targetPort: 서비스가 접근할 Pod의 Port (기본: port랑 동일)
- spec.selector: 서비스가 접근할 Pod의 label 조건

redis에 접근할 counter 앱을 Deployment로 만듦
```
# guide/service/counter-app.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter
spec:
  selector:
    matchLabels:
      app: counter
      tier: app
  template:
    metadata:
      labels:
        app: counter
        tier: app
    spec:
      containers:
        - name: counter
          image: ghcr.io/subicura/counter:latest
          env:
            - name: REDIS_HOST
              value: "redis"
            - name: REDIS_PORT
              value: "6379"
```

```
kubectl apply -f counter-app.yml

# counter app에 접근
kubectl get po
kubectl exec -it counter-<xxxxx> -- sh

# apk add curl busybox-extras # install telnet
# curl localhost:3000
# curl localhost:3000
# telnet redis 6379
  dbsize
  KEYS *
  GET count
  quit
```

Service를 통해 Pod과 성공적으로 연결


## Service 생성 흐름
Service는 각 Pod를 바라보는 로드밸런서 역할을 하면서 내부 도메인서버에 새로운 도메인을 생성함.

1. Endpoint Controller는 Service와 Pod을 감시하면서 조건에 맞는 Pod의 IP를 수집
2. Endpoint Controller가 수집한 IP를 가지고 Endpoint 생성
3. Kube-Proxy는 Endpoint 변화를 감시하고 노드의 iptables을 설정
4. CoreDNS는 Service를 감시하고 서비스 이름과 IP를 CoreDNS에 추가

iptables는 커널 레벨의 네트워크 도구이고 CoreDNS는 빠르고 편리하게 사용할 수 있는 클러스터 내부용 도메인 네임 서버. 각각의 역할은 iptables 설정으로 여러 IP에 트래픽을 전달하고 CoreDNS를 이용하여 IP 대신 도메인 이름을 사용.

> iptables
>
>iptables는 규칙이 많아지면 성능이 느려지는 이슈가 있어, ipvs를 > 사용하는 옵션도 있습니다.

> CoreDNS 
> 
> CoreDNS는 클러스터에서 호환성을 위해 kube-dns라는 이름으로 생성됩니다.

Endpoint는 서비스의 접속 정보를 가지고 있음.
```
kubectl get endpoints
kubectl get ep #줄여서

# redis Endpoint 확인
kubectl describe ep/redis
```

## Service(NodePort) 만들기
CluterIP는 클러스터 내부에서만 접근할 수 있습니다. 클러스터 외부(노드)에서 접근할 수 있도록 NodePort 서비스를 만듦
```
# guide/service/counter-nodeport.yml
apiVersion: v1
kind: Service
metadata:
  name: counter-np
spec:
  type: NodePort
  ports:
    - port: 3000
      protocol: TCP
      nodePort: 31000
  selector:
    app: counter
    tier: app
```

- spec.ports.nodePort : 노드에 오픈할 Port (미지정시 30000-32768 중에 자동 할당)

```
kubectl apply -f counter-nodeport.yml

# 서비스 상태 확인
kubectl get svc
```

minikube ip로 테스트 클러스터의 노드 IP를 구하고 31000으로 접근
```
curl 192.168.64.4:31000 # 또는 브라우저에서 접근
```

![Service(NodePort) 만들기](https://subicura.com/k8s/assets/img/nodeport.8677d87b.png)

NodePort는 클러스터의 모든 노드에 포트를 오픈함. 지금은 테스트라서 하나의 노드밖에 없지만 여러 개의 노드가 있다면 아무 노드로 접근해도 지정한 Pod으로 쏘옥 접근할 수 있음.

![Service(NodePort) 만들기2](https://subicura.com/k8s/assets/img/nodeport-multi.8a7b8dce.png)

> NodePort와 ClusterIP
>
> NodePort는 CluterIP의 기능을 기본으로 포함합니다.

## Service(LoadBalancer) 만들기
NodePort의 단점은 노드가 사라졌을 때 자동으로 다른 노드를 통해 접근이 불가능하다는 점. 예를 들어, 3개의 노드가 있다면 3개 중에 아무 노드로 접근해도 NodePort로 연결할 수 있지만 어떤 노드가 살아 있는지는 알 수가 없음.

![Service(LoadBalancer) 만들기](https://subicura.com/k8s/assets/img/lb.1dff4c9d.png)

자동으로 살아 있는 노드에 접근하기 위해 모든 노드를 바라보는 Load Balancer가 필요함. 브라우저는 NodePort에 직접 요청을 보내는 것이 아니라 Load Balancer에 요청하고 Load Balancer가 알아서 살아 있는 노드에 접근하면 NodePort의 단점을 없앨 수 있음.

```
# guide/service/counter-lb.yml
apiVersion: v1
kind: Service
metadata:
  name: counter-lb
spec:
  type: LoadBalancer
  ports:
    - port: 30000
      targetPort: 3000
      protocol: TCP
  selector:
    app: counter
    tier: app
```
counter-lb가 생성되었지만, EXTERNAL-IP가 `<pending>`. 사실 Load Balancer는 AWS, Google Cloud, Azure 같은 클라우드 환경이 아니면 사용이 제한적. 특정 서버(노드)를 가리키는 무언가(Load Balancer)가 필요

## minikube에 가상 LoadBalancer 만들기
Load Balancer를 사용할 수 없는 환경에서 가상 환경을 만들어 주는 것이 MetalLB라는 것. minikube에서는 현재 떠 있는 노드를 Load Balancer로 설정함.

```
minikube addons enable metallb
```

minikube ip명령어로 확인한 ip를 ConfigMap으로 지정

```
# guide/service/metallb-cm.yml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.49.2/32 # minikube ip
```
```
kubectl apply -f metallb-cm.yml

# 다시 서비스 확인
kubectl get svc
```

> LoadBalancer와 NodePort
>
> LoadBalancer는 NodePort의 기능을 기본으로 포함합니다.

## 마무리

서비스는 로우레벨 수준의 네트워크를 이해하고 성능, 보안 이슈를 신경 써야 함.

실전에선 NodePort와 LoadBalancer를 제한적으로 사용. 보통 웹 애플리케이션을 배포하면 80 또는 443 포트를 사용하고 하나의 포트에서 여러 개의 서비스를 도메인이나 경로에 따라 다르게 연결하기 때문