# Git

## 거의 모든 명령을 로컬에서 실행

Git은 프로젝트의 히스토리를 조회할 때 서버 없이 조회한다. 그래서 속도가 매우매우 빠르다. 네트워크에 연결이 되어 있지 않더라도 커밋을 할 수 있다.

## Git의 무결성

Git은 데이터를 저장하기 전에 항상 체크섬을 구하고 그 체크섬으로 데이터를 관리한다. 

Git은 SHA-1 해시를 사용하여 체크섬을 만든다. 만든 체크섬은 40자 길이의 16진수 문자열이다. 파일의 내용이나 디렉터리 구조를 이용하여 체크섬을 구한다. 

## Git의 세 가지 상태

- Committed: 데이터가 로컬 데이터베이스에 안전하게 저장됐다는 것을 의미
- Modified: 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않은 것을 의미
- Staged: 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태를 의미

```code
git add [파일명]
```

add 명령으로 파일을 새로 추적할 수 있다. 

```code
git status
```

status 명령으로 add한 파일이 Tracked 상태이며 커밋에 추가될 Staged 상태임을 확인할 수 있다.

## Staged와 Unstaged 상태의 변경 내용을 보기

git status로 어떤 파일이 Staged인지 확인할 수 있지만 변경 내용을 볼 수는 없었다. 더 자세히 보려면 git diff 명령을 사용한다.

```code
git diff
```

만약 커밋하고 Staged 상태의 변경 사항을 보려면 다음과 같이 하면 된다.

```code
git diff --staged
```

## 커밋 히스토리 조회하기

```code
git log
```

단순 조회는 git log로 볼 수 있다.

```code
git log -p -2
```

-p 혹은 --patch는 굉장히 유용한 옵션인데 각 커밋의 diff 결과를 보여준다. -2는 최근 두 개의 결과만 보여주는 옵션이다. 위의 diff와 마찬가지로 어디가 어떻게 수정됐는지 보여준다.

```code
git log --stat
```

이 옵션은 어떤 파일이 수정됐는지 얼마나 많은 파일이 변경됐는지 얼마나 많은 라인을 추가하거나 삭제했는지를 보여준다.

## Git 되돌리기

### 커밋 이후 파일을 빠트린 경우

```code
git add [빠트린 파일 혹은 수정한 파일]
git commit --amend
```

수정 혹은 추가할 파일을 Staging Area에 추가한 이후 (add) --amend 옵션으로 커밋에 포함시킬 수 있다. 결과적으로 한 커밋에 기록할 수 있다.

### 실수로 두 개의 파일을 한 번에 커밋한 경우

두 개의 파일을 나눠서 커밋을 하려고 했지만 실수로 둘 다 add한 경우의 해결책이다. 현재 둘 다 Staging Area에 들어가 있다. 

```
git reset head [파일명]

```

이렇게 reset 명령으로 **한 파일을 Unstaged 상태**로 만들 수 있다.

## 브랜치 만들기

브랜치를 만드려면

```code
git branch [브랜치명]
git checkout [브랜치명]

```

혹은 

```code
git branch -b [브랜치명]

```

으로 한 번에 checkout과 함께 진행할 수 있다.

### 브랜치 관리

```code
git branch

```

아무 옵션없이 실행하면 그냥 모든 브랜치의 목록을 보여준다.

```code
git branch -v

```

이 명령어는 브랜치마다 마지막 커밋 메세지까지 함께 보여준다.

```code
git branch --merged

```

브랜치 중 merge된 브랜치의 목록을 보여준다. 

```code
git branch --no-merged

```

merge되지 않은 브랜치의 목록을 보여준다. merge 되지 않았기 때문에 git branch -d 로 브랜치를 삭제할 수 없다. 강제 삭제는 -D 옵션이다.

## Reset 활용

Git은 일반적으로 세 가지 트리를 관리하는 시스템이다.

### HEAD

head는 현재 브랜치를 가리키는 포인터이다. 

