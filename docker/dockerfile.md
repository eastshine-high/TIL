# Dockerfile

Docker는 `Dockerfile`에 있는 지시 사항들을 읽어서 이미지들을 빌드할 수 있습니다. `Dockerfile`은 이미지를 어셈블하기 위해 사용자가 커멘드 라인(터미널)에서 호출할 수 있는 모든 명령들이 포함된 텍스트 문서입니다. 사용자는 `docker build` 를 이용해서 자동화된 빌드(여러 커맨드 라인 지시들을 연속적으로 실행)를 만들 수 있습니다.

## `Dockerfile` 사용

[docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령어는 `Dockerfile` 및 컨텍스트에서 이미지를 빌드합니다. 빌드 컨텍스트는 지정된 위치 `PATH` 또는 `URL`에 있는 파일 집합입니다. `PATH`는 로컬 파일 시스템의 디렉토리입니다. `URL`은 Git 리포지토리 위치입니다.

빌드 컨텍스트는 재귀적으로 처리됩니다. 따라서 `PATH`에는 모든 하위 디렉터리가 포함되고 URL에는 저장소와 해당 하위 모듈이 포함됩니다. 아래 예는 현재 디렉토리(`.`)를 빌드 컨텍스트로 사용하는 빌드 명령을 보여줍니다.

```bash
$ docker build .

Sending build context to Docker daemon  6.51 MB
...
```

## `Dockerfile` 지시어 살펴보기

다음은 Java 어플리케이션을 빌드하는 `Dockerfile`의 예시입니다. 이 예시에 사용된 `Dockerfile`의 지시어들을 살펴보겠습니다.

```docker
# 어떤 이미지로부터 만들지 선택합니다. 이 이미지는 우리 프로젝트를 빌드를 하기 위해서만 사용되는 이미지입니다. 빌드 하기 위해서 모든 파일을 컨테이너로 복사한 후 빌드를 실행합니다. 빌드를 하는 용도로만 사용되고 더 이상 사용되지 않습니다.
FROM openjdk:15.0.1 AS builder
# 현재 폴더에서 컨테이너의 현재 폴더로 복사합니다.
COPY . .
# 명령어를 실행합니다.
RUN ["./gradlew", "assemble"]

# 이 이미지가 우리가 실제로 실행시킬 이미지입니다. 마찬가지로 openjdk로 이미지를 사용합니다.
FROM openjdk:15.0.1
# 위의 빌드 이미지로부터 만들어진 jar 파일을 복사합니다.
COPY --from=builder /app/build/libs/app.jar .
# 서버를 실행시키는 명령어입니다.
CMD ["java", "-jar", "app.jar"]
```

### FROM

```docker
FROM [--platform=<platform>] <image> [AS <name>]
Or
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
Or
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```

`FROM` 지시어는 새로운 빌드 단계를 초기화 하고 후속 지시어들을 위한 기본 이미지를 설정합니다. 따라서 유효한 `Dockerfile`은 `FROM` 지시어로 시작해야 합니다. 이미지는 어떤 유효한 이미지도 가능합니다. 특히 [공개 저장소](https://docs.docker.com/docker-hub/repos/)에서 이미지를 가져(pull)와서 시작하는 것이 쉽습니다.

- `FROM`은 하나의 `Dockerfile` 내에서 여러 번 사용되어, 여러 이미지들을 생성하거나 하나의 빌드 단계를 다른 빌드 단계의 종속성(dependency)으로 사용할 수 있습니다. `FROM` 지시어는 이전 지시어들에 의해 생성된 모든 상태를 비웁니다. 따라서 새로운 `FROM` 지시어의 시작 전에, 커밋에 의해 출력된 마지막 이미지 ID를 기록해 둡니다.
- 선택적으로 `FROM` 명령에 `AS name`을 추가하여 새 빌드 단계에 이름을 지정할 수 있습니다. 이름은 이 단계에서 빌드된 이미지를 참조하기 위해 후속 `FROM` 과 `COPY --from=<name>` 지시어에서 사용할 수 있습니다.
- `ARG` 는 `Dockerfile`에서 `FROM` 지시어 앞에 올 수 있는 유일한 지시어입니다([ARG and FROM interact](https://docs.docker.com/engine/reference/builder/#understand-how-arg-and-from-interact)).

옵션 플래그인 `--platform`은 이미지의 플랫폼을 지정하는 데 사용할 수 있습니다. `FROM`의 경우 다중 플랫폼 이미지를 참조합니다. 예: `linux/amd64`, `linux/arm64`, or `windows/amd64`.

### COPY

```docker
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

> `--chown` 기능은 리눅스 컨테이너를 빌드하는 데 사용되는 Dockerfiles에서만 지원됩니다.
> 

`COPY` 지시어는 `<src>`에 있는 새로운 파일들이나 디렉토리들을 복사하여 컨테이너 파일시스템의 `<dest>` 경로에 추가합니다. 여러 `<src>` 리소스를 지정할 수 있지만 파일 및 디렉터리들의 경로들은 빌드 컨텍스트의 소스를 기준(relative)으로 번역됩니다.

각 `<src>`에는 와일드카드가 포함될 수 있으며 Go의 [filepath.Match](https://golang.org/pkg/path/filepath#Match) 규칙들을 사용하여 매치됩니다. 예를 들어 "hom"으로 시작하는 모든 파일을 추가하려면 다음과 같이 사용합니다.

```docker
COPY hom* /mydir/
```

`<dest>`는 절대 경로 또는 `WORKDIR`에 대한 상대 경로이며 소스는 대상 컨테이너 안에 복사됩니다. 아래 예에서는 상대 경로를 사용하고 'test.txt'를 `<WORKDIR>/relativeDir/`에 추가합니다.

```docker
COPY test.txt relativeDir/
```

반면, 이 예에서는 절대 경로를 사용하고 "test.txt"를 `/absoluteDir/`에 추가합니다.

```docker
COPY test.txt /absoluteDir/
```

### RUN

`RUN` 지시어는 다음 2가지 형태로 사용할 수 있습니다.

- `RUN <command>` (shell 형식, 명령은 기본적으로 Linux의 경우 `/bin/sh -c`, Windows의 경우 `cmd /S /C`인 셸에서 실행됩니다)
- `RUN ["executable", "param1", "param2"]` (*exec* 형식)

`**RUN` 명령어는 현재 이미지 위에 있는 새 레이어에 명령들을 실행하고 결과를 커밋합니다.** 커밋된 결과 이미지는 `Dockerfile`의 다음 단계에 사용될 수 있습니다. `RUN` 명령을 계층화하고 커밋들을 생성하는 것은 도커의 핵심 개념을 따르는 것입니다. 커밋이 쉽고 소스 컨트롤과 비슷하게 이미지 히스토리의 모든 지점에서 컨테이너들을 생성할 수 있습니다.

아래는 shell 형식으로 명령을 기술하는 예시입니다. apt 명령을 사용하여 Nginx를 설치합니다.

```docker
RUN apt -get install -y nginx
```

- 위의 명령 기술은 Docker 컨테이너 안에서 `/bin/sh -c` 명령을 사용하여 명령을 실행했을 때와 똑같이 작동합니다.
- Docker 컨테이너에서 실행할 기본 쉘은 `SHELL` 명령을 사용해 변경할 수 있습니다.

exec 형식을 이용해 `/bin/sh` 이외의 다른 셸을 사용할 수도 있습니다.

```docker
RUN ["/bin/bash", "-c", "echo hello"]
```

exec 형식으로 명령을 기술하면 쉘을 경유하지 않고 기본 이미지를 사용하여 직접 명령을 실행합니다. 따라서 명령 인수에 `$HOME`과 같은 환경변수를 지정할 수 없습니다. exec 형식에서는 실행하고 싶은 명령을 JSON 배열로 지정합니다. 아래는 exec 형식으로 Java 이미지에 직접 명령을 실행하는 예시입니다.

```docker
RUN ["./gradlew", "assemble"]
```

### CMD

`CMD` 지시어는 다음 3가지 형태로 사용할 수 있습니다.

- `CMD ["executable","param1","param2"]` (exec 형식, Java 어플리케이션 빌드에 사용했던 방식)
- `CMD ["param1","param2"]` (`ENTRYPOINT`에 대한 기본 매개변수)
- `CMD command param1 param2` (shell 형식)

> JSON 배열 형식은 단어 주위에 작은따옴표`'`가 아닌 큰따옴표`"`를 사용해야 정상적으로 구문 분석이 됩니다.
> 

Dockerfile에는 `CMD` 명령어가 하나만 있을 수 있습니다. 둘 이상의 `CMD`를 나열할 경우, 마지막 `CMD`만 적용됩니다. **`CMD`의 주요 목적은 컨테이너 실행에 기본값들을 제공하는 것**입니다. 이러한 기본값들은 실행에 포함하거나 제외할 수 있습니다. 이 경우에는 반드시 `ENTRYPOINT` 지시어도 지정해야 합니다.

`CMD`를 사용하여 `ENTRYPOINT` 명령어에 대한 기본 인수를 제공하는 경우 `CMD` 및 `ENTRYPOINT` 명령어를 모두 JSON 배열 형식으로 지정해야 합니다.

`ENTRYPOINT`와 `CMD` 지시어를 조합한 예시, 

```docker
FROM ubuntu:16.04

# top 실행
ENTRYPOINT ["top"]
# 갱신 간격을 10초로 지정
CMD ["-d", "10"]
```

`CMD` 지시어는 `docker container run` 명령 시에 덮어 쓸 수 있습니다.

```bash
$ docker container run -it sample -d 2 // (갱신 간격을 2초로 덮어씀)
```

`ENTRYPOINT`와 `CMD` 지시어의 차이는 `docker container run` 명령 실행 시의 동작에 있습니다. CMD 지시어의 경우는 컨테이너 시작 시에 실행하고 싶은 명령을 정의해도 `docker container run` 명령 실행 시에 아규먼트로 새로운 명령을 지정한 경우 이것을 우선 실행합니다.

`ENTRYPOINT` 지시어에서 지정한 명령은 반드시 컨테이너에서 실행되는데, 실행 시에 명령 아규먼트를 지정하고 싶을 때는 `CMD` 지시어와 조합하여 사용합니다.

### ENTRYPOINT

`ENTRYPOINT` 지시어에서 지정한 명령은 Dockerfile에서 빌드한 이미지로부터 Docker 컨테이너를 시작하기 때문에 `docker container run` 명령을 실행했을 때 실행됩니다.

`ENTRYPOINT` 지시어는 다음 2가지 형태로 사용할 수 있습니다.

- `ENTRYPOINT ["executable", "param1", "param2"]`(exec form, 선호되는 방식)
- `ENTRYPOINT command param1 param2`(shell form)

`docker run <image> [COMMAND]`에 대한 커멘드 라인 아규먼트들은 exec 형식 `ENTRYPOINT`의 요소들 뒤에 추가되고 `CMD`를 사용하여 지정한 모든 요소를 오버라이드합니다. 이를 통해 아규먼트들을 entry point로 전달할 수 있습니다. 즉, `docker run <image> -d`는 `-d` 인수를 entry point로 전달합니다. `docker run --entrypoint` 플래그를 사용하여 `ENTRYPOINT` 지시어를 오버라이드할 수도 있습니다.

shell 형식은 `CMD` 또는 `run` 커멘드 라인 아규먼트들이 사용되는 것을 막아주지만, `ENTRYPOINT`가 `/bin/sh -c`(신호들을 전달하지 않음)의 하위 명령으로 시작된다는 단점이 있습니다. 이는 실행 파일이 컨테이너의 `PID 1`이 아니고 Unix의 신호들을 수신하지 못하는 것을 의미합니다. 따라서 실행 파일은 `docker stop <container>`으로부터 `SIGTERM`을 수신하지 못합니다.

### WORKDIR

다음은 React App을 빌드하는 `Dockerfile`의 예시입니다. 

```docker
FROM node:14.15.3
WORKDIR /app
COPY package.json ./
COPY package-lock.json ./
RUN npm install
COPY . .
CMD ["npm", "start"]
```

`WORKDIR` 지시어는 Dockerfile에서 뒤에 오는 모든 `RUN`, `CMD`, `ENTRYPOINT`, `COPY` 및 `ADD` 지시어에 대한 작업 디렉터리를 설정합니다. 각 명령어의 현재 디렉토리는 한 줄마다 초기화되기 때문에 `RUN cd /path`를 하더라도 다음 명령어에선 위치가 초기화 됩니다. 같은 디렉토리에서 계속 작업하기 위해서 `WORKDIR`을 사용합니다.

`WORKDIR` 지시어는 Dockerfile에서 여러 번 사용할 수 있습니다. 상대 경로가 제공되면 이전 `WORKDIR` 지시어의 경로를 기준으로 합니다. 예를 들어 아래의 ****Dockerfile에서 최종 `pwd` 명령의 출력은 `/a/b/c`입니다.

```docker
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

`WORKDIR` 지시어는 `ENV`를 사용하여 설정한 환경 변수를 사용할 수 있습니다. Dockerfile에 명시적으로 설정된 환경 변수만 사용 가능합니다. 

```docker
ENV DIRPATH=/path
ENV DIRNAME=/dirname
WORKDIR $DIRPATH/$DIRNAME
RUN ["pwd"]
```

위의 마지막 줄이 실행되면 `/path/dirname`이 출력됩니다.

### 기타 지시어들

`ENV` :  컨테이너에서 사용할 환경변수를 지정합니다. 컨테이너를 실행할 때 `-e`옵션을 사용하면 기존 값을 오버라이딩 하게 됩니다.

`EXPOSE` :  도커 컨테이너가 실행되었을 때 요청을 기다리고 있는(Listen) 포트를 지정합니다. 여러개의 포트를 지정할 수 있습니다.

`VOLUME` :  컨테이너 외부에 파일시스템을 마운트 할 때 사용합니다. 

`MAINTAINER` :  Dockerfile을 관리하는 사람의 이름 또는 이메일 정보를 적습니다. 빌드에 영향을 주지는 않습니다.

`ADD` :  `COPY`명령어와 매우 유사하나 몇가지 추가 기능이 있습니다. `src`에 파일 대신 URL을 입력할 수 있고 `src`에 압축 파일을 입력하는 경우 자동으로 압축을 해제하면서 복사됩니다.

외에도 `Dockerfile`에는 다양한 지시어들이 있습니다. 더 자세한 내용은 아래 참조를 통해 확인할 수 있습니다.

## 참조

[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)
