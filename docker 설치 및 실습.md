# docker 설치 및 실습

---

### 일반 설치

---

**업데이트 및 HTTP 패키지 설치**

```bash
$ sudo apt update
$ sudo apt-get install -y ca-certificates \ 
    curl \
    software-properties-common \
    apt-transport-https \
    gnupg \
    lsb-release
```

**GPG 키 및 저장소 추가**

```bash
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**도커 엔진 설치**

```bash
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

도커는 CE(Community Edition)과 EE(Enterprise Edition)으로 나뉜다.
EE는 유료 제공 기업용 솔루션이고, 본인은 무료 버전인 CE 버전을 사용하였다.
(핵심 컨테이너 기능은 동일함)

도커 버전 확인

```bash
$ sudo docker version
```

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled.png)

## docker 실습

---

도커 컨테이너로 Hello world 메세지를 출력하는 애플리케이션을 실행하겠다.

**docker container run 명령은 컨테이너로 애플리케이션을 실행**하라는 도커 명령이다.

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled%201.png)

이름이 diamol/ch02-hello-diamol 인 도커 애플리케이션 패키지(이미지)를 다운받고, 컨테이너로 애플리케이션을 실행해 메세지를 출력한다.

주황 사각형은 이 컴퓨터에서는 현재 해당하는 패키지가 없어 내려받는 과정,

초록 사각형은 패키지를 사용하여 컨테이너를 실행시켜, 애플리케이션이 로그를 출력한 내용이다.

또한 컨테이너가 실행 중인 환경을 출력하는데,

- 컴퓨터의 이름
- 운영제체 종류
- 네트워크 주소

를 나타내며, 동일한 명령을 다시 입력하면 실행 메세지 중 운영체제와 아키텍처에 관한 이름은 같으며, 컴퓨터 이름과 네트워크 주소에 대한 내용은 달라진다.

**컨테이너 환경**

같은 컴퓨터에서 컨테이너들은 운영체제와 CPU, 메모리를 공유하며, 호스트명, ip주소, 디스크를 독립적으로 가지게 된다.

**컨테이녀 접속 및 터미널 세션으로 컨테이너 조작**

```bash
$ docker container run --interactive --tty diamol/base
```

- --interactive  플래그를 사용하면 컨테이너에 접속된 상태가 된다.
- --tty 플래그는 터미널 세션을 통해 컨테이너를 조작하겠다는 의미다

**컨테이너 명령어**

```bash
$ docker container ls 
# 현재 실행중인 모든 컨테이너에 대한 정보 확인

$ docker container top f1
# 대상 컨테이너에서 실행 중인 프로세스 목록을 확인

$ docker container logs f1
# 대상 컨테이너에서 수집된 모든 로그 출력

$ docker container inspect f1
# 대상 컨테이너의 상세한 정보 출력
```

지금까지 텍스트 출력 후 종료되는 컨테이너와 대화식 컨테이너로 실행 실습을 진행하였는데,

터미널 세션을 끝내고 docker container ls 명령을 입력하면 현재 실행 중인 컨테이너가 없다고 나타난다.

1. 컨테이너 내부의 애플리케이션이 실행 중이어야 컨테이너의 상태도 실행 중이 된다.
애플리케이션 프로세스가 종료되면 컨테이너 상태도 Existed가 되며, CPU 자원이나 메모리를  사용하지 않는다.
2. 컨테이너는 종료되어도 사라지지 않는다. 
즉, 다시 실행하거나, 로그를 확인하거나, 새로운 파일을 복사하거나 할 수 있다. 
docker container ls 명령으로는 실행 중인 컨테이너의 목록만 볼 수 있지만, 종료된 컨테이너도 삭제하지 않는 한 그대로 남아 있다.
컨테이너의 파일 시스템이 그대로 남아 있으므로,, 호스트 컴퓨터의 디스크 공간을 계속 점유함

**컨테이너 백그라운드 실행 유지**

```bash
$ docker container run --detach --publish 8088:80 diamol/ch02-hello-diamol-web
```

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled%202.png)

- --detach : 컨테이너를 백그라운드에서 실행하며 컨테이너 ID를 출력한다.
- --publish : 컨테이너의 포트를 호스트 컴퓨터에 공개한다.

이후 브라우저에서 [http://localhost:8088](http://localhost:8088) 페이지에 접근한다.

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled%203.png)

HTTP 요청은 로컬 컴퓨터에서 보낸것인데, HTTP 응답은 컨테이너로부터 나온다.

**컨테이너 삭제**

컨테이너 사용이 끝난 후 `docker container rm` 명령에 컨테이너 ID를 지정하여 삭제할 수 있다.

- --force 플래그를 사용하면 실행 중인 컨티이너라도 삭제가 가능하다.

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled%204.png)

$() 문법으로 괄호 안 명령의 출력을 다른 명령으로 전달이 가능하다.

전체 명령으로 호스트 컴퓨터에 존재하는 모든 컨테이너 목록을 만든 다음 컨테이너를 제거하는 것이다.

삭제 전 확인 절차가 없기 때문에 사용할때 주의하여야 한다.

## 컨테이너 파일 시스템 다루기

---

연습문제. 

앞서 실행하였던 웹 사이트 컨테이너를 실행하고 index.html 파일을 교체해 웹 페이지 내용 수정

1. docker container 명령을 사용하여 컨테이너 대상의 기능 목록을 확인할 수 있다.
2. 모든 docker 명령어 --help 플래그를 추가하면 도움말을 볼 수 있다.
3. 사용한 도커 이미지(diamol/ch02-hello-diamol-web)안에 웹 페이지 파일은
/usr/local/apapche2/htdocs 경로로 존재한다

실행중인 컨테이너 파일 내용 수정 완료

![Untitled](docker%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%8E%E1%85%B5%20%E1%84%86%E1%85%B5%E1%86%BE%20%E1%84%89%E1%85%B5%E1%86%AF%E1%84%89%E1%85%B3%E1%86%B8%20d82667143af14e54a4da3b7a1efb6efe/Untitled%205.png)

- `docker exec -it a3 /bin/sh`
exec 명령어는 실행중인 컨테이너에서 명령을 실행할 수 있게 한다.
-i 옵션은 터미널과 컨테이너를 지속적으로 연결시켜주며, 
-t 옵션은 터미널 환경을 조성시켜 준다.
- 이후 index.html 파일을 찾아 vim를 사용하여 내용을 수정하였다.

---