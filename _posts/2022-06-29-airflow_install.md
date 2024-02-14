---
layout: article
title: k8s에 Airflow 설치하기
tags: Python Airflow
aside:
  toc: true
---

Airflow를 Kubernetes 위에 배포할 예정이므로 미리 클러스터와 네임스페이스를 생성해두었다!

# Airflow 설치하기

- 참고 : [airflow-helm / charts](https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/guides/quickstart.md)
- Helm 참고하면 좋은 [블로그](https://malwareanalysis.tistory.com/193)

### Install Helm

```bash
brew install helm
```

### Add the Helm Repository

```bash
# add this helm repository
helm repo add airflow-stable https://airflow-helm.github.io/charts

# update your helm repo cache
helm repo update
```

### Create Values.yaml

 여기서는 KubernetesExecutor를 사용할 예정이므로 아래의 샘플 values.yaml 사용했다.

- [샘플 values.yaml](https://github.com/airflow-helm/charts/blob/main/charts/airflow/sample-values-KubernetesExecutor.yaml)
- values.yaml 항목에 대한 설명 및 기본값
    - [참고 1](https://github.com/airflow-helm/charts/tree/main/charts/airflow#helm-values)
    - [참고 2](https://airflow.apache.org/docs/apache-airflow/stable/configurations-ref.html)
    - [참고 3](https://airflow.apache.org/docs/helm-chart/stable/parameters-ref.html#airflow)
    - [참고 4](https://github.com/airflow-helm/charts/blob/main/charts/airflow/values.yaml)

- 그 외에 설정해야 할 값

```yaml
# 아래의 설정을 통해 dag가 어느 repo의 어느 branch를 볼지 설정할 수 있다.

dags:
  gitSync:
    enabled: true
    repo: # 연결할 git repo 주소
    branch: # git repo의 branch 명 
    revision: "HEAD"
    syncWait: 60
```


```yaml
# Dag를 api로 run하기 위해 필요한 설정

config:
    # api auth
		# slack bot에서 dag run할 때 api를 이용하여 호출하기 때문에 해당 설정 필요
    AIRFLOW__API__AUTH_BACKEND: 'airflow.api.auth.backend.basic_auth'
```


### Set env

- 환경 변수 세팅

```bash
export RELEASE_NAME=airflow-test  #  배포할 airflow 이름
export NAMESPACE=airflow-test     # 위에서 생성한 namespace 이름 지정해주기
export CHART_VERSION=8.6.1
export VALUES_FILE=values.yaml    # helm install에 사용할 values.yaml 파일 이름 
```

**[주의]**

- CHART_VERSION 및 values.yaml에 포함될 airflow version은 [airflow-helm chart 깃](https://github.com/airflow-helm/charts/tree/main/charts/airflow)에서 확인하여 적절하게 선택해야 한다!
- 여기서는 8.6.1으로 설치했다.

<img width="511" alt="image" src="https://github.com/2cong/2cong/assets/60612551/81c43025-6b72-4e85-87c4-76cb5c109dcf">

### **Install the Airflow Chart**

```bash
# helm install
helm install <Release_name> <Chart_name> --namespace <namespace> 
```


- 위의 명령어를 실행하면 아래와 같이 airflow Pod가 생성된다!

<img width="596" alt="image" src="https://github.com/2cong/2cong/assets/60612551/2a08a318-692c-480a-aea7-0c39512c47cd">


<br>
<br>




