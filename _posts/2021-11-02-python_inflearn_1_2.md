---
layout: article
title: 우리를 위한 프로그래밍 | Ch 2. 파이썬 클래스 심화 
tags: Python
aside:
  toc: true
---

[우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)을 듣고 정리한 내용

<!--more-->

---
# Chapter 02 - 01

## 절차지향 vs 객체지향

- 절차지향 프로그래밍
    - 함수 중심 → 데이터가 방대해 짐 → 복잡하고 개선 어려움
      
    - 컴퓨터의 처리구조와 유사해 실행 속도가 빠름
  
- 객체지향 프로그래밍(OOP)
    - 코드의 재사용, 코드 중복 방지 등
      
    - 클래스 중심 → 객체로 관리됨 → 유지 보수가 쉬움
      
    - 설계에 많은 시간 소요가 발생
      
    - 절차지향보다 느림
  
- 만들고자하는 프로그램의 규모와 목적에 따라서 객체지향인지 절차지향인지를 결정하기
    
     → utils 코드는 절차지향으로 해도 충분함
<br><br>

## 객체를 사용하지 않은 경우

```python
# 객체를 사용하지 않은 경우
# 차량1
car_company_1 = 'Ferrari'
car_detail_1 = [
    {'color' : 'White'},
    {'horsepower': 400},
    {'price': 8000}
]


# 차량2
car_company_2 = 'Bmw'
car_detail_2 = [
    {'color' : 'Black'},
    {'horsepower': 270},
    {'price': 5000}
]


# 차량3
car_company_3 = 'Audi'
car_detail_3 = [
    {'color' : 'Silver'},
    {'horsepower': 300},
    {'price': 6000}
]
```
위와 같이 객체를 사용하지 않고 데이터를 하나씩 선언하는 경우 <br>
  → 차량이 추가될 때마다 반복적인 내용을 입력해줘야함 <br>
  → 따라서 데이터가 많아지고 변경될 때마다 수정이 번거로움
<br><br>

```python
# 리스트 구조
# 관리하기 불편
# 인덱스 접근 시 실수 가능성 증가, 삭제 불편 -> 인덱스로 접근해야하기 때문에
car_company_list = ['Ferrari', 'Bmw', 'Audi']
car_detail_list = [
    {'color' : 'White', 'horsepower': 400, 'price': 8000},
    {'color' : 'Black', 'horsepower': 270, 'price': 5000},
    {'color' : 'Silver', 'horsepower': 300, 'price': 6000}
]


# 자동차 회사 삭제
del car_company_list[1]
del car_detail_list[1]

```
데이터가 많아질수록 인덱스 관리가 어려워서 불편해짐
<br><br>

```python
# 딕셔너리 구조
# 코드 반복 지속, 중첩 문제 (키), 키 조회 예외 처리 등
cars_dicts = [
    {'car_company': 'Ferrari', 'car_detail': {'color' : 'White', 'horsepower': 400, 'price': 8000}},
    {'car_company': 'Bmw', 'car_detail': {'color' : 'Black', 'horsepower': 270, 'price': 5000}},
    {'car_company': 'Audi', 'car_detail': {'color' : 'Silver', 'horsepower': 300, 'price': 6000}}
]


del cars_dicts[1]
```
<br><br>

## 객체를 사용하는 경우

```python
# 클래스 구조
# 클래스 사용 장점 : 구조 설계 후 재사용성 증가, 코드 반복 최소화, 메소드 활용
# 기본적으로 class는 Object 상속받음
class Car():
    def __init__(self, company, details):
        self._company = company
        self._details = details

    def __str__(self):
        return 'str : {} - {}'.format(self._company, self._details)

    def __repr__(self):
        return 'repr : {} - {}'.format(self._company, self._details)
    

    
# 아래와 같이 instance를 생성할 수 있음
car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
car2 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})
car3 = Car('Audi', {'color' : 'Silver', 'horsepower': 300, 'price': 6000})
```

