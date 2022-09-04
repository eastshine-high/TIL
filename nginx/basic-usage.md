# Nginx 사용 기초

- 구성 파일의 구조(Configuration File’s Structure)
- 정적 컨텐츠 제공(Serving Static Content)
- 프록시 서버 설정하기(Setting Up a Simple Proxy Server)

이 글은 Nginx에 대한 기본적 설명과 수행할 수 있는 간단한 작업을 설명합니다. [Nginx의 설치](https://nginx.org/en/docs/install.html) 및 [실행, 종료, 재시작](https://www.nginx.com/resources/wiki/start/topics/tutorials/commandline/) 방법에 대한 이해를 전제로 작성하였습니다.

Nginx는 master 프로세스 하나와 worker 프로세스 여러 개를 가집니다. master 프로세스의 주 역할은 설정(configuration)을 읽고 적용하는 것과 worker 프로세스를 관리하는 것입니다. worker 프로세스는 요청에 대한 실질적인 처리를 합니다. nginx는 요청(request)들에 대한 효율적인 처리를 위해 이벤트 기반 모델과 운영체제 의존 메커니즘을 사용합니다. worker 프로세스의 수는 설정 파일에서 정의되고, 보통 기본 설정 또는 자동 설정에 의해 이용 가능한 CPU 코어 수에 고정됩니다.

Nginx와 Nginx 모듈들은 설정 파일에 정의된 대로 작동합니다. 디폴트 설정 파일명은 `nginx.conf` 이고 `/usr/local/nginx/conf`, `/etc/nginx`, 또는 `/usr/local/etc/nginx`에 위치합니다. 리눅스에서 다음 명령으로 설정 파일을 검색할 수 있습니다.

```bash
$ sudo find / -name nginx.conf
/etc/nginx/nginx.conf
```

### 구성 파일의 구조(Configuration File’s Structure)

Nginx는 모듈들로 구성됩니다. 이 모듈들은 설정 파일에 작성된 지시어들(directives)에 의해 작동합니다. 지시어들은 단순(simple) 지시어와 블록 지시어로 나뉩니다. 단순 지시어는 이름과 파라미터들로 구성되며 공백으로 구분됩니다. 그리고 세미콜론 `;`으로  끝납니다. 블록 지시어는 단순 지시어와 같은 구조를 가지지만 세미콜론 대신 괄호`({` and `})`로 묶인 추가 명령 세트로 끝납니다. 블록 지시어가 괄호 안에 다른 지시어를 가질 수 있는 경우, 이를 문맥(context)이라고 부릅니다(예, [events](https://nginx.org/en/docs/ngx_core_module.html#events), [http](https://nginx.org/en/docs/http/ngx_http_core_module.html#http), [server](https://nginx.org/en/docs/http/ngx_http_core_module.html#server), and [location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location)).

설정 파일에서 문맥(context) 외부에 위치한 지시문은 main 문맥에 있는 것으로 간주합니다. 따라서`events` 와 `http` 지시어는 `main` 문맥에 있고, `server`는 `http` 문맥에, `location`은 `server`문맥에 있습니다.

라인에서 `#` 기호 뒤의 부분은 주석으로 간주됩니다.

아래는 기본으로 구성된 `nginx.conf` 의 일부를 발췌한 예시입니다.

```
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

		server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
    }

    include servers/*;
}
```

위 예시에서 사용된 모듈들에 대해 간단히 설명하겠습니다.

- [ngx_http_core_module](https://nginx.org/en/docs/http/ngx_http_core_module.html)
    - Nginx의 웹 서비스 기능을 활용한다면 필수적으로 활성화해야하는 모듈입니다.
    - HTTP 서버의 모든 기반 블록, 지시어, 변수를 포함하는 구성 요소입니다. 이 모듈을 컴파일 과정에서 빼면 HTTP 기능이 전혀 동작하지 않을 뿐 아니라 다른 HTTP 모듈들도 컴파일되지 않게 됩니다.
    - 위 예시에서는 이 모듈의 `http`, `listen`, `location`, `root` 지시어 등이 사용되었습니다. 지시어들에 대한 설명은 아래에서 설명하겠습니다.
- [ngx_core_module](https://nginx.org/en/docs/ngx_core_module.html)
    - 기반 모듈은 엔진엑스의 기본적인 기능을 가진 매개변수를 정의할 수 있는 지시어를 제공합니다. 기반 모듈은 다음과 같은 세 가지로 구분할 수 있습니다.
        - 핵심 모듈 : 프로세스 관리나 보안 같은 필수 기능 및 지시어로 이뤄집니다.
            - `env`, `error_log`, `master_process`, `worker_processes`,`ssl_engine`, `thread_pool` 와 같은 지시어들이 있습니다.
        - 이벤트 모듈 : 네트워킹 기능의 내부 동작 방식을 구성합니다.
            - 반드시 구성파일의 최상위 수준에 있는 `events` 블록 안에 있어야 합니다.
            - `worker_connections` 는 worker 프로세스가 동시에 처리할 수 있는 연결의 수를 지정합니다.
        - 구성 모듈 : 구성을 외부 파일에서 가져와 포함시킵니다.
            - `include` 지시어로 다른 파일을 포함할 수 있게 해주는 간단한 모듈입니다.
- Nginx가 지원하는 여러 모듈들의 목록은 [공식 문서](https://nginx.org/en/docs/) - Modules reference에서 확인할 수 있습니다.

### 정적 컨텐츠 제공(Serving Static Content)

웹 서버의 중요한 임무(task)는 파일(예: 이미지 또는 정적 HTML 페이지들)을 제공하는 것입니다. 예를 들어, 요청에 따라 다른 로컬 디렉토리에서 파일이 제공되도록 구현한다고 가정해 봅시다. `/data/www` 디렉토리는 HTML 파일을 제공하고 `/data/images` 디렉토리는 이미지 파일을 제공합니다. 이렇게 하려면 구성 파일을 편집하고 [`server`](https://nginx.org/en/docs/http/ngx_http_core_module.html#server) 블록 안에 있는 [`http`](https://nginx.org/en/docs/http/ngx_http_core_module.html#http) 블록 내부에 두 개의 `[location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location)` 블록을 설정해야 합니다.

우선 `/data/www` 디렉토리를 만들고 그 안에 텍스트가 작성되어 있는 `index.html` 파일을 넣습니다. 그리고 `/data/images` 디렉토리를 만들고 그 안에 이미지들을 넣습니다.
다음으로 구성 파일을 엽니다. 기본 구성 파일은 이미 몇 가지 `server` 블록의 예시들(대부분 주석 처리)을 담고 있을 것입니다. 지금부터 이러한 블록을 모두 주석 처리하고 새 `server` 블록을 시작합니다.

```
http {
    server {
    }
}
```

- 일반적으로 구성 파일에는 수신하는 포트와 서버 이름으로 구분되는 여러 `server` 블록이 포함될 수 있습니다.

nginx가 요청을 처리할 `server`를 정하면, 요청 헤더에 지정된 URI와 `server` 블록 내부에 정의된 `location` 지시어의 파라미터들을 테스트합니다. `server` 블록 안에 `location` 블록을 추가합니다.

```
http {
    server {
				location / {
				    root /data/www;
				}
    }
}

```

- `location` 블록에 지정된 `/` 접두어는 요청 URI와 비교됩니다. 일치하는 요청의 경우 URI가 [root](https://nginx.org/en/docs/http/ngx_http_core_module.html#root) 지시어에 지정된 경로, 즉 `/data/www` 에 추가되어 로컬 파일 시스템에서 요청된 파일의 경로를 형성합니다.
    - 예를 들어 `http://localhost/some/example.html` 요청에 대한 응답으로 nginx는 `/data/www/some/example.html` 파일을 보냅니다.
- 일치하는 `location` 블록이 여러 개일 경우, nginx는 접두어가 가장 긴 블록을 선택합니다. 위의 `location` 블록은 길이가 1인 가장 짧은 접두어를 제공하므로 다른 모든 `location` 블록이 일치하지 못하는 경우에만 이 블록이 사용됩니다.

다음, 두 번째 `location` 블록을 추가합니다.

```
http {
    server {
				location / {
				    root /data/www;
				}

				location /images/ {
				    root /data;
				}
    }
}
```

- 새로 추가한 `location`은 `/images/`로 시작하는 요청과 매치될 것입니다(`location /`도 이 요청과 일치하지만 접두어가 더 짧습니다).
- `/images/`로 시작하는 URI가 있는 요청에 대한 응답으로 서버는 `/data/images` 디렉토리에서 파일을 보냅니다.
    - 예를 들어 `http://localhost/images/example.png` 요청에 대한 응답으로 nginx는 `/data/images/example.png` 파일을 보냅니다.
    - 파일이 존재하지 않으면 nginx는 404 오류를 나타내는 응답을 보냅니다.
- `/images/`로 시작하지 않는 URI가 있는 요청은 `/data/www` 디렉토리에 매핑됩니다.
- 이  구성으로 서버는 표준 포트인 80번을 수신하며 로컬 머신의 `http://localhost/` 에서 접근 가능합니다.

이제 새 구성을 적용합니다. nginx가 아직 시작되지 않았다면, 시작합니다. nginx가 작동중이라면 아래와 같이 nginx의 마스터 프로세스에 `reload` 신호를 보냅니다.

```bash
$ sudo nginx -s reload
```

Cf. [nginx reload와 restart의 차이점](https://webisfree.com/2020-11-12/nginx-%EC%84%9C%EB%B2%84-%EC%9E%AC%EC%8B%9C%EC%9E%91-%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-restart-reload)

> 무언가가 예상대로 작동하지 않는 경우 /usr/local/nginx/logs 또는 /var/log/nginx 디렉토리의 access.log 및 error.log 파일에서 원인을 찾으려고 시도할 수 있습니다.
In case something does not work as expected, you may try to find out the reason n `access.log`
 and `error.log` files in the directory `/usr/local/nginx/logs` or `/var/log/nginx`.
> 

### 프록시 서버 설정하기(Setting Up a Simple Proxy Server)

nginx에서 자주 사용하는 것 중 하나는 nginx를 프록시 서버로 설정하는 것입니다. 즉, 클라이언트로부터 요청을 수신하고 프록시 서버에 전달하고 응답을 받아 클라이언트에 보내는 서버를 의미합니다.

이번에는 이미지에 대한 요청들은 로컬 디렉토리의 파일로 처리하고 다른 모든 요청들은 프록시 서버로 보내는 기본 프록시 서버를 구성할 것입니다. 이 예에서 두 서버는 단일 nginx 인스턴스에 정의됩니다.

먼저 다음 내용이 포함된 nginx의 구성 파일에 `server` 블록을 하나 더 추가하여 프록시 서버를 정의합니다.

```
server {
    listen 8080;
    root /data/up1;

    location / {
    }
}
```

- 8080번 포트를 수신하는 간단한 서버입니다.
    - 앞선 예시에는 표준 포트인 80이 사용되었기 때문에 `listen` 지시문이 지정되지 않았습니다.
- 모든 요청을 로컬 파일 시스템의 `/data/up1` 디렉토리에 매핑합니다.
- 여기서는 `root` 지시어가 `server` 컨텍스트 안에 있습니다. 이러한 `root` 지시문은 요청을 처리하기 위해 선택된 `location` 블록에 자체 `root` 지시문이 없을 경우에 사용됩니다.

`/data/up1` 디렉토리를 만들고 `index.html` 파일을 이 디렉토리에 넣습니다.

그런 다음 이전 섹션의 서버 구성을 수정해서 프록시 서버 구성으로 사용합니다. 첫 번째 `location` 블록에 [proxy_pass](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass) 지시어와 함께 파라미터에는 프록시 서버의 프로토콜, 이름 및 포트 번호(이 경우 `http://localhost:8080`)를 입력합니다.

```
server {
    location / {
        proxy_pass http://localhost:8080;
    }

    location /images/ {
        root /data;
    }
}
```

다음은 두 번째 `location` 블록을 수정하여 일반적인 이미지 파일 확장자를 가진 요청과 일치하도록 합니다.

```
location ~ \.(gif|jpg|png)$ {
    root /data/images;
}
```

- 파라미터는 `.gif`, `.jpg` 또는 `.png`로 끝나는 모든 URI와 일치하는 정규 표현식입니다.
- 정규 표현식 앞에는 `~` 가 있어야 합니다.
- 정규 표현식에 해당하는 요청은 `/data/images` 디렉토리에 매핑됩니다.
- nginx가 요청을 처리하기 위해 `location` 블록을 선택할 때, 먼저 접두어를 지정하는 [location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location) 지시문들을 확인하고 가장 긴 접두어가 있는 `location` 를 기억한 다음 정규 표현식을 확인합니다. 정규 표현식과 일치하는 항목이 있으면 nginx가 이 `location` 을 선택하고, 그렇지 않으면 이전에 기억한 `location`를 선택합니다.

최종적으로 다음과 같은 구성 파일을 작성합니다.

```bash
http {
		server {
		    listen 8080;
		    root /data/up1;
		
		    location / {
		    }
		}
		
		server {
		    location / {
		        proxy_pass http://localhost:8080/;
		    }
		
		    location ~ \.(gif|jpg|png)$ {
		        root /data/images;
		    }
		}
}
```

- 이 서버는 `.gif`, `.jpg` 또는 `.png`로 끝나는 요청을 필터링하여 `/data/images` 디렉토리에 매핑하고(루트 지시어의 파라미터에 URI를 추가하여) 다른 모든 요청을 위에 구성된 프록시 서버에 전달합니다.

이 예시를 통해 기초적인 프록시 서버 설정 방법을 살펴보았습니다. 운영 환경에서는 추가적인 설정이 필요하므로 [Admin Guide](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)와 [프록시 모듈의 지시어들](https://nginx.org/en/docs/http/ngx_http_proxy_module.html)을 참조할 필요가 있습니다.

---

**참조**

[https://nginx.org/en/docs/beginners_guide.html](https://nginx.org/en/docs/beginners_guide.html)

<끌레망 네델꾸, Nginx HTTP 서버, 에이콘>
