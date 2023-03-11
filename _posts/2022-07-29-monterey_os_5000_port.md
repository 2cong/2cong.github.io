---
layout: article
title: MacOS Monterey 5000 port 이슈
tags: MacOS Monterey
aside:
  toc: true
---
<br>

### 이슈 사항
로컬 호스트를 이용하여 web을 띄울 때 5000 포트를 사용하면 간헐적으로 아래와 같은 403 에러가 발생했다.
``` shell
HTTP/1.1 403 Forbidden
```

----
### 원인
macOS Monterey의 AirPlay 수신모드가 5000포트를 사용하기 때문에 5000포트를 사용하여 서버를 띄우면 정상동작 하지 않을 수 있다고 한다.
- [참고](https://developer.apple.com/forums/thread/693768)

----

### 해결 방안
- 5000포트 사용하지 않거나
- AirPlay 수신 모드 사용하지 않기
    - 아래의 AirPlay 수신 모드 체크 해제
    ![image](https://user-images.githubusercontent.com/60612551/224476767-20a699f1-f06a-4ded-a4f0-07575053e2c8.png)

나는 5000 port를 사용하고 싶어서 AirPlay 수신 모드를 해체했다.
AirPlay 수신모드를 해제하고 나니 5000 port를 이용해도 정상적으로 동작했다!