### \_\_str_\_() vs \_\_repr_\_()

- \_\_str_\_() 
    - 목적 : 객체를 표현하는 것(representation)보다는 추가적인 가공이나 다른 데이터와 호환될 수 있도록 <span style="color:#0067a3">**문자열**</span>화하는 것
      
    - informal(비공식적) 정보를 문자열로 출력
      
    - 사용자 입장의 출력
    
- \_\_repr_\_()
    - 목적 : 객체를 인간이 이해할 수 있는 평문으로 <span style="color:#DD2525">**표현(representation)**</span>하는 것
      
    - official(공식적) 정보를 출력
      
    - 엔지니어 레벨의 출력
    <br>
    

    ```python
    >>> car1.__str__()
    "str : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}"
    
    >>> car1.__repr__()
    "repr : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}"
    

    # 위의 class에서 car1을 호출하거나 car1을 print하면 아래와 같이 나타나게 됨
    

    >>> car1
    repr : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    

    >>> print(car1)
    str : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    ```
    
    ```python
    # class에 __str__과 __repr__가 정의되지 않은 경우
    
    class Car():
        def __init__(self, company, details):
            self._company = company
            self._details = details
    
    car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
    

    >>> car1
    <__main__.Car object at 0x7ff54ba3c310>
    

    >>> print(car1)
    <__main__.Car object at 0x7ff54ba3c310>
    
    # 위의 두 경우 모두 class 정보가 출력되게 됨
    ```
    
    ```python
    # class에 __str__만 정의된 경우
    
    class Car():
        def __init__(self, company, details):
            self._company = company
            self._details = details
    
        def __str__(self):
            return 'str : {} - {}'.format(self._company, self._details)
    

    car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
    

    >>> car1
    <__main__.Car object at 0x7ff54ba33580>
    

    >>> print(car1)
    str : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    

    # car1만 호출한 경우 class 정보가 출력되게 됨
    # print로 출력한 경우 __str__에 정의된 내용이 출력됨
    ```
    
    ```python
    # class에 __repr__만 정의된 경우
    
    class Car():
        def __init__(self, company, details):
            self._company = company
            self._details = details
        def __repr__(self):
            return 'repr : {} - {}'.format(self._company, self._details)
        

    car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
    

    >>> car1
    repr : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    

    >>> print(car1)
    repr : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    
    # 위의 두 경우 모두 __repr__에 정의된 내용이 출력됨
    ```
    
    객체를 출력할 때
    
    - **__str__**이 정의되어 있으면 **__str__**에 정의된 내용 출력함
    - **__str__**이 정의되어 있지 않으면 **__repr__**에 정의된 내용 출력함
    - 둘 다 정의되어 있지 않으면 class 정보 출력됨
    
    <br>
    
    ```python
    # __repr__와 __str__이 모두 정의된 Car class의 인스턴스 car1, car2, car3를 list에 추가한 경우
    car_list = []
    

    car_list.append(car1)
    car_list.append(car2)
    car_list.append(car3)
    

    # car_list 자체를 출력한 경우
    >>> print(car_list)
    [repr : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000},
     repr : Bmw - {'color': 'Black', 'horsepower': 270, 'price': 5000},
     repr : Audi - {'color': 'Silver', 'horsepower': 300, 'price': 6000}]
    

    # car_list 자체를 출력하면 내부의 인스턴스들은 repr에 정의된 내용으로 출력됨
    # **list안에서 객체에 대한 정보**를 보여주기 때문에 repr로 출력되는 것
    

    # 반복문으로 list에 있는 내용을 하나씩 출력할 때는 str에 정의된 내용으로 출력됨
    >>> for car in car_list:
        print(car)

    str : Ferrari - {'color': 'White', 'horsepower': 400, 'price': 8000}
    str : Bmw - {'color': 'Black', 'horsepower': 270, 'price': 5000}
    str : Audi - {'color': 'Silver', 'horsepower': 300, 'price': 6000}
    ```
