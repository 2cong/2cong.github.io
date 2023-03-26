---
layout: article
title: mongoengine default field와 update
tags: mongodb mongoengine
aside:
  toc: true
---
### 이슈 사항

DB migration 작업 진행 중 default 값이 정의되어 있는 field에서 예상하지 못한 동작을 발견했다 🥹<br>
default 값이 정의되어 있는 field에 default value와 같은 값을 넣는 경우 해당 내용이 collection에 추가 되지 않는 문제였다. <br>
위의 현상이 어떨 때는 발생하고 또 어떨 때는 발생하지 않아서 원인을 파악해보기로 했다..! 👊🏻

---

### 참고 사항 
#### DB migration 작업 방향
현재 진행하고 있는 DB migration 작업은 크게 두가지 종류로 나뉜다.
1. 존재하는 collection에 새로운 내용 추가하기 (DB collection 병합)
<img width="545" alt="image" src="https://user-images.githubusercontent.com/60612551/224525778-0f0e8f1f-1e49-4d7c-bba2-11f4d8bd4698.png">

<br>
2. 새로운 collection 생성하기 (다른 DB로 collection 이동)
<img width="545" alt="image" src="https://user-images.githubusercontent.com/60612551/224525838-ffbdd749-a599-4909-a2f2-82dd9441a638.png">

---
### default

