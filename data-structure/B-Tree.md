# B-Tree

- 이진트리에서 발전, 모든 리프노드들이 같은 레벨을 가질 수 있도록 자동으로 밸런스를 맞춰주는 트리.
- 정렬된 순서를 보장하고, 멀티레벨 인덱싱을 통한 빠른 검색을 할 수 있기 때문에 DB에서 주로 사용.

## B-Tree 상세

- 이진트리와 다르게 하나의 노드에 많은 수의 정보를 가지고 있음.
- 최대 M개의 자식을 가질 수 있는 B트리를 M차 B트리라고 하며 다음과 같은 특징을 가짐.
  - 노드는 최대 M개 부터 M/2개 까지의 자식을 가질 수 있음.
  - 노드에는 최대 M−1개 부터 [M/2]−1개의 키가 포함될 수 있음.
  - 노드의 키가 x개라면 자식의 수는 x+1개.
  - 최소차수는 자식수의 하한값을 의미하며, 최소차수가 t라면 M=2t−1을 만족. (최소차수 t가 2라면 3차 B트리이며, key의 하한은 1개입니다.)

![B-Tree](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fddbae2c9-da94-457d-bad8-77ff6791255b%2FB%ED%8A%B8%EB%A6%AC%20%EA%B8%B0%EB%B3%B8%20%ED%98%95%ED%83%9C.png)

- 위는 차수가 3인 B트리
- 파란색 부분은 각 노드의 key를 나타냄.
- 빨간색 부분은 자식 노드들을 가르키는 포인터.
- key들은 노드 안에서 항상 정렬된 값을 가지며, 이진검색 트리처럼 각 key들의 왼쪽 자식들은 항상 key보다 작은 값을, 오른쪽은 큰 값을 가짐.

## key 검색과정

루트노드에서 시작, 하향식으로 검색 수행.

1. 루트 노드에서 시작하여 key들을 순회하면서 검사
   1. 만일 k와 같은 key를 찾았다면 검색을 종료.
   2. 검색하는 값과 key들의 대소관계를 비교. 어떠한 key들 사이에 k가 들어간다면 해당 key들 사이의 자식노드로로 내려감.
2. 해당 과정을 리프노드에 도달할 때까지 반복. 리프노드에도 k와 같은 key가 없다면 검색을 실패.

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fb7df8287-2524-4ec0-ad03-b969a8830c8e%2FB%ED%8A%B8%EB%A6%AC%20%EA%B2%80%EC%83%89%201.png)
![2](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fe20bdef7-e106-4c89-9560-d7f57154dce1%2FB%ED%8A%B8%EB%A6%AC%20%EA%B2%80%EC%83%89%202.png)

## key 삽입과정

리프노드 검색은 하향식이지만 노드 분할의 과정은 상향식으로 이루어진다고 볼 수 있음.

1. 트리가 비어있으면 루트 노드를 할당하고 k를 삽입. 만일 루트노드가 가득 찼다면, 노드를 분할하고 리프노드가 생성.
2. 삽입하기에 적절한 리프노드를 찾아 k를 삽입. 삽입 위치는 노드의 key값과 k값을 검색 연산과 동일한 방법으로 비교하면서 찾음.

이후 두가지 케이스로 나뉨

### Case 1. 분할이 일어나지 않는 경우

리프노드가 가득차지 않았다면, 오름차순으로 k를 삽입
![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2F95b4c5c3-c267-4423-865e-778a68ad4a50%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%BD%EC%9E%85%201-1.png)

### Case 2. 분할이 일어나는 경우

리프노드에 key노드가 가득 찬 경우, 노드를 분할.

