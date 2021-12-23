## **git restore**

Git 버전 2.23에는 `git restore`라는 새로운 명령어가 `git reset`의 대안으로 도입되었다. Git 버전 2.23부터 Git은 많은 실행 취소 작업에 `git reset` 대신 `git restore`를 사용한다.

`git reset`으로 수행할 수 있는 아래의 역할은 `git restore`로 대체할 수 있다.

- **Unstaging a Staged File with git restore**
- **Unmodifying a Modified File with git restore**

<br>

### **Unstaging a Staged File with git restore**

예를 들어 두 개의 파일을 변경하고 두 개의 개별 변경 사항으로 커밋하려고 하지만 실수로 `git add *`를 입력하고 둘 다 준비한다고 가정해 보자. 어떻게 둘 중 하나를 해제할 수 있을까? `git status` 명령어는 다음을 알려준다.

```bash
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   CONTRIBUTING.md
	renamed:    README.md -> README
```

"Changes to be committed" 텍스트 바로 아래에 use라고 표시되어 있다. `"git restore --staged <file>…" to unstage`. 따라서 이 조언을 사용하여 `CONTRIBUTING.md` 파일을 unstage해 보자.

```bash
$ git restore --staged CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   CONTRIBUTING.md
```

`CONTRIBUTING.md` 파일은 수정(modified)되었지만 다시 한 번 unstage 상태임을 확인할 수 있다.

<br>

### **Unmodifying a Modified File with git restore**

`CONTRIBUTING.md` 파일에 대한 변경 사항을 유지하고 싶지 않다면 어떻게 할 것인가? 어떻게 하면 쉽게 수정 취소할 수 있을까?  어떻게 마지막으로 커밋했을 때(또는 처음에 복제했거나 작업 디렉토리에 넣었을 때)의 모습으로 되돌릴 수 있을까? `git status`는 그렇게 하는 방법도 알려준다. 아래 예제 출력에서 준비되지 않은 영역은 다음과 같다.

```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   CONTRIBUTING.md
```

위 메세지는 변경 사항을 취소하는 방법을 매우 명확하게 알려줍니다. 메세지가 말하는 대로 해보자.

```bash
$ git restore CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	renamed:    README.md -> README
```

`git restore <file>`은 위험한 명령이다. 해당 파일에 대한 로컬 변경 사항은 모두 사라졌다. Git은 해당 file을 마지막으로 커밋되거나 staged된 버전으로 교체한다.

<br>

### Reference

[https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things](https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things)