<br><br>    

### \_\_dict_\_\() 와 dir()

```python
# __dict__를 사용하면 attribute 볼 수 있음
# dict 형태로 객체의 속성과 속성의 내용을 호출함

>>> car1.__dict__
{'_company': 'Ferrari', '_details': {'color': 'White', 'horsepower': 400, 'price': 8000}}


>>> car2.__dict__
{'_company': 'Bmw', '_details': {'color': 'Black', 'horsepower': 270, 'price': 5000}}


>>> car3.__dict__
{'_company': 'Audi', '_details': {'color': 'Silver', 'horsepower': 300, 'price': 6000}}

# dir(객체)를 사용하면 내부의 모든 메타정보(객체가 가진 속성과 메소드 정보)를 볼 수 있음 
# 아래와 같이 모든 메타정보가 list에 담겨서 출력됨
# class에 정의한 attribute도 함께 출력됨
 

>>> dir(car1)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', ... , 
 '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__',
 '_company', '_details']
```

---

# Chapter 02 - 02

## 클래스 변수 vs 인스턴스 변수

- 클래스 변수
  
    - 같은 class로 만들어진 모든 인스턴스가 공유하고 있는 데이터
  
- 인스턴스 변수
  
    - 각각의 인스턴스마다 가지고 있는 고유한 데이터
    

```python
# 클래스 변수, 인스턴스 변수
# class가 아래와 같을 때 car_count는 class 변수, __init__ 아래에 self로 정의된 변수는 인스턴스 변수
class Car():
    # class 변수
    car_count = 0

def __init__(self, company, details):
    # 인스턴스 변수
    self._company = company
    self._details = details

    
car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
car2 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})
car3 = Car('Audi', {'color' : 'Silver', 'horsepower': 300, 'price': 6000})
```

```python
# 인스턴스 변수
# 직접 접근(PEP 문법적으로 권장X)
# 아래와 같이 각 인스턴스마다 고유한 값을 가짐
>>> car1._company
'Ferrari'


>>> car2._company
'Bmw'


>>> car3._company
'Audi'

# 클래스 변수 -> 모든 인스턴스가 공유
# 아래와 같이 같은 값을 공유하게 됨
>>> car1.car_count
0

>>> car2.car_count
0

>>> car3.car_count
0

>>> Car.car_count
0

```

### Class와 Instance에서 \_\_dict_\_()와 dir()

```python
# Car class와 instance의 __dict__
# __dict__는 attribute를 key, value로 보여줌
>>> Car.__dict__
mappingproxy({'__module__': '__main__', 'car_count': 0,
              '__init__': <function Car.__init__ at 0x7ff54ba31dc0>,
              '__dict__': <attribute '__dict__' of 'Car' objects>,
              '__weakref__': <attribute '__weakref__' of 'Car' objects>,
              '__doc__': None})


>>> car1.__dict__
{'_company': 'Ferrari', '_details': {'color': 'White', 'horsepower': 400, 'price': 8000}}


>>> car2.__dict__
{'_company': 'Bmw', '_details': {'color': 'Black', 'horsepower': 270, 'price': 5000}}

# 인스턴스에 __dict__를 사용한 경우 클래스 변수인 car_count는 출력되지 않고 인스턴스 변수만 출력됨 
# 이는 인스턴스의 네임 스페이스에 car_count가 없기 때문에


# Car class와 instance의 dir
# dir은 해당 인스턴스가 가지고 있는 모든 attribute를 list로 보여줌 -> 상속받는 모든 것도 보여줌
>>> dir(Car)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', ..., 
 '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'car_count']


>>> dir(car1)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', ..., 
 '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__',
 '_company', '_details', 'car_count']

# 클래스에 dir()함수를 사용한 경우 속성값에 클래스 변수가 함께 출력됨
# 인스턴스에 dir()함수를 사용한 경우 속성값에 클래스 변수 및 인스턴스 변수가 함께 출력됨 -> 클래스 변수 및 인스턴스 변수를 호출 할 수 있다는 의미 
```

