# Git Branch 종류(5가지)

Gitflow Workflow에서는 항상 유지되는 메인 브랜치들(master, develop)과 일정 기간 동안만 유지되는 보조 브랜치들(feature, release, hotfix)을 포함하여 총 5가지의 브랜치를 사용

![Git Branch 종류(5가지)](https://gmlwjd9405.github.io/images/types-of-git-branch/total-branch.png)

## 1. Master Branch

**제품으로 출시될 수 있는 브랜치**

배포(Release) 이력을 관리하기 위해 사용. 즉, 배포 가능한 상태만을 관리

![Master Branch](https://gmlwjd9405.github.io/images/types-of-git-branch/develop-branch.svg)

## 2. Develop Branch

**다음 출시 버전을 개발하는 브랜치**
기능 개발을 위한 브랜치들을 병합하기 위해 사용. 모든 기능이 추가되고 버그가 수정되어 배포 가능한 안정적인 상태라면 develop 브랜치를 `master` 브랜치에 병합(merge)한다.
평소에는 이 브랜치를 기반으로 개발을 진행.

![Develop Branch](https://gmlwjd9405.github.io/images/types-of-git-branch/develop-branch.png)

## 3. Feature Branch

**기능을 개발하는 브랜치**
feature 브랜치는 새로운 기능 개발 및 버그 수정이 필요할 때마다 `develo` 브랜치로부터 분기.  
feature 브랜치에서의 작업은 기본적으로 공유할 필요가 없기 때문에, 자신의 로컬 저장소에서 관리함. 개발이 완료되면 `develop` 브랜치로 병합(merge)하여 다른 사람들과 공유

1. `develop` 브랜치에서 새로운 기능에 대한 feature 브랜치를 분기.
2. 새로운 기능에 대한 작업 수행.
3. 작업이 끝나면 `develop` 브랜치로 병합(merge).
4. 더 이상 필요하지 않은 `feature` 브랜치는 삭제.
5. 새로운 기능에 대한 `feature` 브랜치를 중앙 원격 저장소에 올린다.(push)

- `feature` 브랜치 이름 정하기
  - master, develop, release-(RB\_), or hotfix- 제외
  - [feature/기능요약] 형식을 추천 EX) feature/login
- `--no-ff` 옵션
  - 새로운 커밋 객체를 만들어 `develop` 브랜치에 merge 함.
  - 이것은 `feature` 브랜치에 존재하는 커밋 이력을 모두 합쳐서 하나의 새로운 커밋 객체를 만들어 `develop` 브랜치로 병합(merge)하는 것

![--no-ff](https://gmlwjd9405.github.io/images/types-of-git-branch/feature-branch-merge.png)

## 4. Release Branch

**이번 출시 버전을 준비하는 브랜치**
배포를 위한 전용 브랜치를 사용함으로써 한 팀이 해당 배포를 준비하는 동안 다른 팀은 다음 배포를 위한 기능 개발을 계속할 수 있음.

1. `develop` 브랜치에서 배포할 수 있는 수준의 기능이 모이면 또는 정해진 배포 일정이 되면, release 브랜치를 분기.
   - release 브랜치를 만드는 순간부터 배포 사이클 시작.
   - release 브랜치에서는 배포를 위한 최종적인 버그 수정, 문서 추가 등 릴리스와 직접적으로 관련된 작업을 수행.
   - 직접적으로 관련된 작업들을 제외하고는 release 브랜치에 새로운 기능을 추가로 병합(merge)하지 않음.
2. `release` 브랜치에서 배포 가능한 상태가 되면(배포 준비가 완료되면), - `master` 브랜치에 병합. (이때, 병합한 커밋에 Release 버전 태그를 부여) - 배포를 준비하는 동안 release 브랜치가 변경되었을 수 있으므로 배포 완료 후 ‘develop’ 브랜치에도 병합.

![Release Branch](https://gmlwjd9405.github.io/images/types-of-git-branch/release-branch.svg)

## 5. Hotfix Branch

**출시 버전에서 발생한 버그를 수정하는 브랜치**

배포한 버전에 긴급하게 수정을 해야 할 필요가 있을 경우, `master` 브랜치에서 분기하는 브랜치. `develop` 브랜치에서 문제가 되는 부분을 수정하여 배포 가능한 버전을 만들기에는 시간도 많이 소요되고 안정성을 보장하기도 어려우므로 바로 배포가 가능한 `master` 브랜치에서 직접 브랜치를 만들어 필요한 부분만을 수정한 후 다시 `master`브랜치에 병합하여 이를 배포해야 하는 것.

1. 배포한 버전에 긴급하게 수정을 해야 할 필요가 있을 경우,
   - `master` 브랜치에서 `hotfix` 브랜치를 분기. (`hotfix` 브랜치만 master에서 바로 딸 수 있음)
2. 문제가 되는 부분만을 빠르게 수정.
   - 다시 `master` 브랜치에 병합(merge)하여 이를 안정적으로 다시 배포.
   - 새로운 버전 이름으로 태그를 매김.
3. hotfix 브랜치에서의 변경 사항은 'develop' 브랜치에도 병합(merge)한다.

![Hotfix Branch](https://gmlwjd9405.github.io/images/types-of-git-branch/hotfix-branch.png)

## Branch의 전체 흐름

![Branch의 전체 흐름](https://gmlwjd9405.github.io/images/types-of-git-branch/hotfix-branch.svg)

> ## 출처
>
> - https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html
