# docker run

Docker는 격리된 컨테이너들에서 프로세스들을 실행(run)합니다. 컨테이너는 호스트에서 실행되는 프로세스입니다. 호스트는 로컬 또는 원격(remote)일 수 있습니다. 운영자가 `docker run`을 명령(execute)할 때, 실행(run)되는 컨테이너 프로세스는 자체 파일 시스템, 자체 네트워킹, 호스트와 격리된 자체 프로세스 트리가 있다는 점에서 격리되어(isolated) 있습니다. `docker run` 명령어는 런타임에 컨테이너의 리소스들을 정의합니다.

## 명령어 형식(General form)

`docker run`  명령어는 다음 형태입니다.

```bash
$ docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

`docker run` 명령어는 컨테이너를 파생시킬 [IMAGE](https://docs.docker.com/glossary/#image)를 지정해야 합니다. 이미지 개발자는 다음과 관련된 이미지 기본값들을 정의할 수 있습니다.

- 백그라운드 또는 포크라운드 실행(detached or foreground running)
- 컨테이너 식별(container identification)
- 네트워크 설정(network settings)
- CPU 및 메모리에 대한 런타임 제약 조건**(**runtime constraints on CPU and memory)

운영자는 `docker run[OPTIONS]`을 사용하여 개발자가 설정한 이미지 기본값을 추가하거나 오버라이드할 수 있습니다. 또한 운영자는 Docker 런타임 자체에서 설정한 거의 모든 기본값을 오버라이드할 수 있습니다. 이미지 또는 Docker 런타임의 기본값을 오버라이드할 수 있는 운영자의 기능으로 인해 `run` 명령어는 다른 docker 명령보다 더 많은 옵션을 가집니다.

## 운영자 권한 옵션들(Operator exclusive options)

운영자(`docker run`을 실행하는 사람)는 다음 옵션들을 설정할 수 있습니다.

- [Detached vs foreground](https://docs.docker.com/engine/reference/run/#detached-vs-foreground)
    - [Detached (-d)](https://docs.docker.com/engine/reference/run/#detached--d)
    - [Foreground](https://docs.docker.com/engine/reference/run/#foreground)
- [Container identification](https://docs.docker.com/engine/reference/run/#container-identification)
    - [Name (--name)](https://docs.docker.com/engine/reference/run/#name---name)
    - [PID equivalent](https://docs.docker.com/engine/reference/run/#pid-equivalent)
- [IPC settings (--ipc)](https://docs.docker.com/engine/reference/run/#ipc-settings---ipc)
- [Network settings](https://docs.docker.com/engine/reference/run/#network-settings)
- [Restart policies (--restart)](https://docs.docker.com/engine/reference/run/#restart-policies---restart)
- [Clean up (--rm)](https://docs.docker.com/engine/reference/run/#clean-up---rm)
- [Runtime constraints on resources](https://docs.docker.com/engine/reference/run/#runtime-constraints-on-resources)
- [Runtime privilege and Linux capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)

## `docker run` 예시

다음은 [Dockerfile](https://github.com/eastshine-high/til/blob/main/docker/dockerfile.md#dockerfile-%EC%A7%80%EC%8B%9C%EC%96%B4-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0)을 작성하고 `docker build` 명령을 통해 생성한 docker 이미지(스프링 부트 어플리케이션)를 `docker run`의 여러 옵션들을 사용하여 실행하는 예시입니다.

```bash
$ docker run --rm -d --name api-server -v /logs/api.log:/app/logs/api.log app -p 80:8080 -e SPRING_PROFILES_ACTIVE=dev spring-boot-container
```

이 페이지에서는 위의 예시에 사용된 `docker run`의 옵션과 관련된 내용을 살펴보고자 합니다. 이를 통해 `docker run` 명령의 기본적인 이해 및 사용 방법을 학습합니다. `docker run`에 대한 더 많은 정보는 [공식 문서](https://docs.docker.com/engine/reference/run/)를 참조하시면 좋을 것 같습니다.

## Detached vs foreground

Docker 컨테이너를 시작할 때 먼저 컨테이너를 **detached** 모드에서 백그라운드로 실행할지 **foreground**(default) 모드로 실행할지 결정해야 합니다.

### Foreground

포그라운드 모드(`-d`가 지정되지 않은 경우, 기본값)에서 `docker run`은 컨테이너 안에서 프로세스를 시작하고 콘솔을 프로세스의 표준 입력과 출력, 표준 오류에 연결할 수 있습니다.

```
-a=[]           : Attach to `STDIN`, `STDOUT` and/or `STDERR`
-t              : Allocate a pseudo-tty
--sig-proxy=true: Proxy all received signals to the process (non-TTY mode only)
-i              : Keep STDIN open even if not attached
```

`-a`를 지정하지 않으면 Docker는 [`stdout` 및 `stderr` 모두에 연결](https://github.com/docker/docker/blob/4118e0c9eebda2412a09ae66e90c34b85fae3275/runconfig/opts/parse.go#L267)합니다. 다음과 같이 세 가지 표준 스트림(`STDIN`, `STDOUT`, `STDERR`) 중 어느 것에 연결할 것인지 지정할 수 있습니다.

```
$ docker run -a stdin -a stdout -i -t ubuntu /bin/bash
```

쉘과 같은 대화식 프로세스를 사용하려면, 해당 컨테이너 프로세스에 tty(단말 디바이스)를 할당하기 위해 `-i -t`를 함께 사용해야 합니다. `-i -t`는 `-it`로 함께 작성할 수 있습니다. 아래와 같이 클라이언트가 파이프에서 표준 입력을 수신할 때 `-t`를 지정하는 것은 금지되어 있습니다.

```
$ echo test | docker run -i busybox cat
```

### Detached [`-d`]

백그라운드(분리 모드)에서 컨테이너를 시작하려면 `-d=true` 또는 `-d` 옵션을 사용합니다.

```bash
$ docker run -d -p 80:80 my_image service nginx start
```

## 정리(Clean up, `—rm`)

기본적으로 컨테이너를 나가더라도(exit or stop) 컨테이너의 파일 시스템은 영속화 됩니다. 이것은 디버깅을 쉽게하고 기본적으로 모든 데이터를 간직할 수 있게 합니다. `docker ps -a` 명령어를 통해 남아있는 컨테이너들을 확인할 수 있습니다. **컨테이너를 나갈 때 자동적으로 컨테이너 파일 시스템을 삭제하고 싶다면 `—rm` 옵션을 추가한다.** 영속화된 컨테이너를 삭제하고 싶을 경우에는 `docker rm <container name>`을 사용합니다.

## 재시작 정책(Restart policies, `--restart`)

Docker를 실행(run)할 때, `--restart` 플래그를 사용하면 컨테이너가 종료(exit)되는 시점에 컨테이너를 다시 시작해야 하는지를 결정하는 ‘재시작 정책(Restart policies)’을 지정할 수 있습니다. 컨테이너에서 재시작 정책이 활성화되있을 경우, `docker ps`에서 `Up` 또는 `Restarting`으로 표시됩니다. `--restart` 플래그와 `--rm`(정리) 플래그를 같이 사용하면 오류가 발생합니다.

Docker가 지원하는 재시작 정책은 다음과 같습니다.

| Policy | Result |
| --- | --- |
| no | 자동으로 컨테이너를 재시작하지 않습니다. |
| on-failure[:max-retries] | 종료 status가 0이 아닐 때 재시작합니다. 선택적으로 재시작 시도 횟수를 지정할 수 있습니다. |
| always | 항상 재시작합니다. |
| unless-stopped | 최근 컨테이너가 정지 상태가 아니라면 항상 재시작합니다. |

## 컨테이너 식별(Container identification)

운영자는 세 가지 방법으로 컨테이너를 식별할 수 있습니다.

| Identifier type | Example value |
| --- | --- |
| UUID long identifier | “f78375b1c487e03c9438c729345e54db9d20cfa2ac1fc3494b6eb60872e74778” |
| UUID short identifier | “f78375b1c487” |
| Name | “evil_ptolemy” |

### Name (`--name`)

UUID 식별자는 Docker 데몬에서 가져옵니다. `--name` 옵션을 사용하여 컨테이너 이름을 지정하지 않으면 데몬이 임의의 문자열 이름을 생성합니다. 이름을 정의하면 컨테이너에 의미를 추가하는 편리한 방법이 될 수 있습니다. 이름을 지정하면 Docker 네트워크 내에서 컨테이너를 참조할 때 사용할 수 있습니다. 이것은 백그라운드 및 포그라운드 Docker 컨테이너 모두에서 작동합니다.

## Dockerfile 이미지의 기본값 재정의(Overriding Dockerfile image defaults)

개발자가 [Dockerfile](https://docs.docker.com/engine/reference/builder/)에서 이미지를 빌드하거나 커밋할 때, 개발자는 이미지가 컨테이너로 시작될 때 적용되는 여러 기본 매개변수를 설정할 수 있습니다. Dockerfile 명령 중 `FROM`, `MAINTAINER`, `RUN`, `ADD` 4개는 런타임에 오버라이드할 수 없습니다. 이 외의 모든 명령어는 `docker run`에서 오버라이드할 수 있습니다. 아래는 개발자가 각 Dockerfile 명령에서 설정했을 수 있는 것과 운영자가 해당 설정을 재정의할 수 있는 방법에 대해 살펴보겠습니다.

### EXPOSE (incoming ports)

`EXPOSE` 지시어를 제외하고 이미지 개발자는 네트워킹에 대한 제어 권한이 없습니다. `EXPOSE` 지시어는 서비스를 제공하는 초기 수신 포트들을 정의합니다. 이 포트들은 컨테이너 안의 프로세스들에서 사용할 수 있습니다. 운영자는 노출된 포트를 추가하기 위해 `--expose` 옵션을 사용할 수 있습니다.

컨테이너의 내부 포트를 노출하기 위해 운영자는 `-P` 또는 `-p` 플래그로 컨테이너를 시작할 수 있습니다. 노출된 포트는 호스트에서 접근(access)할 수 있으며 포트는 호스트에 연결(reach)할 수 있는 모든 클라이언트에서 사용할 수 있습니다.

서비스가 수신하는 컨테이너 내부의 포트 번호는 클라이언트가 연결되는 컨테이너 외부에 노출된 포트 번호와 일치할 필요는 없습니다. 예를 들어 컨테이너 내부에서 HTTP 서비스는 포트 80에서 수신 대기 중이므로 이미지 개발자는 Dockerfile에서 `EXPOSE 80`을 지정합니다. 이 포트는 런타임에 `-p 42800:80` 옵션으로 호스트의 42800 포트에 바인딩될 수 있습니다. 호스트 포트와 노출된 포트 간의 매핑을 찾으려면 `docker port`를 사용합니다.

### ENV (environment variables)

Docker는 Linux 컨테이너를 생성할 때 일부 환경 변수를 자동으로 설정합니다. Windows 컨테이너를 생성할 때는 환경 변수를 설정하지 않습니다.

다음은 Linux 컨테이너에서 설정되는 환경 변수들입니다.

| Variable | Value |
| --- | --- |
| HOME | Set based on the value of USER |
| HOSTNAME | The hostname associated with the container |
| PATH | Includes popular directories, such as /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin |
| TERM | xterm if the container is allocated a pseudo-TTY |

운영자는 하나 이상의 `-e` 플래그를 사용하여 컨테이너의 모든 환경 변수를 설정할 수 있으며, 위에서 언급하거나 Dockerfile `ENV`로 개발자가 이미 정의한 플래그를 오버라이드할 수도 있습니다. 연산자가 값을 지정하지 않고 환경 변수의 이름을 지정하면 명명된 변수의 현재 값이 컨테이너의 환경으로 전파됩니다.

```bash
$ export today=Wednesday
$ docker run -e "deep=purple" -e today --rm alpine env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=d2219b854598
deep=purple
today=Wednesday
HOME=/root
```

### VOLUME (shared filesystems)

```
-v, --volume=[host-src:]container-dest[:<options>]: Bind mount a volume.
```

volume 명령은 [볼륨 사용에 대한 자체 문서](https://docs.docker.com/storage/volumes/)가 있을 정도로 복잡합니다. 개발자는 이미지와 관련된 하나 또는 그 이상의 `VOLUME`을 정의할 수 있지만, 오직 운영자만 한 컨테이너에서 다른 컨테이너로(또는 컨테이너에서 호스트에 마운트된 볼륨으로) 접근하는 권한을 부여할 수 있습니다.

`container-dest`는 항상 `/src/docs`와 같은 절대 경로여야 합니다. `host-src`는 절대 경로 또는 `name` 값일 수 있습니다. `host-src`에 대한 절대 경로를 제공하면 Docker가 지정한 경로에 바인드 마운트(bind-mount)합니다. 만약`name`을 제공하면 Docker는 해당 이름으로 명명된 볼륨을 생성합니다.

`name` 값은 영숫자(alphanumeric)로 시작해야 하며 `a-z0-9`, `_`(밑줄), `.` (마침표) 또는 `-`(하이픈)이 뒤따를 수 있습니다. 절대 경로는 `/`(슬래시)로 시작해야 합니다.

예를 들어, `host-src` 값에 대해 `/foo` 또는 `foo`를 지정할 수 있습니다. `/foo` 값을 제공하면 Docker는 바인드 마운트를 생성합니다. `foo` 값을 전달하면 Docker는 이름이 명명된 볼륨을 생성합니다.

옵션 사용 예시(zsh)

```bash
$ docer run -v $(pwd)/app/src/resources/application-prod.yml:/app/resources/application-prod.yml spring-boot-image
```

---

**참조**

[https://docs.docker.com/engine/reference/run/](https://docs.docker.com/engine/reference/run/)
