# reset

## 세 트리와 워크플로

**reset을 이해하기 전에 먼저 세 트리(HEAD, index, Working directory)와 이 트리들의 워크플로에 대해 이해하자.** Git을 서로 다른 세 트리를 관리하는 컨텐츠 관리자로 생각하면 `reset` 과 `checkout` 을 좀 더 쉽게 이해할 수 있다. 여기서 “트리” 란 실제로는 “파일의 묶음” 이다. 자료구조의 트리가 아니다 세 트리 중 `Index`는 트리도 아니지만, 이해를 쉽게 하려고 일단 트리라고 한다.

Git은 일반적으로 세 가지 트리를 관리하는 시스템이다.

| 트리 | 역할 |
| --- | --- |
| HEAD | 마지막 커밋 스냅샷, 다음 커밋의 부모 커밋 |
| Index | 다음에 커밋할 스냅샷 |
| 워킹 디렉토리 | 샌드박스 |

<br>

### **HEAD**

**HEAD는 현재 브랜치를 가리키는 포인터**이며, 브랜치는 브랜치에 담긴 커밋 중 가장 마지막 커밋을 가리킨다. 지금의 HEAD가 가리키는 커밋은 바로 다음 커밋의 부모가 된다. **단순하게 생각하면 HEAD는 *현재 브랜치 마지막 커밋의 스냅샷***이다.

<br>

### **Index**

**Index는 바로 다음에 커밋할 것들**이다. 이미 앞에서 우리는 이런 개념을 'Staging Area' 라고 배운 바 있다. Staging Area'' 는 사용자가 `git commit` 명령을 실행했을 때 Git이 처리할 것들이 있는 곳이다.

먼저 Index는 워킹 디렉토리에서 마지막으로 Checkout 한 브랜치의 파일 목록과 파일 내용으로 채워진다. 이후 파일 변경작업을 하고 변경한 내용으로 Index를 업데이트 할 수 있다. 이렇게 업데이트 하고 `git commit` 명령을 실행하면 Index는 새 커밋으로 변환된다.

<br>

### **워킹 디렉토리**

마지막으로 워킹 디렉토리를 살펴보자. 위의 두 트리는 파일과 그 내용을 효율적인 형태로 `.git` 디렉토리에 저장한다. 하지만, 사람이 알아보기 어렵다. 워킹 디렉토리는 실제 파일로 존재한다. 바로 눈에 보이기 때문에 사용자가 편집하기 수월하다. 워킹 디렉토리는 **샌드박스**로 생각하자. 커밋하기 전에는 Index(Staging Area)에 올려놓고 얼마든지 변경할 수 있다.

```bash
$ tree
.
├── README
├── Rakefile
└── lib
    └── simplegit.rb

1 directory, 3 files
```

<br>

## **워크플로**

Git의 주목적은 프로젝트의 스냅샷을 지속적으로 저장하는 것이다. 이 트리 세 개를 사용해 더 나은 상태로 관리한다.