### Index

Index는 바로 다음에 커밋할 것들이다. 이 곳이 Staging Area이다.

### Reset의 동작

reset 명령이 하는 첫 번째 일은 head 브랜치를 이동시킨다. 브랜치 자체의 이동이 아니라 현재 브랜치가 가리키는 커밋을 바꾸는 것이다. 

```code
git reset --soft HEAD~

```

reset 명령은 가장 최근의 git commit 명령을 되돌린다 HEAD~ 옵션을 주면 Index나 워킹 디렉토리는 그대로 놔두고 브랜치가 가리키는 커밋만 이전으로 돌린다. 이 상태에서 Index 업데이트 후에 git commit을 주면 git commit --amend 명령과 같아진다.

### Index 업데이트

```code
git reset --mixed HEAD~
git reset HEAD~

```

기본적으로 reset의 디폴트는 --mixed 옵션으로 동작한다. 이것은 가장 최근의 커밋을 되돌리고 Staging Area를 비운다. 

### 워킹 디렉토리 업데이트 

```code
git reset --hard HEAD~

```

--hard 옵션은 reset 명령을 위험하게 만드는 유일한 옵션이다. Git에는 데이터를 실제로 삭제하는 방법이 별로 없지만 --hard는 삭제하는 방법 중 하나이다. 이 옵션으로 reset을 하면 되돌리는 것이 불가능하다. 

### Reset 최종 정리

reset하는 순서는 3단계로 이루어져있다. 옵션에 따라 어느 단계에서 멈출지 결정을 하는 것이다.

1. HEAD가 가리키는 브랜치를 옮기는 것 (--soft)
2. Index를 HEAD 가 가리키는 상태로 만든다 (--mixed)
3. 워킹 디렉토리를 Index의 상태로 만든다 (--hard)

checkout과 다른 점은 checkout은 워킹 디렉토리를 안전하게 다루는 것이다. 그리고 checkout 명령은 HEAD 자체를 옮기고 reset 명령은 HEAD 가 가리키는 브랜치의 포인터를 옮긴다.

## 고급 Merge

### Merge 취소하기

Merge 중에 발생한 충돌을 해결하는 방법은 몇 가지 있다. 첫 번째는 그저 이 상황을 벗어나는 것이다.

```code
git merge --abort

```

아니면 merge를 처음부터 다시 시작하고 싶다면

```
git reset --hard HEAD

```

명령으로 되돌릴 수 있다.

### Merge 되돌리기

토픽 브랜치에서 일을 하다가 로컬에서 실수로 마스터에 잘못 merge를 한 상황이다. 이렇게 우발적으로 merge를 했다면 (그리고 로컬 저장소에서 발생한 일이라면) reset을 해주면 된다.

```code
git reset --hard HEAD~

```

이런식으로 hard를 줘서 날리면 된다.

### Rebase 되돌리기

```code
git reflog [브랜치명]

```

reflog 명령어로 로그들을 볼 수 있다. 그리고 돌아가고 싶은 부분을 찾는다.

```code
git reset --hard [SHA-1해시값]

```

log를 확인하고 해당 해시값을 찾고 저 명령어를 실행해준다.

```code
git push -f origin [브랜치명]

```

그리고 리셋한 커밋이력을 리모트로 강제 푸시를 해주면 된다.

### 과거의 커밋 내용 수정하기

```code
git rebase -i [SHA-1해시값]
git commit --amend
git rebase --continue

```

수정을 한 후 amend를 해주고 그리고 continue를 한다.

### 과거의 커밋 메세지만을 수정하기

위와 완전히 같은데 rebase를 할때 선택할 단어를 고르면 된다. 거기서 수정하고 싶은 커밋을 찾고 edit 옵션으로 수정하고 저장한다. 커밋메세지를 수정하고 git commit --amend 이후 git rebase --continue를 해준다.

### Rebase 중지하기

```code
git rebase --abort

```