### Name space

<img class="image image--xl" src="https://user-images.githubusercontent.com/60612551/144032095-77a6a5f2-01ad-4d5e-a54e-dd400caed212.png"/>


- 모든 객체는 name space가 존재함
- 각각의 Instance는 자신만의 name space가 있음
- Class의 name space는 모든 객체가 공통으로 사용할 수 있음


```python
# 변수가 인스턴스 네임 스페이스 없으면 상위(클래스 네임 스페이스)에서 검색함
# 즉 동일한 이름으로 변수 생성 가능함 (인스턴스 네임 스페이스 검색 -> 상위(클래스 변수, 부모 클래스 변수) 검색)

class Car():
    car_count = 0

    def __init__(self, company, details):
        self._company = company
        self._details = details

car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})


# 위와 같이 인스턴스 변수에 car_count가 없는 경우 아래과 같이 클래스 변수가 출력됨
>>> car1.car_count
0


# 동일한 이름이 인스턴스 변수에 있는 경우
class Car():
    car_count = 0

    def __init__(self, company, details):
        self._company = company
        self._details = details
        self.car_count = 10

        
car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})


>>> car1.car_count
10

# 위와 같이 인스턴스 변수에 클래스 변수와 동일한 이름으로 정의되어 있는 경우 인스턴스 변수가 출력됨

>>> car1.__dict__
{'_company': 'Ferrari', '_details': {'color': 'White', 'horsepower': 400, 'price': 8000},
 'car_count': 10}
```

```python
# 동일한 이름을 인스턴스 변수에 할당하는 경우
class Car():
    car_count = 0

def __init__(self, company, details):
    self._company = company
    self._details = details

    
car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
car2 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})


# 동일한 이름을 할당하기 전에는 모두 같은 class 변수 값을 return
>>> car1.car_count
0


>>> car2.car_count
0


>>> Car.car_count
0


>>> car1.__dict__
{'_company': 'Ferrari', '_details': {'color': 'White', 'horsepower': 400, 'price': 8000}}


# class 변수와 동일한 이름을 인스턴스 변수에 할당
>>> car1.car_count = 10


>>> car1.car_count
10


>>> car2.car_count
0


>>> Car.car_count
0


# 변수 할당 후 __dict__를 이용하면 인스턴스 변수에 car_count가 추가됨
>>> car1.__dict__
{'_company': 'Ferrari', '_details': {'color': 'White', 'horsepower': 400, 'price': 8000},
 'car_count': 10}


>>> car2.__dict__
{'_company': 'Bmw', '_details': {'color': 'Black', 'horsepower': 270, 'price': 5000}}

# 위에서 car1 인스턴스에 class 변수와 동일한 이름의 변수를 할당함 (car_count)
# car_count가 car1의 인스턴스 변수로 추가됨
# 다른 인스턴스인 car2 및 class 변수에는 영향을 주지 않음
```

```python
# class 변수 자체를 변경했을 때
>> Car.car_count = 30


>> Car.car_count
30


# 클래스 변수 return
>> car2.car_count
30


# 인스턴스 변수 return
>> car1.car_count
10

# 위와 같이 class 변수를 변경했을 때 동일한 이름의 인스턴스 변수가 없는 경우는 클래스 변수 return
# 동일한 이름의 인스턴스 변수가 있는 경우는 인스턴스 변수 return
```

### Class의 id vs Instance의 id

