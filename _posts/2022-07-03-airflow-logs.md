---
layout: article
title: Airflow Logs 관리하기
tags: Python Airflow
aside:
  toc: true
---

## Azure Blob Storage 생성

- [BlobStorage container 만들기](https://docs.microsoft.com/ko-kr/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
- connection string 확인하기
    - Blob Storage가 있는 storage account의 connection string 가져오기
<img width="638" alt="image" src="https://github.com/2cong/2cong/assets/60612551/72a1ad89-943f-478b-8914-50b9a06cbd1b">


- container 생성해주기
    - 해당 storage account 하위에 container를 생성해준다.
  <img width="875" alt="image" src="https://github.com/2cong/2cong/assets/60612551/c81846ac-f98f-40dd-ad55-971635d3da9e">

## Values.yaml 수정

- connections 정보 추가
    - [참고](https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/faq/dags/airflow-connections.md#azure-blob-storage-connection)
    - values.yaml을 수정하여 connections 정보를 자동으로 수정하도록 한다.
    
    ```yaml
    connections:
      - id: airflow_logs   # connection id
        type: wasb
        extra: |
          { 
            # 위에서 얻은 connection string을 여기에 적어둔다
            "extra__wasb__connection_string": "CONNECTION_STRING"
          }
    ```
   
<br>
 
- 위의 내용 수정 후 helm upgrade 하면 아래와 같이 connection이 생성된다.
  <img width="532" alt="image" src="https://github.com/2cong/2cong/assets/60612551/a301aeff-da06-46bb-b1c4-38da3336d701">


- remote log config 설정
    - [참고](https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/faq/monitoring/log-persistence.md)

```yaml
config:
    ## remote log
    AIRFLOW__LOGGING__REMOTE_LOGGING: "True"
    AIRFLOW__LOGGING__REMOTE_BASE_LOG_FOLDER: "wasb-airflow-test-log" # 생성할 container 이름
    AIRFLOW__LOGGING__REMOTE_LOG_CONN_ID: "airflow_logs"  # 위에서 정의한 conn_id
```

여기에서 AIRFLOW__LOGGING__REMOTE_BASE_LOG_FOLDER 이름의 시작을 반드시 `wasb`으로 해줘야 한다.

<br>
위의 수정한 내용 배포하고 test 실행하면 airflow-logs 하위에 로그 폴더가 생기고 로그가 기록된다.

<img width="766" alt="image" src="https://github.com/2cong/2cong/assets/60612551/8c2bd751-a31e-48b5-aeaa-d564220b23f8">

<br>
<br>