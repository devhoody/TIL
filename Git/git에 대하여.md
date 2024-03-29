> 뉴렉처 국비과정에서 배운 내용 + a 로 정리하였다.

# Git이란?

- 협업과 버전관리를 위한 분산 버전 관리 시스템
- 여러 사람과 프로젝트시 서로의 코드를 확인하고 코드리뷰를 가능하게해준다.
- 커밋을 통해 진행과정을 세분화하여 저장할 수 있으며, 이전 버전으로 이동이 가능하게 해준다.

# 깃의 버전 확인

- 깃의 버전을 확인하는 방법은 cmd, teminal, VScode의 terminal에서 `git version` 또는 `git -v` 를 이용하면 확인할 수 있다.

# 깃의 초기화(.git생성)

- git init을 사용하면 기존 디렉토리를 git 저장소로 변환할 수 있다.
- git 저장소를 초기화하면 프로젝트의 모든 파일과 디렉토리에 대한 버전 관리를 시작할 수 있다.

```
git init
```

## 초기 설정

- git init을 실행한 후에는 저장소의 초기 설정이 필요하다.
- `username(사용자이름)`, `user.email(사용자이메일)`을 두어 식별 정보를 추가할 수 있다.

```
git config --global user.name "사용자이름"
git config --global user.email "사용자이메일"
```

# 깃의 Commit 과정

- `WorkTree -> Index -> Commit`의 순서로 Commit을 진행한다.
- `git status`를 이용하여 `현재 파일 상태를 확인`할 수 있다.

```
git status   ==> 길게 확인
git status -s    ==> 짧게 확인
```

## WorkTree -> Index

- `add`를 이용해서 변경된 파일을 stage에 올릴 수 있다.
- VSCode의 EXPLORER에서 파일 옆의 알파벳이 `M -> A` 로 변경된다.

```
git add 파일명(확장자포함)  --> 특정 파일 저장
git add '.' or '*'     --> 전체 파일 저장
```

## Index -> Commit

- `Commit`명령어를 이용하여 stage에 올라간 파일을 커밋메시지와 함께 commit을 할 수 있다.
- VSCode의 EXPLORER에서 파일 옆의 알파벳이 `A`가 제거된다.

```
git commit -m "커밋메시지"
```

## WorkTree -> Commit

- 'Commit -am"을 이용하여 stage올리기부터 commit까지 한번에 수행이 가능하다.

```
git commit -am "커밋메시지"
```

## Index -> WorkTree

- `Restore`를 이용하여 stage에서 WorkTree로 이동이 가능하다.

```
git restore 파일명
```

## Commit -> Index

- `Reset`을 이용하여 Index에서 Commit으로 이동이 가능하다.

```
git reset 파일명
```

# git의 이동

## git log

- 특정 브랜치에서 커밋한 기록을 볼 수 있는 명령어

```
git log   ==> 길게 보기
git log --oneline    ==> 한줄로 보기
```

## 브랜치나 커밋 파일 간의 이동

- `checkout`을 이용하여 이동이 가능하다.

```
git checkout 커밋고유번호
git checkout 브랜치명

git chechout -   ==> 가장 최근 커밋한 파일로 돌아가기
```

# Reset , Revert, Branch

- git에서 reset, revert, branch는 `커밋의 이전 버전으로 돌아가도록 만들어주는 명령어`이다.

## Reset

- reset은 git에서 과거 커밋으로 돌아가도록 만들어주는 명령어이다.
- reset은 이전의 특정 커밋파일로 돌아간다면 `그 이후~ 현재까지 만들 커밋 파일들은 제거`된다.
- reset은 `스테이징 영역과 코드 유지 정도`에 따라 Soft, Mixed, Hard 모드의 세가지가 있다.

### Soft Reset

- 스테이징 영역 : Stage상태
- 코드 유지 : O

```
git reset --soft 커밋고유번호
```

### Mixed Reset (일반적인 Reset)

- 스테이징 영역 : Worktree상태(Stage 전)
- 코드 유지 : O

```
git reset 커밋고유번호
```

### Hard Reset

- 스테이징 영역 : X
- 코드 유지 : X

