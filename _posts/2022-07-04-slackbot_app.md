---
layout: article
title: Slackbot 만들기
tags: SlackBot
aside:
  toc: true
---

## SlackBot 구성

만들려고 하는 슬랙봇의 구성은 아래와 같다. <br>

<img width="961" alt="image" src="https://github.com/2cong/2cong/assets/60612551/a36d11f1-82d6-43b2-b483-12762242a8d9">

Slack API는 응답 시간이 3초로 제한되어 있고, slackbot의 각 메뉴마다 필요한 인자가 다르다. 이러한 제약 때문에 메뉴 버튼 생성, 모달 생성 및 작업을 큐에 추가하는 요청이 각각 따로 구현되었다.

http request 및 enqueue 작업은 Azure Functions을 사용해서 서버리스로 구성했다. 


## Create New App

- [Slack APP 설정](https://api.slack.com/apps) 페이지에서 App 생성

<img width="979" alt="image" src="https://github.com/2cong/2cong/assets/60612551/273d57bc-b23a-4402-8f93-22b1fb77953c">


## Slash Command 설정

<img width="871" alt="image" src="https://github.com/2cong/2cong/assets/60612551/d346bf60-2795-429c-8eca-e221ec19d9da">

- Command 에 slash command 호출할 명령어 정의해주기
- Request URL
  - slash command를 입력하면 호출될 HTTP 주소
  - 이미지에서는 예시로 localhost 주소를 입력해두었지만 실제로는 azure function을 연결해두었다!


## Interactivity & Shortcuts 설정

- Slackbot에 사용하는 모달은 [슬랙](https://api.slack.com/block-kit/building)의 Block Kit Builder 를 사용하면 쉽게 만들 수 있다!

<img width="389" alt="image" src="https://github.com/2cong/2cong/assets/60612551/e70574f3-e397-4422-b11f-3b9059979f9e">

- Interactivity의 Request URL
  - slash command로 생성된 메뉴 **Button**을 누르면 호출할 HTTP 주소
  - 이미지에서는 예시로 localhost 주소를 입력해두었지만 실제로는 [azure function을 연결](https://learn.microsoft.com/ko-kr/azure/azure-functions/functions-create-storage-queue-triggered-function)해두었다.
  - 따라서 버튼을 누르면 Dag를 trigger하는 작업이 queue에 보내지게 된다!

## Workspace에 App Install

- 위의 설정 후 [Slack APP 설정](https://api.slack.com/apps) 페이지에서 설치 할 App 클릭 하고 workspace에 App Install 하면 된다.

<br>
<br>
 


