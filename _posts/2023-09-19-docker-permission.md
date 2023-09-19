---
layout: article
title: Docker | daemon socket permission denied 오류 해결하기 !
tags: Docker Git_Workflows
aside:
  toc: true
---

##  이슈

Git Workflows 단계 실행 중 에러가 발생했다 🥹 <br>

### 에러가 발생한 단계
- Docker image pull 받기 (이미지는 Azure Container Registry 에서 pull 받는다)
- pull 받아온 이미지를 캐시로 활용하여 새로운 이미지 build 하기

### 발생한 에러
<img width="668" alt="image" src="https://github.com/2cong/2cong/assets/60612551/a3dc655a-60b1-4f9c-b4d0-c667fb2fada4">

```
ERROR: permission denied while trying to connect to the Docker daemon socket at
unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix
/var/run/docker.sock: connect: permission denied 
```

---
## 해결 과정

찾아보니 이 에러는 [Docker 데몬 소켓에 대한 액세스 권한이 없는 경우](https://stackoverflow.com/questions/51342810/how-to-fix-dial-unix-var-run-docker-sock-connect-permission-denied-when-gro)에 발생하는 에러였다.

위의 Workflow 는 GitHub Self-Hosted Runner를 이용하여 실행되고 있다. Runner가 호스팅되는 서버가 Docker 소켓에 대한 권한이 없어서 위의 문제가 발생한 것으로 보인다..! 🫢

먼저 내가 Runner로 사용하는 서버에서 docker ps 명령어를 쳐보더니 아래와 같이 권한 에러가 발생했다.
```shell
~ ❯ docker ps
Got permission denied while trying to connect to the Docker daemon socket at
unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json?all=1:
dial unix /var/run/docker.sock: connect: permission denied
```

docker.sock 파일의 권한을 변경해주었다.
```shell
sudo chmod 666 /var/run/docker.sock 
```

그 후 docker ps 를 다시 실행하니 권한 에러가 발생하지 않고 잘 동작하는 것을 확인할 수 있었다..!

```shell
~ ❯ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Runner 서버가 Docker 소켓에 대한 권한을 얻었으니 Runner를 재실행한다.
```shell
sudo ./svc.sh stop
sudo ./svc.sh start
```

위의 내용을 실행한 후 다시 Workflows를 실행하니 Docker Image가 성공적으로 build 되었다!! 🎉

<br>