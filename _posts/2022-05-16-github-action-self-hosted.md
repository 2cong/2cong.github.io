---
layout: article
title: Github Action self-hosted로 run하기
tags: github github-action self-hosted
aside:
  toc: true
---

on-premises 서버에 [Github action Self-hosted runner](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)를 세팅해보자~! 🤓

Runner를 설정하고 싶은 repo의 Settings -> Actions -> Runners 에 들어간다. <br>
여기서 **New self-hosted runner** 클릭! 

<img width="866" alt="image" src="https://user-images.githubusercontent.com/60612551/230701976-623d5e0e-280b-442a-bdb5-b2c5fe1953d5.png">

Runner를 세팅할 서버의 OS 타입을 선택한다. 나는 우분투 서버에 Runner를 세팅할 예정이라 Linux를 선택했다!

![image](https://user-images.githubusercontent.com/60612551/230702262-cb31ae49-2b1e-4291-a0dc-3ef683cd817c.png)

<br>
이제 화면에 나온 내용을 서버에서 복붙하여 실행하면 된다..! 😙

[ssh로 서버에 원격 접속](https://2cong.github.io/2022/05/16/ssh_server_connect.html)해서 해당 스크립트를 실행한다. <br>
전부 실행하고 나면 아래와 같이 runner가 생긴다 ✨

![image](https://user-images.githubusercontent.com/60612551/230703985-a5bcf30b-8db2-4208-ad16-0050b75e667d.png)

<br>
아래의 명령어로 runner를 실행할 수 있다!
```shell
./run.sh
```

---

#### self-hosted runner 자동으로 start 하기 ✨

원격 서버에서 runner를 실행한 이후 시간이 지났더니 runner가 offline 상태가 되었다 🙊 <br>
offline이 될 때 마다 원격 서버에 접속해서 runner를 실행할 수는 없으니 runner를 자동으로 start하게 만들어보자~! 

원격 서버에 접속해서 아래의 명령어를 입력한다. 👉🏻 참고 : [Configuring the self-hosted runner application as a service](https://docs.github.com/en/actions/hosting-your-own-runners/configuring-the-self-hosted-runner-application-as-a-service)
```shell
sudo ./svc.sh install

sudo ./svc.sh start
```

위의 명령어를 실행해두면 runner가 offline 되지 않고 idle 상태로 대기하게 된다! 

<br>