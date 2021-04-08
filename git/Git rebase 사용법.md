# Git rebase

master에 다른 branch를 병합할 때 쓰이는 방법이 두 가지인데, 하나는 merge이고, 다른 하나는 rebase이다.

merge와 rebase를 했을 때 가장 큰 차이는 “깔끔함”
![이미지](https://miro.medium.com/max/3468/1*eZrqyYU4d-M92fvN95L5jw.png)
merge로 사용하면 모든 commit 을 남기게 되지만, rebase를 이용하면 필요없는 commit을 생략시키기 때문에 master의 commit이 깔끔해짐

# 사용법

1. master에서 feature/test라는 branch를 만들고 퇴근 전까지 열심히 작업했다 가정

```
master > git checkout -b feature/test
feature/test > git add .
feature/test > git commit -m 'fix typo'
```

2. 다음 날 출근해서 다시 남은 작업들을 하고, 퇴근 전에 또 commit을 남김

```
feature/test > git add .
feature/test > git commit -m 'wip'
```

3. 다음 날 또 출근해서 버그를 고침

```
feature/test > git add .
feature/test > git commit -m 'bugfix'
```

4. origin에 push 하기 전에 rebase로 불필요한 commit을 squash해줌

```
feature/test > git rebase -i @~3
(참고: -i는 --interactive 옵션이고, @~3은 최근 3개의 commit을 rebase하겠다는 뜻이다 HEAD~3과 같은 뜻이다.)
```

5. 그러면, 복잡해보이지만 아래와 같은 화면이 보인다. 필요 없는 commit은 s옵션으로 바꿔준다.

```
pick f7f3f6d fix typo
pick 310154e wip   // 여기서 pick을 s로 바꿔준다.
pick a5f4a0d bugfix  // 여기서 pick을 s로 바꿔준다.
# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

6. 편집하면 아래와 같은 모습이고 :wq로 저장

```
pick f7f3f6d fix typo
s 310154e wip
s a5f4a0d bugfix
# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

7. 그러면 아래와 같은 또다른 vi로 commit을 입력할 수 있게 됨.

```
# This is a combination of 3 commits.//이 위치에서 dd를 눌러 줄을 삭제한다.
# The first commit's message is: //이 위치에서 dd를 눌러 줄을 삭제한다.
Task 1/3 //이 위치에서 dd를 눌러 줄을 삭제한다.

# This is the 2nd commit message: //이 위치에서 dd를 눌러 줄을 삭제한다.

wip //이 위치에서 dd를 눌러 줄을 삭제한다.

# This is the 3rd commit message: //이 위치에서 dd를 눌러 줄을 삭제한다.

bugfix //나는 이 commit만 남기고 싶으므로 이건 냅둔다.

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# rebase in progress;
```

8. 정리를 하면 이런 모양이됨. 이 상태에서 :wq를 하면 rebase가 끝나고 브랜치로 다시 돌아오게 된다.

```
bugfix

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# rebase in progress;
```

이렇게 git rebase를 하면 여러 commit을 남겼어도 꼭 필요한 commit만 남길 수 있어서 master의 commit 기록을 보면 커밋이 깔끔함

주의할 사항은 다른 사람들과 함께 쓰고 있는 브랜치에다가 git push를 한 경우에는 가급적 rebase를 쓰지 않는 것이 좋다.

내가 rebase한 내용을 다른 사람이 git pull로 당겨 받으면 엄청난 conflict를 만날 수 있다. local에서 작업하고 origin으로 push하기 전에 깔끔하게 커밋을 정리하는 차원에서 이용하는 것이 좋다.
