---
layout: article
title: Django | DB에 CSV 파일 데이터 추가하기
tags: Django Python CSV MySQL
aside:
  toc: true
---

아래의 방법을 이용하여 CSV 파일의 데이터를 Django 프로젝트에 연결된 DB에 넣을 수 있다. 여기서 사용한 DB는 MySQL이다.

먼저 실행 파일을 만든다. 만들어진 파일을 실행하면 DB에 데이터가 추가된다. <br>
실행 파일은 Django의 manage.py가 있는 위치에 생성한다.

실행 파일의 내용은 아래와 같다.

```python
 import csv
 import os
 import django
 import sys

 os.chdir(".")
 print("Current dir=", end=""), print(os.getcwd())

 BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
 print("BASE_DIR=", end=""), print(BASE_DIR)

 sys.path.append(BASE_DIR)

 os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project1.settings")   # 1. 프로젝트명.settings 입력
 django.setup()
 
 # 위의 과정까지가 python manage.py shell을 키는 것과 비슷한 효과

 from car.models import *   # from {App이름}.models

 # modelversionline
 CSV_PATH = './model.csv'   # csv 파일 경로

 with open(CSV_PATH, newline='') as csvfile:	# 2. newline =''
     data_reader = csv.DictReader(csvfile)

     for row in data_reader:
         print(row)
         Model.objects.create(		# class명.objects.create (여기서는 Model이 class 이름)
             name = row['model']
             # 3
         )
 
 ...     
         
 CSV_PATH = './CSV/modelversionline.csv'    # csv 파일 경로        
 with open(CSV_PATH, newline='') as csvfile:    
     data_reader = csv.DictReader(csvfile)
     for row in data_reader:
         ModelVersionLine.objects.create(   # class명.objects.create (여기서는 ModelVersionLine이 class 이름)
              model = Model.objects.get(id=row['model_id']),    # 4. Foreign Key
              version = Version.objects.get(id=row['version_id']),
              line = Line.objects.get(id=row['line_id']),
              spec = Spec.objects.get(id=row['spec_id']),
              dimension = Dimension.objects.get(id=row['dimension_id']),
              code = row['code']
         )
```


#### 1. 프로젝트명.settings 입력
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project1.settings")의 project1.settings 부분에 "프로젝트명.settings"을 입력한다. <br>
여기서는 프로젝트 이름이 project1이라서 위와 같이 입력했다.

#### 2. newline =''
newline 인자를 이용하여 line을 나누는 기준을 정해주어야 한다. 위의 명령어를 사용하면 ' ' (공백)이 있을 시 새로운 줄로 인식한다.


#### 3. name = row['model']
여기서 'name'은 내 Model class에 입력된 데이터 이름이고 row['model']은 내 CSV에 입력된 header 명이다. <br>
즉 DB의 name 필드에 csv의 'model' row 하위의 내용이 입력된다는 뜻이다.

- 예시)

  ![](https://images.velog.io/images/2cong/post/1b42eb94-37e2-401b-9d91-2bf93903788c/image.png)
  
  위 CSV 파일의 model 하위의 내용 (Ghibli, Levante ..)이 아래의 Model class의 name 필드에 대응되는 것이다. 따라서 models 테이블에 Ghibli, Levante 등 의 내용이 한개씩 생성된다.

  ```python
  # Model class
   
   class Model(models.Model):
       name = models.CharField(max_length = 50) # fields 이름 : name
  
       class Meta:
           db_table = 'models' # 실제 MySQL에 table 이름으로 들어가게 될 내용
  ```
  
  아래의 이미지는 위의 실행파일을 실행했을 때 생긴 데이터이다!
  ![](https://images.velog.io/images/2cong/post/1983ccfa-bdd4-427a-a64a-c16718f75445/image.png)

#### 4. Foreign Key

foreign Key 필드는 아래와 같이 표현한다.
```python
line = Line.objects.get(id=row['line_id']),
```

여기서 line이 해당 table의 field 명이 되고 그 값으로 Line class의 id 값이 들어가게 되는 것이다.

이 때 주의할 점이 있다. 바로 **Foreign Key를 가지는 데이터는 원본 데이터가 먼저 데이터베이스에 등록되어야한다**는 것이다. <br>
따라서 ModelVersionLine라는 모델이 등록되기 전에 Model, Version, Line, Spec, Dimension 모델이 먼저 등록되어야한다.
 <br>
 <br>