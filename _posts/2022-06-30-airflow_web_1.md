---
layout: article
title: Airflow Web | Ingress 설정하기
tags: Python Airflow
aside:
  toc: true
---

# Airflow Web UI 사용하기

## airflow UI에 접근하는 방법
- port-forward 사용
    - 아래의 명령어 입력 하여 airflow webserver 포트 포워딩
        
        ```bash
        export RELEASE_NAME=airflow-test  #  release된 airflow name
        export NAMESPACE=airflow-test     # release된 pod가 있는 namespace
        ```
        
        ```bash
        kubectl port-forward svc/${RELEASE_NAME}-web 8080:8080 --namespace $NAMESPACE
        ```
        
    - [localhost:8080](http://localhost:8080) 접속
        - default login  id / password : `admin` / `admin`

<br>

- Ingress 사용하기
    - 여기서는 nginx ingress를 사용할 것!


## Ingress 설정하기

[참고](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/)

### Add the Helm Repository

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### Install the Ingress Chart

```bash
helm install airflow-test-ingress ingress-nginx/ingress-nginx \
    --namespace airflow-test  \
		--release_name 
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

- 위의 명령어를 실행하면 아래와 같이 ingress Pod가 생성된다

<img width="558" alt="image" src="https://github.com/2cong/2cong/assets/60612551/ad30c1ba-4b11-4a77-af5c-0b76a6527db7">


### azure에서 DNS 설정

- ingress 설치하며 추가된 공용 IP에 DNS를 설정한다. 이미지에 있는 Subscription ID 등은 혹시 몰라서 지워두었다.. 🫢

<img width="921" alt="image" src="https://github.com/2cong/2cong/assets/60612551/dd0882b9-14a7-4e11-a5b1-fc9009ac07dc">

<img width="740" alt="image" src="https://github.com/2cong/2cong/assets/60612551/ac3b29ac-dd8b-4e19-b4f7-6f5adc0a3d8b">

<br>

- 위의 과정을 거치면 아래와 같이 DNS name을 얻을 수 있다! 참고로 이미지에 있는 DNS name도 임의로 변경해둔 이름이다!

<img width="1005" alt="image" src="https://github.com/2cong/2cong/assets/60612551/f2409c79-3d25-4479-91cb-4780479043b8">



### airflow의 values.yaml 수정하기

- 위의 ingress 설정으로 얻은 DNS name에 /airflow 를 추가한 주소가 airflow web 주소이다.
- 위의 DNS name을 예시로 airflow 주소를 만들면 아래와 같다.
    
    ```
    http://airflow-dns-test.cloudapp.azure.com/airflow
    ```
    

- 위의 web 주소를 이용하여 접속하면 아래와 같이 404 Not Found 페이지가 나옴
    
<img width="815" alt="image" src="https://github.com/2cong/2cong/assets/60612551/dbf2bdf3-dee2-410c-9616-a391b4a61b0d">

- airflow의 values.yaml 수정해주어야 위의 사이트에 접속할 수 있다!
    
    [values.yaml 수정 참고](https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/faq/kubernetes/ingress.md)

- 아래의 내용을 values.yaml에 추가해준다!

```yaml
config:
# 여기에 web 주소를 입력한다.
AIRFLOW__WEBSERVER__BASE_URL: "http://airflow-dns-test.cloudapp.azure.com/airflow"  


ingress:
   enabled: true

   ## WARNING: set as "networking.k8s.io/v1beta1" for Kubernetes 1.18 and earlier
   apiVersion: networking.k8s.io/v1

   ## airflow webserver ingress configs
   web:
     path: "/airflow"
     ingressClassName: "nginx"
     precedingPaths:
       - path: "/*"
         serviceName: "ssl-redirect"
         servicePort: "use-annotation"
```

### helm upgrade

- values.yaml 을 수정하고 helm chart 업그레이드 해준다.
    
    [참고](https://helm.sh/ko/docs/helm/helm_upgrade/)

```bash
helm upgrade [RELEASE] [CHART] [flags]
```

```bash
helm upgrade -f values.yaml $RELEASE_NAME airflow-stable/airflow -n airflow-test
```

- 위의 과정을 거친 뒤 airflow web 주소로 들어가면 접속 가능하다! 🎉
<img width="588" alt="image" src="https://github.com/2cong/2cong/assets/60612551/545b6d6a-d35a-450f-9b25-f1156ff7a86e">

<br>
<br>