---
layout: article
title: IntelliJ | VM Options 자동으로 설정하기
tags: IntelliJ
aside:
  toc: true
---

Spring Boot 프로젝트에서 Junit을 이용하여 Test를 실행하고 있다. <br>
이 때 VM option을 준 다음 테스트를 실행해야하는데, 매번 configuration에 추가해주는게 번거로워서 자동으로 VM options을 설정하게 수정하였다!

### Configuration templates 수정하기
먼저 아래의 이미지와 같이 Edit Configurations에 들어가서 Edit Configuration templates에 들어간다.

<img width="804" alt="image" src="https://github.com/2cong/2cong/assets/60612551/63ff3709-0524-4e65-bf10-23b95f006f54">

그 다음 아래 이미지 처럼 설정해주면 새로 생기는 configuration들은 아래 Template에 정의해둔 내용이 자동으로 반영된다.

<img width="727" alt="image" src="https://github.com/2cong/2cong/assets/60612551/d06ed854-1981-4e5f-9b0a-16f73a575d69">


---
### 참고
Grdle로 Test를 돌릴 때는 build.gradle의 test 부분에 아래의 내용을 추가해주면 Configuration template을 수정하지 않아도 VM option이 적용되는 것 같다..!! 😮

```gradle
tasks.test {
    jvmArgs(
        "--add-opens", "java.base/java.time=ALL-UNNAMED",
    )
}
```

<br>
<br>