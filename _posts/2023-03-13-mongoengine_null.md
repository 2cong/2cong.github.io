---
layout: article
title: mongoengine field에 null=True 의미
tags: mongoengine
aside:
  toc: true
---

mongoengine에서 fields를 정의할 때 parameter로 null 이라는 값을 받을 수 있다.

```python
class mongoengine.base.fields.BaseField(db_field=None, required=False,
                                        default=None, unique=False, unique_with=None,
                                        primary_key=False, validation=None, choices=None,
                                        null=False, sparse=False, **kwargs)
```

[공식 사이트](https://docs.mongoengine.org/apireference.html#fields)에서는 해당 parameter를 아래와 같이 설명하고 있다.

<img width="631" alt="image" src="https://user-images.githubusercontent.com/60612551/236611591-aa8c6b89-984c-49d5-a189-b660cd61055f.png">

<br>

설명을 봤을 때 예상한 동작 방식은 아래와 같다. 

<br>


##  예상한 동작 방식
`1.` null=True가 있어도 처음 생성할 때 초기 값이 없으면 **default value로 생성**됨! <br>
`2.` null=True가 있으면 default field의 value를 **None으로 변경 가능!** <br>
`3.` null=True는 default가 정의되지 않은 field에는 아무런 영향이 X <br>


그러나 실제로는 위와 같이 동작하지 않았다..🤦‍♀️ <br> 아래의 예시를 통해 null=True가 어떻게 동작하는지 확인해보자 👊🏻


<br>
📌 **예시에 들어가기 전 참고 사항 !!** <br>
Mongoengine에서 field에 default value가 정의되어 있는 경우 해당 field의 value로 None을 저장할 수 없다! <br>
　→ value가 들어오지 않는 경우 default로 생성되기 때문에

```python
class DefaultTest(Document):
    test = StringField(default='test_value')

# ✅ 초기값 없이 생성
test_obj = DefaultTest().save()

# default 값으로 생성
test_obj.test
>> 'test_value'


# ✅ default가 정의된 field에 None을 저장
test_obj.test = None
test_obj.save()

# None이 저장되지 않고 default 값이 그대로 사용됨
test_obj.test
>> 'test_value'


# ✅ 다른 값을 저장
test_obj.test = 'value'
test_obj.save()

# 값이 변경되어 저장된다!
test_obj.test
>> 'value'
```


---

##  예시

```python
class Test(Document):
    test1 = StringField()
    test2 = StringField(default='test2', null=True)
    test3 = StringField(default='test3')
    test4 = StringField(null=True)

# test1: default 값이 없는 경우
# test2: default 값이 있고 null=True인 경우
# test3: default 값만 있는 경우
# test4: default 값이 없고 null=True인 경우
```
<br>

### 예상한 내용 vs 결과

#### 초기값 없이 생성했을 때

```python
test = Test().save()

test.to_mongo()
>> SON([('_id', ObjectId('64564c4a8224d9c0ae175eb3')), ('test2', None), ('test3', 'test3'), ('test4', None)])
```

초기값을 설정하지 않은 경우 object는 위와 같이 생성되었다. 위 결과를 통해 1번과 3번 예상의 결과를 확인할 수 있었다.

<br>

`1.` null=True가 있어도 처음 생성할 때 초기 값이 없으면 default value로 생성됨 ? → ❌ <br>
확인해보니 **null=True면 default 값이 설정되어 있어도** default로 지정된 값을 사용하지 않고 **None 값을 저장**한다.

<br>
[mongoengine에 정의된 내용](https://github.com/MongoEngine/mongoengine/blob/master/mongoengine/base/fields.py)을 통해 이유를 알 수 있었다 🤓! <br>
아래의 코드는 BaseField 클래스의 `__set__` 함수 일부분이다.

```python 
class BaseField:
    def __set__(self, instance, value):
        """Descriptor for assigning a value to a field in a document."""
        # If setting to None and there is a default value provided for this
        # field, then set the value to the default value.
        if value is None:
            if self.null:
                value = None
            elif self.default is not None:
                value = self.default
                if callable(value):
                    value = value()
```

위 코드를 보면 입력된 value가 없는 경우에 필드가 null=True로 정의되어 있으면, default 값을 사용하지 않고 value에 None을 지정하고 있다. 이러한 이유로 예상 1번과 같이 동작하지 않았다 🫢


<br>

`3.` null=True는 default가 정의되지 않은 field에는 아무런 영향이 X ? → ❌ <br>
- null=False인 경우
  - test1 필드
  - to_mongo()로 확인해보니 **default 지정 여부와 관계 없이 field가 생성되지 않음**
    - test3 필드는 None이 아닌 default 값이 존재하므로 default 값으로 field 생성

  <br>

- null=True인 경우
  - test2 필드, test4 필드
  - to_mongo()로 확인해보니 **field가 생성되고 value로 None이 추가됨**

<br>
위 내용으로 field를 null=True로 설정한 경우 default 값 정의 여부와 관계 없이 field가 생성되고 value로 None이 추가됨을 확인할 수 있었다. 

그런데 [Issue 2344](https://github.com/MongoEngine/mongoengine/issues/2344)의 답변에서 value가 None인 경우 field가 생성되지 않는다고 했다..! 👀 

<img width="926" alt="image" src="https://user-images.githubusercontent.com/60612551/236843611-98f65a41-9cbd-4431-9f24-29124934c134.png">


그렇다면 왜 null=True인 경우 value가 None이라도 저장을 하게 되는걸까?! 이 부분도 코드를 살펴보도록 하자..! 🔍

이 [PR](https://github.com/MongoEngine/mongoengine/pull/1731/files)에서 null이 저장될 수 있도록 수정된 것 같다.



이런 이유로 예상 3번처럼 동작하지 않았다!

<br>

#### 초기에 설정된 값을 None으로 변경하는 경우

```python
# 초기값 지정하여 생성
test=Test(test1='1', test2='2', test3='3', test4='4').save()

test.to_mongo()
>> SON([('_id', ObjectId('645a2368ee63c69595726a82')), ('test1', '1'), ('test2', '2'), ('test3', '3'), ('test4', '4')])


# 다시 None으로 변경한 경우
test.test1 = None
test.test2 = None
test.test3 = None
test.test4 = None
test.save()

test.to_mongo()
SON([('_id', ObjectId('645a2368ee63c69595726a82')), ('test2', None), ('test3', 'test3'), ('test4', None)])

```

위 결과를 통해 2번 예상의 결과를 확인할 수 있었다.

<br>

`2.` null=True가 있으면 default field의 value를 None으로 변경 가능? → ⭕️ <br>

필드에 default 값이 정의되어 있을 때
- null=True 인 경우 (test 2) : None 으로 변경 가능 !
- null=False 인 경우 (test 3) : None 으로 변경 불가능 !

이 부분도 위의 [mongoengine에 정의된 내용](https://github.com/MongoEngine/mongoengine/blob/master/mongoengine/base/fields.py) 에서 알 수 있는 내용이었다!!

---

## 결론

`1.`  null=True + field의 value가 None인 경우 → **default value 여부와 상관없이 None으로 생성**됨! <br>
`2.`  null=True로 설정하면 default field의 value를 **None으로 변경 가능!** <br>
`3.`  null=True는 **default 지정 여부와 관계없이** value로 **None을 추가할 수 있음!**

<br>
