# Deployment
Deployment는 쿠버네티스에서 가장 널리 사용되는 오브젝트. ReplicaSet을 이용하여 Pod을 업데이트하고 이력을 관리하여 롤백하거나 특정 버전으로 돌아갈 수 있음.

## Deployment 만들기
```
# guide/deployment/echo-deployment.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-deploy
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

type을 제외하곤 ReplicaSet과 완전히 동일

결과 또한 ReplicaSet과 비슷해 보이지만 Deployment의 진가는 Pod을 새로운 이미지로 업데이트할 때 발휘됨

기존 설정에서 이미지 태그만 변경하고 다시 적용

```
# guide/deployment/echo-deployment-v2.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-deploy
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
          image: ghcr.io/subicura/echo:v2
```

Pod이 모두 새로운 버전으로 업데이트됨.

Deployment는 새로운 이미지로 업데이트하기 위해 ReplicaSet을 이용함. 버전을 업데이트하면 새로운 ReplicaSet을 생성하고 해당 ReplicaSet이 새로운 버전의 Pod을 생성됨.

![Deployment1](https://subicura.com/k8s/assets/img/deploy-1.291861bf.png)

새로운 ReplicaSet을 0 -> 1개로 조정하고 정상적으로 Pod이 동작하면 기존 ReplicaSet을 4 -> 3개로 조정함.

![Deployment2](https://subicura.com/k8s/assets/img/deploy-2.9a98dc68.png)

새로운 ReplicaSet을 1 -> 2개로 조정하고 정상적으로 Pod이 동작하면 기존 ReplicaSet을 3 -> 2개로 조정함.

![Deployment3](https://subicura.com/k8s/assets/img/deploy-3.8b5491c5.png)

새로운 ReplicaSet을 2 -> 3개로 조정하고 정상적으로 Pod이 동작하면 기존 ReplicaSet을 2 -> 1개로 조정함.

![Deployment4](https://subicura.com/k8s/assets/img/deploy-4.680a0d2d.png)

최종적으로 새로운 ReplicaSet을 4개로 조정하고 정상적으로 Pod이 동작하면 기존 ReplicaSet을 0개로 조정, 업데이터 완료.
![Deployment5](https://subicura.com/k8s/assets/img/deploy-5.0b85d0bc.png)

생성한 Deployment의 상세 상태 조회
```
kubectl describe deploy/echo-deploy
```

Deployment Controller 동작 원리
1. Deployment Controller는 Deployment조건을 감시하면서 현재 상태와 원하는 상태가 다른 것을 체크
2. Deployment Controller가 원하는 상태가 되도록 ReplicaSet 설정
3. ReplicaSet Controller는 ReplicaSet조건을 감시하면서 현재 상태와 원하는 상태가 다른 것을 체크
4. ReplicaSet Controller가 원하는 상태가 되도록 Pod을 생성하거나 제거
5. Scheduler는 API서버를 감시하면서 할당되지 않은unassigned Pod이 있는지 체크
6. Scheduler는 할당되지 않은 새로운 Pod을 감지하고 적절한 노드node에 배치
7. 이후 노드는 기존대로 동작

## 버전관리

Deployment는 변경된 상태를 기록함.
```
# 히스토리 확인
kubectl rollout history deploy/echo-deploy

# revision 1 히스토리 상세 확인
kubectl rollout history deploy/echo-deploy --revision=1

# 바로 전으로 롤백
kubectl rollout undo deploy/echo-deploy

# 특정 버전으로 롤백
kubectl rollout undo deploy/echo-deploy --to-revision=2
```

## 배포 전략 설정
Deployment 다양한 방식의 배포 전략이 있음. 롤링업데이트 방식을 사용할 때 동시에 업데이트하는 Pod의 개수를 변경
```
# guide/deployment/echo-strategy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-deploy-st
spec:
  replicas: 4
  selector:
    matchLabels:
      app: echo
      tier: app
  minReadySeconds: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 3
      maxUnavailable: 3
  template:
    metadata:
      labels:
        app: echo
        tier: app
    spec:
      containers:
        - name: echo
          image: ghcr.io/subicura/echo:v1
          livenessProbe:
            httpGet:
              path: /
              port: 3000
```

```
# 이미지 변경 (명령어로)
kubectl set image deploy/echo-deploy-st echo=ghcr.io/subicura/echo:v2

# 이벤트 확인
kubectl describe deploy/echo-deploy-st
```

## 마무리
Deployment는 가장 흔하게 사용하는 배포방식. 이외에 StatefulSet, DaemonSet, CronJob, Job등이 있지만 사용법은 크게 다르지 않음.