![https://git-scm.com/book/en/v2/images/reset-workflow.png](https://git-scm.com/book/en/v2/images/reset-workflow.png)

- 파일이 하나 있는 디렉토리로 이동한다. 이걸 파일의 v1이라고 하고 파란색으로 표시한다.
- git init 명령을 실행하면 Git 저장소가 생기고 HEAD는 아직 없는 브랜치를 가리킨다(master 는 아직 없다).

![https://git-scm.com/book/en/v2/images/reset-ex1.png](https://git-scm.com/book/en/v2/images/reset-ex1.png)

- 이 시점에서는 워킹 디렉토리 트리에만 데이터가 있다.
- 이제 파일을 커밋해보자. git add 명령으로 워킹 디렉토리의 내용을 Index로 복사한다.
- 그리고 git commit 명령을 실행한다. 그러면 Index의 내용을 스냅샷으로 영구히 저장하고 그 스냅샷을 가리키는 커밋 객체를 만든다. 그리고는 master 가 그 커밋 객체를 가리키도록 한다.

![https://git-scm.com/book/en/v2/images/reset-ex3.png](https://git-scm.com/book/en/v2/images/reset-ex3.png)

- 이때 `git status` 명령을 실행하면 아무런 변경 사항이 없다고 나온다. 세 트리 모두가 같기 때문이다.
- 다시 파일 내용을 바꾸고 커밋해보자. 위에서 했던 것과 과정은 비슷하다. 먼저 워킹 디렉토리의 파일을 고친다. 이를 이 파일의 v2라고 하자. 이건 빨간색으로 표시한다.
- git status 명령을 바로 실행하면 `Changes not staged for commit,''` 아래에 빨간색으로 된 파일을 볼 수 있다. Index와 워킹 디렉토리가 다른 내용을 담고 있기 때문에 그렇다. git add 명령을 실행해서 변경 사항을 Index에 올려주자.
- 이 시점에서 git status 명령을 실행하면 `Changes to be committed''` 아래에 파일 이름이 녹색으로 변한다. Index와 HEAD의 다른 파일들이 여기에 표시된다. 즉 다음 커밋할 것과 지금 마지막 커밋이 다르다는 말이다.
- 마지막으로 git commit 명령을 실행해 커밋한다.
- 이제 git status 명령을 실행하면 아무것도 출력하지 않는다. 세 개의 트리의 내용이 다시 같아졌기 때문이다.

브랜치를 바꾸거나 Clone 명령도 내부에서는 비슷한 절차를 밟는다. 브랜치를 Checkout 하면, HEAD가 새로운 브랜치를 가리키도록 바뀌고, 새로운 커밋의 스냅샷을 Index에 놓는다. 그리고 Index의 내용을 워킹 디렉토리로 복사한다.

<br>

### **Reset 의 역할**

`reset` 명령은 정해진 순서대로 세 개의 트리를 덮어써 나가다가 옵션에 따라 지정한 곳에서 멈춘다.

1. HEAD가 가리키는 브랜치를 옮긴다. ***(`--soft` 옵션이 붙으면 여기까지)***
2. Index를 HEAD가 가리키는 상태로 만든다. ***(`--hard` 옵션이 붙지 않았으면 여기까지)***
3. 워킹 디렉토리를 Index의 상태로 만든다.

위의 트리 세 개를 이해하면 `reset` 명령이 어떻게 동작하는지 쉽게 알 수 있다.

예로 들어 `file.txt` 파일 하나를 수정하고 커밋한다. 이것을 세 번 반복한다. 그러면 히스토리는 아래와 같이 된다.

![https://git-scm.com/book/en/v2/images/reset-start.png](https://git-scm.com/book/en/v2/images/reset-start.png)

자 이제 `reset` 명령이 정확히 어떤 일을 하는지 낱낱이 파헤쳐보자. `reset` 명령은 이 세 트리를 간단하고 예측 가능한 방법으로 조작한다. 트리를 조작하는 동작은 세 단계 이하로 이루어진다.

<br>

### **1 단계: HEAD 이동 (`reset —soft`)**

`**reset` 명령이 하는 첫 번째 일은 HEAD 브랜치를 이동시킨다.** `checkout` 명령처럼 HEAD가 가리키는 브랜치를 바꾸지는 않는다. HEAD는 계속 현재 브랜치를 가리키고 있고, 현재 브랜치가 가리키는 커밋을 바꾼다. HEAD가 `master` 브랜치를 가리키고 있다면(즉 `master` 브랜치를 Checkout 하고 작업하고 있다면) `git reset 9e5e6a4` 명령은 `master` 브랜치가 `9e5e6a4`를 가리키게 한다.

![https://git-scm.com/book/en/v2/images/reset-soft.png](https://git-scm.com/book/en/v2/images/reset-soft.png)

`reset` 명령에 커밋을 넘기고 실행하면 언제나 이런 작업을 수행한다. `reset --soft` 옵션을 사용하면 딱 여기까지 진행하고 동작을 멈춘다.

이제 위의 다이어그램을 보고 어떤 일이 일어난 것인지 생각해보자. reset 명령은 가장 최근의 git commit 명령을 되돌린다. git commit 명령을 실행하면 Git은 새로운 커밋을 생성하고 HEAD가 가리키는 브랜치가 새로운 커밋을 가리키도록 업데이트한다. **reset 명령 뒤에 HEAD~ (HEAD의 부모 커밋)를 주면 Index나 워킹 디렉토리는 그대로 놔두고 브랜치가 가리키는 커밋만 이전으로 되돌린다.** Index를 업데이트한 다음에 git commit 명령를 실행하면 git commit --amend 명령의 결과와 같아진다(마지막 커밋을 수정하기).

<br>

### **2 단계: Index 업데이트 (`reset --mixed`)**

여기서 `git status` 명령을 실행하면 Index와 `reset` 명령으로 이동시킨 HEAD의 다른 점이 녹색으로 출력된다.

`reset` 명령(= **`reset --mixed`**)은 여기서 한 발짝 더 나아가 Index를 현재 HEAD가 가리키는 스냅샷으로 업데이트할 수 있다.

![https://git-scm.com/book/en/v2/images/reset-mixed.png](https://git-scm.com/book/en/v2/images/reset-mixed.png)

`--mixed` 옵션을 주고 실행하면 `reset` 명령은 여기까지 하고 멈춘다. `reset` 명령을 실행할 때 아무 옵션도 주지 않으면 기본적으로 `--mixed` 옵션으로 동작한다.

위의 다이어그램을 보고 어떤 일이 일어날지 한 번 더 생각해보자. **가리키는 대상을 가장 최근의 `커밋` 으로 되돌리는 것은 같다. 그러고 나서 *Staging Area* 를 비우기까지 한다.** `git commit` 명령도 되돌리고 `git add` 명령까지 되돌리는 것이다.

<br>

### **3 단계: 워킹 디렉토리 업데이트 (--hard)**

`**reset` 명령은 세 번째로 워킹 디렉토리까지 업데이트한다.** `--hard` 옵션을 사용하면 `reset` 명령은 이 단계까지 수행한다.

이 --hard 옵션은 매우 매우 중요하다. **reset 명령을 위험하게 만드는 유일한 옵션**이다. Git에는 데이터를 실제로 삭제하는 방법이 별로 없다. 이 삭제하는 방법은 그 중 하나다. reset 명령을 어떻게 사용하더라도 간단히 결과를 되돌릴 수 있다. 하지만 --hard 옵션은 되돌리는 것이 불가능하다. 이 옵션을 사용하면 워킹 디렉토리의 파일까지 강제로 덮어쓴다. 이 예제는 파일의 v3버전을 아직 Git이 커밋으로 보관하고 있기 때문에 reflog 를 이용해서 다시 복원할 수 있다. 만약 커밋한 적 없다면 Git이 덮어쓴 데이터는 복원할 수 없다.

<br>

### Reference
https://git-scm.com/book/ko/v2/Git-도구-Reset-명확히-알고-가기
