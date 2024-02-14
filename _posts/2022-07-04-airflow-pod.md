---
layout: article
title: Airflow Pod Mount 하기
tags: Python Airflow
aside:
  toc: true
---

## Azure File Shares 생성

- Storage account 하위에 Files Shares 생성
- [File Shares 만들기](https://docs.microsoft.com/ko-kr/azure/storage/files/storage-how-to-create-file-share?tabs=azure-portal)
- Access Key 확인
  - File Shares가 있는 storage account의 Key 가져오기
<img width="638" alt="image" src="https://github.com/2cong/2cong/assets/60612551/72a1ad89-943f-478b-8914-50b9a06cbd1b">


## Values.yaml 수정
### kubernetesPodTemplate 설정하기

- ConfigMap 또는 Secret 파일을 KubernetesExecutor pod template으로 설정할 수 있다.
    - [ConfigMap](https://kubernetes.io/ko/docs/concepts/configuration/configmap/)
        - Kubernetes Pod의 환경 변수 등으로 사용할 수 있는 데이터 저장 객체
        - 기밀이 아닌 값 저장
    - [Secret](https://kubernetes.io/ko/docs/concepts/configuration/secret/)
        - Kubernetes Pod의 환경 변수 등으로 사용할 수 있는 데이터 저장 객체
        - ConfigMap과 유사하지만 기밀 데이터 보관 가능

### Secret 생성
- 여기서는 Secret을 이용하여 KubernetesExecutor pod template을 설정할 것이다!
- [Create a Kubernetes secret 참고](https://docs.microsoft.com/en-us/azure/aks/azure-files-volume#create-a-kubernetes-secret)
- secret을 생성할 때 namespace를 지정하지 않으면 default에 저장되어 다른 네임스페이스에서 찾을 수 없으므로 꼭 지정해줘야 한다.
   
```bash
kubectl create secret generic {secret_file_name} -n {name_space} --from-literal=azurestorageaccountkey={storage_account_key}== --from-literal=azurestorageaccountname={storage_account_name}rom-literal=azurestorageaccountname={storage_account_name}
```
    

- [values.yaml 참고](https://github.com/airflow-helm/charts/blob/main/charts/airflow/docs/faq/kubernetes/mount-files.md)
```yaml
kubernetesPodTemplate:
    extraVolumeMounts:
      - name: # 마운트할 볼륨의 이름 (아래 extraVolumes에서 정의된 볼륨 이름과 일치해야함)
        mountPath: # 컨테이너 내에서 해당 볼륨이 마운트될 경로
        
    extraVolumes:
      - name: # 볼륨의 이름
        azureFile:
          secretName: # 위에서 설정한 secret_file_name
          shareName: # Azure storage account 내에 생성된 File Share 이름
```

<br>
<br>