```python
# class가 아래와 같을 때
class Car():
    car_count = 0

    def __init__(self, company, details):
        self._company = company
        self._details = details

        
car1 = Car('Ferrari', {'color' : 'White', 'horsepower': 400, 'price': 8000})
car2 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})
car3 = Car('Audi', {'color' : 'Silver', 'horsepower': 300, 'price': 6000})


# ID 확인
# 각 인스턴스의 id가 다름을 확인할 수 있음
>>> id(car1)
140691501654944


>>> id(car2)
140691497990080


>>> id(car3)
140691497990032


# 각 인스턴스의 변수 비교
# == 와 is의 차이는 아래에
>>> car1._company == car2._company
False


>>> car1 is car2
False


# 각 인스턴스의 class 비교

>>> car1.__class__ == car2.__class__
True


# 각 인스턴스의 class를 비교함 -> 둘 다 같은 Car() 클래스의 인스턴스 이므로 == 는 True

>>> id(car1.__class__) == id(car3.__class__)
True

# 각 인스턴스의 id가 아닌 각 인스턴스의 상위 클래스의 id를 비교한 것이므로 둘은 같음
```

- == 연산자 vs is 연산자
    - a == b
        - 객체의 **value**가 같은 경우
    - **a is b**
        - ***a is b*** is equivalent to <span style="color:#DD2525">***id(a) == id(b)***</span>
        - 객체의 **id**가 같은 경우
<br><br>
          
## Self의 의미

- 해당 Class의 Instance를 의미

```python
class Car():
    def __init__(self, company, details):
        self._company = company
        self._details = details

    def __str__(self):
        return 'str : {} - {}'.format(self._company, self._details)

    def __repr__(self):
        return 'repr : {} - {}'.format(self._company, self._details)

    def detail_info(self):
        print('Current Id : {}'.format(id(self)))
        print('Car Detail Info : {} {}'.format(self._company, self._details.get('price')))

        
# 에러인 경우
>>> Car.detail_info()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: detail_info() missing 1 required positional argument: 'self'

# 위의 경우는 self 인자를 받지 않았기에 에러 발생

 
Car.detail_info(car1)
Car.detail_info(car2) -> 명시적으로 self에 인스턴스 넘겨줘서 전달
car2.detail_info() -> 이렇게 전달해도 가능 자동으로 self에 인자 전달


# 정상 동작하는 경우
>>> car1.detail_info()
Current Id : 140691501804656
Car Detail Info : Ferrari 8000


>>> car2.detail_info()
Current Id : 140691501751264
Car Detail Info : Bmw 5000


# 위와 같이 인스턴스의 메소드로 호출하면 자동으로 self에 인스턴스를 넘기게 됨

>>> Car.detail_info(car1)
Current Id : 140691501804656
Car Detail Info : Ferrari 8000

# 위와 같이 명시적으로 self에 인스턴스를 넘겨주어도 가능함
```

## Docstring

```python
class Car():
    """
    Car Class
    Author : Kim
    Date : 2019.11.08
    """

    def __init__(self, company, details):
        self._company = company
        self._details = details

    
# 위와 같이 class에 주석을 달아둔 경우 __doc__ 메소드를 이용하면 주석 내용을 출력해줌
>>> print(Car.__doc__)
Car Class
Author : Kim
Date : 2019.11.08
```

---

# Chapter 02 - 03

## 클래스 기반 메소드

### Instance Method

- 파라미터로 <span style="color:#DD2525">self (인스턴스)</span>를 받음
    
    ```python
    class Car():
        def __init__(self, company, details):
            self._company = company
            self._details = details
        
        # Instance Method
        # 인자로 self를 받음 (self : 객체의 고유한 속성 값 사용)
        def get_company_name(self):
            return self._company
    
  
    car1 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})
    car2 = Car('Audi', {'color' : 'Silver', 'horsepower': 300, 'price': 6000})
    
  
    # Instance Method 사용
    # 아래와 같이 인스턴스에서 사용 가능
    >>> car1.get_company_name()
    'Bmw'
    
  
    # 또는 인자로 인스턴스를 넘겨주어도 됨
    >>> Car.get_company_name(car2)
    'Audi'
    ```
    

### Class Method

- 파라미터로 <span style="color:#0067a3">cls (클래스)</span>를 받음
  
- 사용시 <span style="color:#0067a3">@classmethod</span> 데코레이터가 필요
  