1. 오름차순으로 요소를 삽입합니다. 노드가 담을 수 있는 최대 key 개수를 초과 됨.
2. 중앙값에서 분할을 수행, 중앙값은 부모 노드로 병합하거나 새로 생성, 왼쪽 키들은 왼쪽 자식으로, 오른쪽 키들은 오른쪽 자식으로 분할.
3. 부모 노드를 검사해서 또 다시 가득 찼다면, 다시 부모노드에서 위 과정을 반복.

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2F4b5003e5-55de-441c-a3ee-15e4db7a2abd%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%BD%EC%9E%85%202-1.png)
![2](https://velog.velcdn.com/images%2Femplam27%2Fpost%2F13ab96a4-04cc-42a7-bb01-eac1276bdf67%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%BD%EC%9E%85%202-2.png)
![3](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fd99cdbc8-c5b4-4667-be7d-2589adca45e8%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%BD%EC%9E%85%202-3.png)

## key 삭제과정

요소를 삭제하기 위해선 아래 과정을 해야 함.

1. 삭제할 키가 있는 노드 검색,
2. 키 삭제,
3. 필요한 경우, 트리 균형 조정

삭제 과정을 이해하기 위해서 몇가지 용어를 정의

- inorder predecessor : 노드의 왼쪽 자손에서 가장 큰 key
- inorder successor : 노드의 오른쪽 자손에서 가장 작은 key
- 부모key: 부모노드의 key들 중 왼쪽 자식으로 본인 노드를 가지고 있는 key값. 단, 마지막 자식노드의 경우에는 부모의 마지막 key입니다.

### Case 1. 삭제할 key k가 리프에 있는 경우

#### Case 1.1) 현재 노드의 key 개수가 최소 key 개수보다 크다면

다른 노드들에 영향 없이 해당 k를 단순 삭제
![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fe0e2045e-33a2-439f-a781-f6f9af8d0b66%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%AD%EC%A0%9C%201-1.png)

#### Case 1.2) 왼쪽 또는 오른쪽 형제 노드의 key가 최소 key 개수 이상이라면

1. 부모 key 값으로 k를 대체
2. 최소키 개수 이상의 키를 가진 형제 노드가 왼쪽 형제라면 가장 큰 값을, 오른쪽 형제라면 가장 작은 값을 부모key로 대체

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2F8e7b0f78-ae26-48df-8925-47171c588c48%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%AD%EC%A0%9C%201-2.png)

#### Case 1.3) 왼쪽, 오른쪽 형제 노드의 key가 최소 key 개수이고, 부모노드의 key가 최소개수 이상이면

1. k를 삭제한 후, 부모key를 형제 노드와 병합.
2. 부모노드의 key개수를 하나 줄이고, 자식 수 역시 하나를 줄여 B-Tree를 유지.

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fdde5e5ae-892c-4d1c-9299-4710023f7531%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%AD%EC%A0%9C%201-3.png)

#### Case 1.4) 자신과 형제, 부모 노드의 key 개수가 모두 최소 key 개수라면

부모노드를 루트노드로 한 부분 트리의 높이가 줄어드는 경우이기 때문에 재구조화의 과정이 일어남.

case3의 2번 과정으로 이동.

### Case 2. 삭제할 key k가 내부 노드에 있고, 노드나 자식에 키가 최소 키수보다 많을 경우

1. 현재 노드의 inorder predecessor 또는 inorder successor와 k의 자리를 바꿈.
2. 리프노드의 k를 삭제하게 되면, 리프노드가 삭제 되었을 때의 조건으로 변합니다. 삭제한 리프노드에 대해서 case 1 조건으로 이동합니다.

### Case 3. 삭제할 key k가 내부 노드에 있고, 노드에 key 개수가 최소key 개수만큼, 노드의 자식 key 개수도 모두 최소 key 개수인 경우

삭제할 key k가 있는 노드도 최소, 자식노드들도 최소의 key 개수를 가지므로, k를 삭제하면 트리의 높이가 줄어들어 재구조화가 일어나는 케이스

1. k를 삭제하고, k의 양쪽 자식을 병합하여 하나의 노드로 만듬.
2. k의 부모key를 인접한 형제 노드에 붙입니다. 이후, 이전에 병합했던 노드를 자식노드로 설정.
3. 해당 과정을 수행하였을 때 부모노드의 개수가 에 따라 이후 수행 과정이 달라짐.
   1. 만일 새로 구성된 인접 형제노드의 key가 최대 key 개수를 넘어갔다면, 삽입 연산의 노드 분할 과정을 수행.
   2. 만일 인접 형제노드가 새로 구성되더라도 원래 k의 부모 노드가 최소 key의 개수보다 작아진다면, 부모 노드에 대하여 2번 과정부터 다시 수행.

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2F84dbc50f-fff4-4207-8e27-a34b9043f798%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%AD%EC%A0%9C%203-1.png)

#### Case 3-3-2) 새로운 트리에서 예시

![1](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fe2f82f30-2f9c-4177-a908-1b5333f8e9d6%2FB%ED%8A%B8%EB%A6%AC%20%EC%82%AD%EC%A0%9C%203-2.png)

> 출처
>
> - https://velog.io/@emplam27/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-B-Tree#key-%EC%82%BD%EC%9E%85%EA%B3%BC%EC%A0%95