[mongoengine- issue](https://github.com/MongoEngine/mongoengine/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc)에서
아래의 내용들을 통해 mongoengine에서 default가 무엇이고 어떻게 사용하면 되는지 확인할 수 있었다.

[issue 734](https://github.com/MongoEngine/mongoengine/issues/734)
<img width="933" alt="image" src="https://user-images.githubusercontent.com/60612551/224502136-d278115d-1ad0-470e-906f-048e0f071881.png">
[issue 735](https://github.com/MongoEngine/mongoengine/issues/735)
<img width="933" alt="image" src="https://user-images.githubusercontent.com/60612551/224502106-3f3277de-c391-4691-a7df-7d5314017a0c.png">

<br>
- default 
  - 새로운 object를 생성시에 사용되어야 함
  - missing된 값을 의미
  - 처음으로 설정되는 값이 아님


<br>

#### 참고 예시

```python
 class User(Document):
    name = StringField(max_length=50)
    member_type = StringField(default="guest")

    
# user 생성
 User.objects.insert([User(name="2cong")])

 
# object를 생성할 때 missing된 값은 default 값이 사용된다.
User.objects(member_type="guest").count() # 1 -> missing된 값은 default를 이용하여 생성됨

User.objects(member_type__exists=True).count() # 1 

user.name # 2cong

user.member_type # guest
```
위와 같이 처음 생성시에 missing된 값은 default를 사용한다. 또 해당 field에 default 값이 초기 세팅된다 🙆‍♀️

<br>
위의 모델에 field가 추가되는 변경이 생겼다면 어떨까? 위의 class가 아래의 예시와 같이 재정의 되면 다음과 같이 동작한다.

```python
class User(Document):
    name = StringField(max_length=50)
    member_type = StringField(default="guest")
    gender = StringField(default="female") # 새로운 default field가 추가되었다.
 

# 새로운 field가 추가되기 전에 생성된 user object 조회
user = User.objects(name="2cong").first()


user.name # 2cong (생성시에 정의된 값)

user.member_type # guest (생성시에 정의된 값)

user.gender # female -> missing value로 default 값을 사용한다.

User.objects(gender__exists=True).count() # 0

User.objects(gender="female").count() # 0
```

처음 생성되었을 때와 다르게 해당 field에 default 값이 초기 세팅되지 않았다! field의 값을 조회할 때는 default 값을 보여주었으나 실제 DB에는 해당 필드에 값이 추가되지 않았다 🙅‍♀️

<br>
위의 이유로 작업 방향 2번과 같이 새로운 DB를 생성하는 경우에는 default value가 DB에 함께 추가될 수 있었고,
1번과 같이 기존 DB에 새로운 field를 추가한 경우에는 default value를 정의해두었어도 실제로 DB에 추가되지 않는 것이었다.

<br>

### save()

default value를 DB에 저장하기 위해 `save()`를 사용하면 어떻게 될까?
<br>

default 예시 에서 사용한 user 객체를 이용해서 실험해보면 아래와 같다.

```python
User.objects(gender__exists=True).count() # 0

User.objects(gender="female").count() # 0 


# 새로운 field가 추가되기 전에 생성된 user object 조회
user = User.objects(name="2cong").first()


# user.gender에 default와 같은 값 할당 후 저장
user.gender = "female"
user.save()

 
# count 값에 변경이 없음 🥹
User.objects(gender__exists=True).count() # 0

User.objects(gender="female").count() # 0
```

위와 같이 할당 후 `save()`를 실행했으나 DB에 field가 추가되지 않았다 🥹
<br>

field가 저장되지 않은 이유를 찾아보기 위해 mongoengine의 `save()` 함수를 살펴보자..!!<br>
mongoengine에서는 `save()` 함수를 아래와 같이 설명하고 있다.
> If the document already exists, it will be updated, otherwise it will be created. Returns the saved object instance.

<br>

즉, document가 존재하는 경우에는 save 함수에서 update를 하게 된다. 이 동작 과정을 간략하게 정리하면 아래와 같다.
- `save()` 가 `_save_update()` 호출
- `_save_update()` 에서 `_get_update_doc()` 호출하여 update할 doc를 가지고 옴
- `_get_update_doc()`는 update할 doc을 구하기 위해 `_delta()` 호출
  - `_delta()`에서는 fields의 value가 default 값과 같으면 변경된 값으로 인식되지 않음

**따라서 save()의 과정 중 fields의 value가 default 값과 같으면 변경될 값으로 인식되지 않고, 그로인해 field가 계속 누락된 상태로 남게된다는 것이다 🙊**

아래의 이미지와 같이 user를 조회하는 find 쿼리만 수행되고 update 쿼리는 수행되지 않음을 확인할 수 있다.
<img width="1387" alt="image" src="https://user-images.githubusercontent.com/60612551/224528173-179dfee3-bfd2-4360-aebc-9ed0bfcfd497.png">

<br>

### update()

그렇다면 `update()`를 사용하면 어떻게 될까?

이번에도 default 예시 에서 사용한 user 객체를 이용해서 실험해보자..!

```python
User.objects(gender__exists=True).count() # 0

User.objects(gender="female").count() # 0 


# 새로운 field가 추가되기 전에 생성된 user object 조회
user = User.objects(name="2cong").first()


# user의 gender field에 default와 같은 값 update
user.update(gender="female")

 
# count 값에 변경이 있음 😙
User.objects(gender__exists=True).count() # 0

User.objects(gender="female").count() # 0
```

`update()`를 직접적으로 이용하는 경우에는 fields의 value가 default 값과 같아도 값이 추가된다. 

아래의 쿼리를 보면 find와 update가 둘 다 수행됨을 확인할 수 있다..!
<img width="1387" alt="image" src="https://user-images.githubusercontent.com/60612551/224528686-0b32c0cf-3336-477b-b23a-a4274822f4a6.png">

<br>
하지만 모든 document를 각각 하나씩 update 하는 것은 좋은 방법이 아니기 때문에 위의 update를 활용하여 문제를 해결하는 것은 어려워보인다 🙊

----

### 현재 DB Bulk update 방식

현재 DB를 업데이트 하는 방식을 간단히 설명하면 아래와 같다.
- mongoengine의 `_get_changed_fields()` 함수를 이용하여 changed_fields가 있는지 확인한다.
  - 이 때, 내가 변경한 값이 field의 default 값과 같으면 changed_field 라고 인식하지 않는다 🥲
- changed_fields가 있으면 `_get_update_doc()` 함수를 이용하여 update할 doc을 구한다.
  - 이 때도 default 값과 같으면 changed 라고 인식하지 못한다 🥹
- `_get_update_doc()`와 + pymongo의 `bulk_write`를 이용해서 document를 bulk_update 한다.

위의 방식대로 bulk update를 수행하고 있었기 때문에 
기존 DB에 새로운 field를 추가한 경우 (작업방향 1번의 경우)에 새로 추가된 field에 default 값이 입력되면, 이를 변경 값으로 인식하지 못했고 결국 field에 값이 누락되게 된 것이다.

-----

### 해결 방안 (?)
default 값을 변경 값으로 인식하지 않는 `_get_changed_fields()`, `_get_update_doc()`, `_delta()` 등의 함수를 이용해서는 DB에 값을 추가할 수 없기 때문에 

1. changed_fields가 있는지 확인하는 과정을 거치지 않고 (default value가 변경 값이면 이 부분에서 확인이 되지 않아서)
2. `_get_update_doc()` 을 이용하지 않고 update할 doc에 default 값들도 전부 넣어주기

#### 예시
```python
def get_update_doc(document):
    updates, removals = document._delta()  # 변경 값 구하기 (default가 아닌 변경 값들은 여기서 구해진다!) 
    
    update_doc = {}
    if updates:
        update_doc["$set"] = updates
    if removals:
        update_doc["$unset"] = removals
    
    # default value가 있는 field name 구하기
    default_fields = [name for name, value in document._fields.items() if value.default is not None]
    
    # update_doc에 update할 내용 추가하기
    for field_name in document.to_mongo().to_dict():
        if field_name in default_fields and field_name not in updates:
            update_doc["$set"][field_name] = document.to_mongo().to_dict()[field_name]

```

위와 같은 방식으로 update_doc을 구한 후 pymongo의 `bulk_write`를 이용해서 document를 bulk_update 하면 된다..!

이 때 document를 dict 형식으로 변경해주어야 한다..! document에 저장된 data는 StringEnumField인 경우 Enum의 형태로,
EmbeddedDocumentField인 경우는 EmbeddedDocument로 저장되어 있기 때문에 변경해주지 않고 update_doc에 바로 넣게 되면 update하는 과정에서 `bson.errors.InvalidDocument: cannot encode object` 에러를 반환하게 된다.

**한계점** : 누락되지 않고 실제 DB에 값이 들어있는 field인 경우도 업데이트 된다..ㅠ 이 경우는 결국 같은 값에서 같은 값으로 update하게 됨..🥹 (field에 default가 정의되어 있는 field는 무조건 업데이트 🤦‍♀️🤦‍♀️🤦‍♀️) 