- class 변수를 read/write 하는 경우에 주로 사용
    
    ```python
    class Car(object):
        # Class Variable (모든 인스턴스가 공유)
        price_per_raise = 1.0
    
        def __init__(self, company, details):
            self._company = company
            self._details = details
    
        # Class Method
        # 인자로 cls를 받음
        # @classmethod 데코레이터 필요
        @classmethod
        def raise_price(cls, per):
            if per <= 1:
                print('Please Enter 1 or More')
                return
            cls.price_per_raise = per
            return 'Succeed! price increased.'
    
  
    # Class Method
    # 아래와 같이 클래스에서 사용 가능
    >>> Car.raise_price(1.5)
    'Succeed! price increased.'
    
  
    >>> Car.price_per_raise
    1.5
    
    # 위와 같이 주로 class 변수를 read/write 할 때 사용함
    ```
    

### Static Method

- 파라미터로 self나 cls <span style="color:#1E821E">인자가 필요하지 않음</span>
  
- 사용시 <span style="color:#1E821E">@staticmethod</span> 데코레이터가 필요
    
    ```python
    class Car(object):
        price_per_raise = 1.0
  
        def __init__(self, company, details):
            self._company = company
            self._details = details
        
        # Static Method
        # 인자로 self, cls를 받지 않음
        # @staticmethod 데코레이터 필요
        @staticmethod
        def is_bmw(inst):
            if inst._company == 'Bmw':
                return 'OK! This car is {}.'.format(inst._company)
            return 'Sorry. This car is not Bmw.'
    
  
    car1 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})
    car2 = Car('Audi', {'color' : 'Silver', 'horsepower': 300, 'price': 6000})
    
  
    # Static Method
    # 아래와 같이 class에서도, 인스턴스에서도 사용 가능
    >>> car1.is_bmw(car1)
    'OK! This car is Bmw.'
    
  
    >>> car2.is_bmw(car2)
    'Sorry. This car is not Bmw.'
    
  
    >>> Car.is_bmw(car2)
    'Sorry. This car is not Bmw.'
    ```
    
    ```python
    # Static Method를 사용하지 않은 경우
    
  
    # 아래와 같은 함수를 따로 정의한 경우
    def is_bmw(inst):
        if inst._company == 'Bmw':
            return 'OK! This car is {}.'.format(inst._company)
        return 'Sorry. This car is not Bmw.'
    
  
    # static method 미사용
    >>> is_bmw(car1)
    'OK! This car is Bmw.'
    
  
    >>> is_bmw(car2)
    'Sorry. This car is not Bmw.'
    
  
    # static method 사용
    >>> Car.is_bmw(car1)
    'OK! This car is Bmw.'
    
  
    >>> Car.is_bmw(car2)
    'Sorry. This car is not Bmw.'
    
  
    정적 메소드는 기능상으로는 클래스 밖에서 정의하더라도 차이가 없음
    다만 클래스와 연관된 함수이므로 클래스 내에 정의
    ```
    
    Static Method
    
    - class, instance에서 둘 다 사용 가능함
    - 유연하게 사용할 수 있음
<br><br>
      
## 캡슐화

```python
class Car(object):
    def __init__(self, company, details):
        self._company = company
        self._details = details

    def get_price(self):
        return self._details.get('price')

# 자동차 인스턴스    
car1 = Car('Bmw', {'color' : 'Black', 'horsepower': 270, 'price': 5000})


# 자동차의 가격을 알고 싶을 때
# 인스턴스 변수에 직접 접근하여 가격 정보 가져오기
>>> car1._details.get('price')
5000


인스턴스 변수에 직접 접근하는 것은 좋지 않은 방법
    → 캡슐화 되어야하기에 좋지 않은 방법
    → 이러한 경우는 method를 만들어서 필요한 정보만 반환하도록 해야함


# method를 만들어서 필요한 정보만 반환하도록
>>> car1.get_price()
5000
```

<br>