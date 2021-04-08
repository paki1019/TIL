pull-request 를 보냈지만 시작시점부터 설계를 잘못했거나 코드베이스를 잘못 이해해서 리셋을 통해 아예 새로 시작하는 마음으로 pull-request를 보내야 하는 경우, 내가 이전에 작성한 코드를 모두 날리지 않고 제대로 작성된 코드(커밋)를 선택적으로 가져오는 방법

브랜치가 아래와 같다고 가정
![이미지](https://miro.medium.com/max/700/1*2cn1RLPqkofLuZiKxFHUhw.png)

```
+ feature 브랜치에서 다음처럼 잘못된 커밋을 함
+  - 체리픽 커밋은 다른 브랜치를 내가 작업한 브랜치로 합치는 커밋
```

보통의 경우라면 이 부분을 수정하거나 삭제하고 다시 커밋을 하겠지만 conflict등의 여러가지 복잡한 경우가 있다고 가정하고 작성의도에 맞게 cherry-pick커밋을 활용

이전 브랜치(feature/add-title)는 남겨둔 채 다음처럼 master 브랜치 에서 feature/add-title-correct 브랜치를 새로 만들고 checkout
![이미지](https://miro.medium.com/max/700/1*l6vQCkuYSZ5Pa_YH78vc-A.png)

git cherry-pick명령어를 통해 내가 원하는(올바른) 커밋만 가져 옴

```
git cherry-pick b8ffcad(가져 가고 싶은 커밋넘버)
```

명령어를 실행하고 히스토리를 보면 내가 원하는 커밋이 새로운 브랜치에 들어와 있음을 확인할 수 있음
![이미지](https://miro.medium.com/max/700/1*8C1sLNXIR7EjGGVevnWs7Q.png)
