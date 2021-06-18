# ReplicaSet
Pod을 단독으로 만들면 Pod에 어떤 문제(서버가 죽어서 Pod이 사라졌다던가)가 생겼을 때 자동으로 복구되지 않음. 이러한 Pod을 정해진 수만큼 복제하고 관리하는 것이 ReplicaSet.

## ReplicaSet 만들기
```
# guide/replicaset/echo-rs.yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: echo-rs
spec:
  replicas: 1
  selector:
    matchLabels:
      app: echo
      tier: app
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
```
ReplicaSet과 Pod이 같이 생성됨
![ReplicaSet 만들기](https://subicura.com/k8s/assets/img/rs.09734c4b.png)

- spec.selector	: label 체크 조건
- spec.replicas	: 원하는 Pod의 개수
- spec.template	: 생성할 Pod의 명세

template을 자세히 보면 이전에 본 Pod 설정 파일과 완전히 동일한 것을 알 수 있음.
```
metadata:
  labels:
    app: echo
    tier: app
spec:
  containers:
    - name: echo
      image: ghcr.io/subicura/echo:v1
```

생성된 Pod의 label을 확인
```
kubectl get pod --show-labels
```

설정한 대로 app=echo,tier=app label이 보임, 임의로 label을 제거할 시 selector에 정의한 app=echo,tier=app 조건을 만족하는 Pod의 개수가 0이 되어 새로운 Pod이 만들어짐. 
```
# app- 를 지정해서 app label을 제거
kubectl label pod/echo-rs-tcdwj app-

# 다시 Pod 확인
kubectl get pod --show-labels
```

다시 app label을 추가하면 replicas에 정의한 대로 Pod의 개수를 1로 유지하기 위해 기존 Pod을 제거함.
```
# app- 를 지정해서 app label을 추가
kubectl label pod/echo-rs-tcdwj app=echo

# 다시 Pod 확인
kubectl get pod --show-labels
```

ReplicaSet 동작 원리
1. ReplicaSet Controller는 ReplicaSet조건을 감시하면서 현재 상태와 원하는 상태가 다른 것을 체크
2. ReplicaSet Controller가 원하는 상태가 되도록 Pod을 생성하거나 제거
3. Scheduler는 API서버를 감시하면서 할당되지 않은 Pod이 있는지 체크
4. Scheduler는 할당되지 않은 새로운 Pod을 감지하고 적절한 노드에 배치
5. 이후 노드는 기존대로 동작

## 스케일 아웃
ReplicaSet을 이용하면 손쉽게 Pod을 여러개로 복제할 수 있음.
```
# guide/replicaset/echo-rs-scaled.yml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: echo-rs
spec:
  replicas: 4
  selector:
    matchLabels:
      app: echo
      tier: app
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
```
기존에 생성된 Pod외에 3개가 추가됨.

## 마무리
ReplicaSet은 원하는 개수의 Pod을 유지하는 역할을 담당함.  label을 이용하여 Pod을 체크하기 때문에 label이 겹치지 않게 신경써서 정의해야 함.