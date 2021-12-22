# 파일 삭제하기

Git에서 파일을 제거하는 방식은 3가지가 있다.

1. git 명령어가 아닌 워킹 디렉토리에서 파일 삭제
2. git rm
3. git rm —cached

<br>

### git 명령어가 아닌 워킹 디렉토리에서 파일 삭제

Git 명령을 사용하지 않고 단순히 워킹 디렉터리에서 파일을 삭제하고 git status 명령으로 상태를 확인하면 Git은 현재 ``Changes not staged for commit'' (즉, Unstaged 상태)라고 표시해준다.

```bash
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

그리고 git rm 명령을 실행하면 삭제한 파일은 Staged 상태가 된다.

<br>

### git rm

git rm 명령으로 Tracked 상태의 파일을 삭제한 후에(정확하게는 Staging Area에서 삭제하는 것) 커밋해야 한다. **이 명령은 워킹 디렉토리에 있는 파일도 삭제하기 때문에 실제로 파일도 지워진다.**

```bash
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

커밋하면 파일은 삭제되고 Git은 이 파일을 더는 추적하지 않는다. 이미 파일을 수정했거나 Staging Area에(Git Index라고도 부른다) 추가했다면 -f 옵션을 주어 강제로 삭제해야 한다. 이 점은 실수로 데이터를 삭제하지 못하도록 하는 안전장치다. 커밋 하지 않고 수정한 데이터는 Git으로 복구할 수 없기 때문이다.

<br>

### git rm —cached

**Staging Area에서만 제거하고 워킹 디렉토리에 있는 파일은 지우지 않고 남겨둘 수 있다.** 다시 말해서 하드디스크에 있는 파일은 그대로 두고 Git만 추적하지 않게 한다. **이 명령어는** .gitignore 파일에 추가하는 것을 빼먹었거나 대용량 로그 파일이나 컴파일된 파일인 .a 파일 같은 것을 **실수로 추가했을 때 쓴다.** --cached 옵션을 사용하여 명령을 실행한다.

```bash
$ git rm --cached README
```
<br>

### Reference
https://git-scm.com/book/ko/v2/Git의-기초-수정하고-저장소에-저장하기
