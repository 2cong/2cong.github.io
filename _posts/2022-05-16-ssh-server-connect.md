---
layout: article
title: SSH 원격 접속하기
tags: ssh
aside:
  toc: true
---

#### SSH 원격 접속


아래의 명령어로 원격 서버에 접속할 수 있다.
```shell
ssh -i 인증키 -p 포트번호 호스트유저@IP주소
```

---

**~/.ssh/config**에 서버 정보를 등록해두는 방법도 있다! <br>
ssh config에 서버 정보를 등록해두면 위와 같이 인증키나 포트번호 등을 입력하지 않고도 편하게 원격 서버에 접속이 가능하다 🤓

```shell
 Host hostname                    # 접속할 서버의 이름
      HostName XXX.XXX.XXX.XXX    # ip 주소
      User user_name              # 접속할 host user 이름
      Port port_number            # 포트 번호
      IdentityFile ~/.ssh/id_rsa  # identity_file 경로
```

위와 같이 config를 설정해두면 아래의 명령어로 간단하게 원격 서버에 접속할 수 있다..!

```shell
ssh hostname
```

---

####  config file을 인식하지 못할 때 🥺

ssh -i ~ 를 이용한 경우는 접속이 되었는데, config 정보를 이용하여 접속하려고 하니 아래와 같은 에러가 발생했다 🙊

```shell
ssh: Could not resolve hostname [hostname]: nodename nor servname provided, or not known
```
ssh config에 해당 hostname이 없을 경우 위의 에러가 발생한다고 했다...! 
난 config에 정보를 입력해 둔 상태인데 뭔가 config 파일 자체를 못찾는 것 같았다. 

찾아보니 ssh dir에 권한이 없는 파일이 있는 경우에 발생할 수 있는 이슈였다. <br>

<img width="673" alt="image" src="https://user-images.githubusercontent.com/60612551/230703829-125d97ba-a02e-4db9-8072-2f255392a96b.png">

<br>

위의 [질문](https://superuser.com/questions/1150158/why-is-ssh-ignoring-user-config-file)을 참고하여 아래의 명령어를 실행하니 ssh hostname 으로 접속할 수 있었다~! 🎉

```shell
sudo chown -R <user> ~/.ssh
```

<br>