```
git reset --hard 커밋고유번호
```

## Revert

- revert는 git에서 과거 커밋으로 돌아가도록 만들어주는 명령어이다.
- revert는 특정 커밋파일로 돌아가더라도 `그 이후부터의 파일은 제거되지 않는다.`

```
git revert 커밋고유번호
```

## Branch

- 프로젝트에서 `나만의 독립적인 공간을 생성`하는 명령어이다.
- 동시에 다른 작업을 진행하게 만들어주고, 버그를 수정하기에 용이하게해준다.
- git에서 기본 브랜치는 `Windows - Master`, `Mac - Main`으로 설정되어 있다.

### Branch 조희

- 프로젝트내에 존재하는 모든 branch를 조회.

```
git branch
```

### Branch 생성하기

```
git branch 브랜치명
```

### Branch 전환하기

```
git chechout 브랜치명
git switch 브랜치명
```

### Branch 생성과 전환을 한번에 하기

- `switch`와 `checkout` 을 이용해서 한번에 생성과 전환을 할 수 있다.

```
git switch -c 브랜치명
git checkout -b 브랜치명
```

### branch -vv

- `git branch`를 이용하면 현재 저장소에 있는 branch 목록을 보여준다. `git branch -vv`를 이용하면 더 자세한 branch 정보를 나열한다.
  1. branch 목록
  2. 각 branch가 지정하고 있는 최신 commit 해시
  3. 각 branch의 상위 branch - _계층 구조를 알기에 편리함_
  4. branch와 연동해있는 원격 branch 이름 - _로컬 저장소와 원격 저장소의 연동 유무 확인에 용이_

```
git branch -vv
```

## git merge

- 서로 다른 branch에 있는 `파일을 서로 합병`하게 만드는 명령어
- git은 협업에 유용한 툴로 팀원 각자 branch를 나누어 작업한 내용을 하나로 합치거나 수정이 필요할 때 유용하게 쓰인다.
- 만약, branch1을 main 기준에서 합병하고 싶다면, 다음과 같이 작성하면 된다.

```
git merge branch1
```

## git remove

- 깃을 이용하여 파일을 삭제하는 명령어

```
git rm 파일명
```

### git rm --cached

- 파일을 커밋기록에서 삭제하거나 git의 추적 대상에서 제외시키는 명령어
- git의 추적 대상에서는 삭제되나 작업 디렉토리에서는 삭제되지 않는다.
- git rm --cached가 유용하게 사용되는 경우
  1. 작업 디렉토리에서 파일을 삭제했으나 git이 여전히 해당 파일을 추적하는 경우
  2. 커밋에 포함되지 말아야할 파일을 git이 추적하고 있는 경우(개인 설정 파일 등)
  3. 이미 커밋에 포함된 파일을 git의 추적 대상에서 제외시켜야 하는 경우

```
git rm --cached 파일이름
```

## git commit amend

- 커밋을 취소하지 않고 커밋 메시지를 수정할 수 있다.

```
git commit --amend -m "변경할 커밋메시지"
```

## git clone

- 원격저장소의 내용을 로컬 환경으로 복제하는 기능 제공
- 협업시 다른 컴퓨터에서 원격저장소로 push한 다른 사람의 코드를 자신의 로컬 환경으로 가져올 수 있다.

```git
git clone <원격 저장소 UTL>
```

- git clone 장점

## git push

- 로컬저장소에서 원격 저장소로 업로드 하는 기능
- 다른 개발자와 코드를 공유하고 협업이 가능

```git
git push <원격 저장소 이름> <로컬브랜치이름>:<원격 브랜치 이름>
```

- 로컬 브랜치와 원격 브랜치의 이름을 매핑하여 업로드 할 수 있다.
- 주의할 점
  - 다른 개발자가 원격 저장소에 변경 내용을 업로드 한 경우, `'git push'전에 반드시 'git pull'로 변경 내용을 가져와 동기화`해야 한다.

# 커밋 메시지 작성법

- 첫 줄에 간단하지만 명확하게 내용을 요약한다.
- 한 줄 비우고
- 자세한 내용을 적는다.
