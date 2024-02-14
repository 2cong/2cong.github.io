---
layout: article
title: Slackbot을 이용하여 Airflow Dag Trigger하기 🔫
tags: Python Airflow SlackBot
aside:
  toc: true
---

요청을 받을 때 마다 직접 함수를 실행해서 처리해야하는 작업이 있었다. 이 작업을 Airflow Dag로 만들어두었으나, 요청자가 이를 직접 사용하기에는 어려움이 있었다. <br>
그래서 Slackbot을 통해 Airflow의 Dag를 Trigger하도록 했다! 이를 통해 작업을 자동화 할 수 있다 😌!

<br>
- 아래와 같이 Airflow 서버를 만든다. 그 후 Airflow Dag를 생성하고 Slack bot을 통해 Dag를 Trigger하면 된다!
- Dag 실행에 필요한 인자는 Slack bot에서 모달 등을 이용하여 전달할 수 있다.

## Airflow 서버 세팅하기

- [Airflow 설치하기](https://2cong.github.io/2022/06/29/airflow_install.html)
- [Airflow Web : Ingress 설정하기](https://2cong.github.io/2022/06/30/airflow_web_1.html)
- [Airflow Web : Google OAuth 설정하기](https://2cong.github.io/2022/06/30/airflow_web_2.html)
- [Airflow Logs 관리하기](https://2cong.github.io/2022/07/03/airflow-logs.html)
- Airflow Pod Mount 하기


## Slack bot 만들고 Airflow 연결하기
- [Slackbot 만들기](https://2cong.github.io/2022/07/04/slackbot_app.html)


<br>
<br>

