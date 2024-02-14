---
layout: article
title: Airflow Web | Google OAuth 설정하기
tags: Python Airflow
aside:
  toc: true
---

## Airflow Google 계정으로 로그인하도록 설정하기
### Google OAuth  Client ID 만들기
[Google Developer Console](https://console.cloud.google.com/projectselector2/apis/credentials?supportedpurview=project)에서 OAuth Client ID 생성할 수 있다.

- 새로운 프로젝트 생성

프로젝트 이름 / 조직 등을 추가하고 프로젝트를 만든다!
<img width="588" alt="image" src="https://github.com/2cong/2cong/assets/60612551/08e636f6-42e6-4932-ae64-f7513e58cfc2">

- OAuth Client ID 생성하기
<img width="663" alt="image" src="https://github.com/2cong/2cong/assets/60612551/afe58f30-48a1-44cc-a3c5-fa51a0f84f05">
<img width="491" alt="image" src="https://github.com/2cong/2cong/assets/60612551/b1691222-6b05-497a-a17e-539f8e0fc8a8">


- 승인된 자바스크립트 원본 
  - airflow가 설치될 dns 정보
  - 여기는 앞의 [Airflow Web | Ingress 설정하기](https://2cong.github.io/2022/06/30/airflow_web_1.html) 에서 만들어둔 예시 dns 주소를 사용했다.
  ```
  http://airflow-dns-test.cloudapp.azure.com
  ```    

    

- 승인된 리디렉션 URI
  - {dns정보}/airflow/oauth-authorized/google 
  - 여기도 앞의 [Airflow Web | Ingress 설정하기](https://2cong.github.io/2022/06/30/airflow_web_1.html) 에서 만들어둔 예시 dns 주소를 사용했다.
  ```
  http://airflow-dns-test.cloudapp.azure.com/airflow/oauth-authorized/google
  ```

- 위와 같이 생성하면 client id, secret을 얻을 수 있다!

## Values.yaml 수정하기
### 기존 users 제거하기

- default로 있던 users를 제거하기
- 필수는 아니다..!
- 기존의 users를 아래와 같이 변경해주면 됨

```yaml
users: []
```

### Google OAuth 설정하기

- Airflow Web UI는 Flask를 사용한다.
- 자동으로 생성되는 webserver_config.py 로 구성된다.
- OAuth는 webserver_config.py에서 적용할 수 있다.
    - values.yaml의 webserverConfig에서 webserver_config.py를 수정할 수 있다.
    - [flask app builder oauth 설정 참고](https://flask-appbuilder.readthedocs.io/en/latest/security.html#authentication-oauth)
        
- 위의 설정을 참고하여 yalues.yaml에 아래의 내용 추가한다

```yaml

web:
  extraPipPackages:
    - "Flask-AppBuilder~=3.2.0" ## the following configs require Flask-AppBuilder 3.2.0 (or later)
    - "Authlib~=0.15.3" ## the following configs require Authlib

  ## configs generating the `webserver_config.py` file
  ## [FAQ] https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/faq/configuration/airflow-configs.md#webserver_configpy
  webserverConfig:
    ## the full content of the `webserver_config.py` file (as a string)
    stringOverride: |
      from airflow import configuration as conf
      from flask_appbuilder.security.manager import AUTH_OAUTH
      
      # the SQLAlchemy connection string
      SQLALCHEMY_DATABASE_URI = conf.get("core", "SQL_ALCHEMY_CONN")
      
      # use embedded DB for auth
      AUTH_TYPE = AUTH_OAUTH

      # Additionally you can customize the name of the builtin roles for Admin and Public accesses
      AUTH_ROLE_ADMIN = 'Admin'
      AUTH_ROLE_PUBLIC = 'Public'

      # registration configs
      AUTH_USER_REGISTRATION = True  # allow users who are not already in the FAB DB
      AUTH_USER_REGISTRATION_ROLE = "Viewer"  # this role will be given in addition to any AUTH_ROLES_MAPPING

      OAUTH_PROVIDERS = [
        {
        "name": "google",
        "icon": "fa-google",
        "token_key": "access_token",
        "remote_app": {
            "client_id": "GOOGLE_KEY",         # 위에서 얻은 client_id 
            "client_secret": "GOOGLE_SECRET",  # 위에서 얻은 client_secret
            "api_base_url": "https://www.googleapis.com/oauth2/v2/",
            "client_kwargs": {"scope": "openid profile email profile"},
            "request_token_url": None,
            "access_token_url": "https://accounts.google.com/o/oauth2/token",
            "authorize_url": "https://accounts.google.com/o/oauth2/auth",
          },
        }]

      # a mapping from the values of `userinfo["role_keys"]` to a list of FAB roles
      AUTH_ROLES_MAPPING = {
        "FAB_USERS": ["User"],
        "FAB_ADMINS": ["Admin"],
        }

      # if we should replace ALL the user's roles each login, or only on registration
      # 이 부분이 true면 새로 로그인 할 때마다 role이 초기화 됨
      AUTH_ROLES_SYNC_AT_LOGIN = False

      # force users to re-auth after 30min of inactivity (to keep roles in sync)
      PERMANENT_SESSION_LIFETIME = 1800

```

### helm upgrade

```bash
helm upgrade -f values.yaml airflow-test airflow-stable/airflow -n airflow-test
```

 

위와 같이 helm upgrade를 진행하면 로그인 페이지가 아래와 같이 변경된다.

<img width="711" alt="image" src="https://github.com/2cong/2cong/assets/60612551/673b6a3f-68cb-4695-8882-3024890e1dd7">

<br>
<br>