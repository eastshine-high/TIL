# Variables

### 셸 변수

특정한 셸(Shell)에서만 적용되는 변수를 말한다. 리눅스에서는 명령행에서 `변수명=값` 형태로 지정하여 사용할 수 있고, 변수 값을 출력할 때는 변수명 앞에 `$`을 붙이고 `echo` 명령으로 확인할 수 있다.

```bash
 ~ % NGINX=/usr/local/etc/nginx/
 ~ % echo $NGINX
/usr/local/etc/nginx/
```

<br>

### 환경 변수

환경 변수란 프롬프트 변경, PATH 변경 등과 같이 셸의 환경을 정의하는 중요한 역할을 수행하는 변수를 말한다. 환경 변수는 미리 예약된 변수명을 사용하고, bash에서는 PATH, SHELL 등과 같이 대문자로 된 변수로 구성되어 있다. 현재 설정된 전체 환경 변수의 값은 `env` 명령으로 확인 가능하고, 특정 **환경 변수의 값 확인과 설정은 일반 셸 변수 설정과 같다.**

<br>

### Reserved Bourne shell variables

| Variable name | Definition |
| --- | --- |
| CDPATH | A colon-separated list of directories used as a search path for the cd built-in command. |
| HOME | The current user's home directory; the default for the cd built-in. The value of this variable is also used by tilde expansion. |
| IFS | A list of characters that separate fields; used when the shell splits words as part of expansion. |
| MAIL | If this parameter is set to a file name and the MAILPATH variable is not set, Bash informs the user of the arrival of mail in the specified file. |
| MAILPATH | A colon-separated list of file names which the shell periodically checks for new mail. |
| OPTARG | The value of the last option argument processed by the getopts built-in. |
| OPTIND | The index of the last option argument processed by the getopts built-in. |
| PATH | A colon-separated list of directories in which the shell looks for commands. |
| PS1 | The primary prompt string. The default value is "'\s-\v\$ '". |
| PS2 | The secondary prompt string. The default value is "'> '". |

Reference : [https://linux.die.net/Bash-Beginners-Guide/sect_03_02.html](https://linux.die.net/Bash-Beginners-Guide/sect_03_02.html)

<br>

### 영구적인 환경변수 편집

일반 셸 변수 설정은 일시적인 변수이다. 터미널이 재부팅되면 사라진다. 영구적인 환경 변수로 설정하고 싶다면 **bash** 셸의 경우, `~/.bash_profile` 이나 `~/.bashrc`를 수정한다. **zsh**일 경우, `~/.zshrc` 파일을 수정한다.

> 주의. 파일 경로에 `.`이 있음을 주의한다. 예를 들어 `.zshrc`은 개인 사용자 설정 파일을 의미하며, `zshrc`은 시스템 전체 사용자 설정 파일을 의미한다.
> 

아래는 `~/.zshrc` 파일을 수정한 예이다.

```
export NGINX="/usr/local/etc/nginx/"
export PATH="$PATH:~/Downloads/flutter/bin"
```

> Cf. 설정 파일을 새로고침하고 싶을 때 : `source ~/.zshrc`
> 

이제 터미널을 종료하고 다시 켠 후에도 저장된 환경 변수를 사용할 수 있음을 확인할 수 있다.

```bash
~ % cd $NGINX
nginx % pwd
/usr/local/etc/nginx
```

<br>

### Reference

<리눅스 마스터 1급 정복하기, 정성재, 배유미, 북스홀릭>

[https://velog.io/@taelee/환경-변수-설정-Mac](https://velog.io/@taelee/%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98-%EC%84%A4%EC%A0%95-Mac)
