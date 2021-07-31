# Pod

Pod은 쿠버네티스에서 관리하는 가장 작은 배포 단위

쿠버네티스와 도커의 차이점은 도커는 컨테이너를 만들지만, 쿠버네티스는 컨테이너 대신 Pod을 만듬. Pod은 한 개 또는 여러 개의 컨테이너를 포함함.

## 빠르게 Pod 만들기
```
# echo라는 이름의 Pod 생성
kubectl run echo --image ghcr.io/subicura/echo:v1
```

```
# Pod 목록 조회
kubectl get pod
```

```
# 단일 Pod 상세 확인
kubectl describe pod/echo
```

## Pod 생성 분석
minikube 클러스터 안에 Pod이 있고 Pod 안에 컨테이너가 있음

kubectl run을 실행하고 Pod이 생성되는 과정
1. Scheduler는 API서버를 감시하면서 할당되지 않은 unassigned Pod이 있는지 체크
2. Scheduler는 할당되지 않은 Pod을 감지하고 적절한 노드node에 할당 (minikube는 단일 노드)
3. 노드에 설치된 kubelet은 자신의 노드에 할당된 Pod이 있는지 체크
4. kubelet은 Scheduler에 의해 자신에게 할당된 Pod의 정보를 확인하고 컨테이너 생성
5. kubelet은 자신에게 할당된 Pod의 상태를 API 서버에 전달

Pod 제거
```
kubectl delete pod/echo
```

## YAML로 설정파일Spec 작성하기
원하는 리소스를 YAML 파일로 작성하면 복잡한 내용을 표현하기 좋고 변경된 내용을 버전 관리할 수 있음.

```
# guide/pod/echo-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: echo
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
```

version, kind, metadata, spec는 리소스를 정의할 때 반드시 필요한 요소

```
# Pod 생성
kubectl apply -f echo-pod.yml

# Pod 목록 조회
kubectl get pod

# Pod 로그 확인
kubectl logs echo
kubectl logs -f echo

# Pod 컨테이너 접속
kubectl exec -it echo -- sh
# ls
# ps
# exit

# Pod 제거
kubectl delete -f echo-pod.yml
```

## 컨테이너 상태 모니터링
컨테이너 생성과 실제 서비스 준비는 약간의 차이가 있음. 서버를 실행하면 바로 접속할 수 없고 짧게는 수초, 길게는 수분의 초기화 시간이 필요한데 실제로 접속이 가능할 때 서비스가 준비되었다고 말할 수 있음.
![컨테이너 상태 모니터링](https://subicura.com/k8s/assets/img/pod-monitoring.0b0e0d5a.png)

쿠버네티스는 컨테이너가 생성되고 서비스가 준비되었다는 것을 체크하는 옵션을 제공하여 초기화하는 동안 서비스되는 것을 막을 수 있음.

## livenessProbe
컨테이너가 정상적으로 동작하는지 체크하고 정상적으로 동작하지 않는다면 컨테이너를 재시작하여 문제를 해결함.

정상이라는 것은 여러 가지 방식으로 체크할 수 있는데 여기서는 http get 요청을 보내 확인하는 방법을 사용

```
# guide/pod/echo-lp.yml
apiVersion: v1
kind: Pod
metadata:
  name: echo-lp
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      livenessProbe:
        httpGet:
          path: /not/exist
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2 # Default 1
        periodSeconds: 5 # Defaults 10
        failureThreshold: 1 # Defaults 3
```

일부러 존재하지 않는 path(/not/exist)와 port(8080)를 입력, 정상적으로 응답하지 않았기 때문에 Pod이 여러 번 재시작되고 CrashLoopBackOff 상태로 변경됨.

## readinessProbe
컨테이너가 준비되었는지 체크하고 정상적으로 준비되지 않았다면 Pod으로 들어오는 요청을 제외함.

livenessProbe와 차이점은 문제가 있어도 Pod을 재시작하지 않고 요청만 제외한다는 점

```
# guide/pod/echo-rp.yml
apiVersion: v1
kind: Pod
metadata:
  name: echo-rp
  labels:
    app: echo
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/echo:v1
      readinessProbe:
        httpGet:
          path: /not/exist
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 2 # Default 1
        periodSeconds: 5 # Defaults 10
        failureThreshold: 1 # Defaults 3
```

## 다중 컨테이너
대부분 1 Pod = 1 컨테이너지만 여러 개의 컨테이너를 가진 경우도 꽤 흔함.

하나의 Pod에 속한 컨테이너는 서로 네트워크를 localhost로 공유하고 동일한 디렉토리를 공유할 수 있음.
```
# guide/pod/counter-pod-redis.yml
apiVersion: v1
kind: Pod
metadata:
  name: counter
  labels:
    app: counter
spec:
  containers:
    - name: app
      image: ghcr.io/subicura/counter:latest
      env:
        - name: REDIS_HOST
          value: "localhost"
    - name: db
      image: redis
```

## 마무리
쿠버네티스는 멀티 컨테이너를 이용한 다양한 패턴이 존재함. 로그를 수집하는 별도의 컨테이너를 같은 Pod으로 배포한다던가, 서버가 실행되기 전 데이터베이스를 마이그레이션 하는 초기화 컨테이너를 만들 수도 있음.