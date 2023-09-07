---
layout: article
title: nginx 파일 업로드 크기 제한 
tags: nginx
aside:
  toc: true
---
### 이슈 사항

업로드된 CSV 파일을 처리하는 기능을 만들었는데 특정 파일들이 업로드 되지 않는 문제가 있었다..! 
이러한 파일들의 특징은 크기가 1mb를 넘어간 경우였다.
에러는 CORS 에러가 발생 !!
---


참고 : https://kangwoo.github.io/devops/kubernetes/ingress-nginx-file-upload-limit/

