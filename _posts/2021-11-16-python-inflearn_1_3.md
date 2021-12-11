---
layout: article
title: 우리를 위한 프로그래밍 | Ch 3. 파이썬 데이터 모델
tags: Python
aside:
  toc: true
---

[우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)을 듣고 정리한 내용

<!--more-->

---

# Chapter 03 - 01

## Special Method
- **Magic Method**
- 클래스 안에 정의할 수 있는 특별한 메소드 (built-in method)
- double underscore(dunder - 던더)로 시작

```python
# 참조 : https://docs.python.org/3/reference/datamodel.html#special-method-names
# 파이썬의 핵심 → 시퀀스(Sequence), 반복(Iterator), 함수(Functions), 클래스(Class)

# 기본형
>>> int
`<class 'int'>`

# 위와 같이 int도 class로 이루어져 있음


# 모든 속성 및 메소드 출력
>>> dir(int))
['__abs__', '__add__', '__and__', '__bool__', '__ceil__','__class__', '__delattr__', ... ,'bit_length',
 'conjugate', 'denominator', 'from_bytes', 'imag', 'numerator', 'real', 'to_bytes']


# 사용
# 아래의 둘은 같음 : + 는 내부적으로 __add__가 호출되도록 함
>>> 10 + 100
110


>>> (10).__add__(100)
110
# 위에서 (10) 과 같이 괄호로 감싸져 있어야 함 → 소수와 구별하기 위해서


>>> n = 10
>>> n.__add__(100)
110
# 위와 같이 n에 대입한 경우 괄호 쓰지 않아도 됨
```

```python
# 그 외의 다른 예시
# bool -> 내부적으로 __bool__이 호출됨
>>> bool(n)
True


>>> n.__bool__()
True


# * -> 내부적으로 __mul__이 호출됨
>>> n * 100
1000


>>> n.__mul__(100)
1000
```
<br>

## Magic Method 예제 1

```python
class Fruit:
    def __init__(self, name, price):
        self._name = name
        self._price = price

        
s1 = Fruit('Orange', 7500)
s2 = Fruit('Banana', 3000)


# s1과 s2의 가격의 합을 계산하기 위해서 아래와 같이 사용함 → 좋은 방법이 아님
>>> s1._price + s2._price
10500


# ✅ 매직 메소드를 이용하기
# magic method를 재정의하여 새로운 계산을 만들 수 있음
class Fruit:
    def __init__(self, name, price):
        self._name = name
        self._price = price
		
    def __add__(self, x):
        print('Called >> __add__ Method.')
        return self._price + x._price
		
    def __sub__(self, x):
        print('Called >> __sub__ Method.')
        return self._price - x._price

    def __ge__(self, x):
        print('Called >> __ge__ Method.')
        if self._price >= x._price:
            return True
        else:
            return False
		
    def __le__(self, x):
        print('Called >> __le__ Method.')
        if self._price <= x._price:
            return True
        else:
            return False

        
s1 = Fruit('Orange', 7500)
s2 = Fruit('Banana', 3000)


# 매직메소드 출력
# + 는 내부적으로 __add__를 호출함
>>> s1+s2
Called >> __add__ Method.
10500


# - 는 내부적으로 __sub__를 호출함
>>> s1 - s2
Called >> __sub__ Method.
4500


>>> s2 - s1
Called >> __sub__ Method.
-4500


# >= 는 내부적으로 __ge__를 호출함
>>> s1 >= s2
Called >> __ge__ Method.
True


# <= 는 내부적으로 __le__를 호출함
>>> s1 <= s2
Called >*>* __le__ Method.
False
```

---

# Chapter 03-02

## Magic Method 예제 2

```python
class Vector(object):
    # arg -> unpacking
    def __init__(self, *args):
        '''Create a vector, example : v = Vector(5,10)'''
        if len(args) == 0:
            self._x, self._y = 0, 0
        else:
            self._x, self._y = args

    def __repr__(self):
        '''Returns the vector infomations'''
        return 'Vector(%r, %r)' % (self._x, self._y)

    def __add__(self, other):
        '''Returns the vector addition of self and other'''
        return Vector(self._x + other._x, self._y + other._y)
    
    def __mul__(self, y):
        return Vector(self._x * y, self._y * y)

    # bool(Vector(0,0)) -> False return 되도록
    def __bool__(self):
        return bool(max(self._x, self._y))
        # max(0,0) -> 0 bool(0)이면 False 이므로

    
# Vector 인스턴스 생성
v1 = Vector(5,7)
v2 = Vector(23, 35)
v3 = Vector()


# 매직메소드 doc
# 메소드에도 doc 정의해두면 __doc__메서드로 출력 가능함
>>> Vector.__init__.__doc__
'Create a vector, example : v = Vector(5,10)'


>>> Vector.__repr__.__doc__
'Returns the vector infomations'


>>> Vector.__add__.__doc__
'Returns the vector addition of self and other'



# + 는 내부적으로 __add__를 호출
# 새롭게 정의된 __add__를 실행함
>>> v1+v2
Vector(28, 42)



# * 는 내부적으로 __mul__를 호출
# 새롭게 정의된 __mul__을 실행함
>>> v1 * 3
Vector(15, 21)


>>> v2*10
Vector(230, 350)



# bool은 새롭게 정의된 __bool__을 실행
>>> bool(v1)
True

>>> bool(v3)
False
```

# Chapter 03-03

## NamedTuple

- index를 통해서만 값 (value)에 접근 가능한 기본 Tuple과는 다르게 **키 (key)값으로 접근 가능한 Tuple**
- dict의 성질을 가지고 있음 (key로 접근 가능함)
- key와 index로 요소에 접근 가능

#### Tuple 사용 예시
```python
# 객체 → 파이썬의 데이터를 추상화
# 모든 객체는 객체의 고유값(id)를 가지고 있음


# 일반적인 튜플 사용 예시
pt1 = (1.0, 5.0)
pt2 = (2.5, 1.5)

# 위와 같이 사용하는 경우 pt1, pt2가 무슨 의미인지 파악할 수 없음
# pt1, pt2를 구성하고 있는 요소가 무엇을 의미하는지 알 수 없음
# 점을 추가할 때 어떤 값을 어떻게 입력해야하는지 알기 어려움


from math import sqrt

# 두 점 사이의 거리 구하는 공식
l_leng1 = sqrt((pt2[0] - pt1[0]) ** 2 + (pt2[1] - pt1[1]) ** 2)


>>> l_leng1
3.8078865529319543
```

#### NamedTuple 사용 예시

```python
# Namedtuple → class처럼 튜플을 선언하여 객체화

# 네임드 튜플 사용 예시
from collections import namedtuple


# 네임드 튜플 선언
# namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)


# typename → Point인 namedtuple을 정의
# field names인 x, y는 띄어쓰기로 구분
point = namedtuple('Point', 'x y')


# 두 점 선언
pt3 = Point(1.0, 5.0)
pt4 = Point(2.5, 1.5)


# 계산
# key로 접근
l_leng2 = sqrt((pt4.x - pt3.x) ** 2 + (pt4.y - pt3.y) ** 2)

# index로 접근
l_leng2 = sqrt((pt4[0] - pt3[0]) ** 2 + (pt4[1] - pt3[1]) ** 2)

# 위와 같이 인덱스로도 접근이 가능하지만 .x .y와 같이 키로 표현하는 가독성이 더 좋음


# 출력
>>> l_leng2
3.8078865529319543
```
<br>

### NamedTuple 선언방법

```python
# 네임드 튜플 선언 방법
# 이 때 내부의 내용은 str으로 정의되어야 함


# field_names → list로 구분
point1 = namedtuple('Point', ['x', 'y'])


# field_names → 콤마로 구분
point2 = namedtuple('Point', 'x, y')


# field_names → 띄어쓰기로 구분
point3 = namedtuple('Point', 'x y')


# 같은 key가 중복되거나 예약어를 사용하는 경우 rename=True 옵션을 사용
point4 = namedtuple('Point', 'x y x class', rename=True)
# 위에서 class → 예약어 / x → 중복된 key
# rename → True 옵션 (default false)


# 만약 rename을 사용하지 않으면 아래와 같이 에러 발생
>>> point4 = namedtuple('Point', 'x y x class')
ValueError('Type names and field names cannot be a '
ValueError: Type names and field names cannot be a keyword: 'class'



# 객체 생성 예시
# 아래와 같이 keyword argument, positional argument로 인자 전달
p1 = point1(x=10, y=35)
p2 = point2(20, 40)
p3 = point3(45, y=20)


>>> p1
Point(x=10, y=35)


>>> p2
Point(x=20, y=40)


>>> p3
Point(x=45, y=20)



# rename test
p4 = point4(10, 20, 30, 40)


>>> p4
Point(x=10, y=20, _2=30, _3=40)
#_2, _3이라는 난수로 스스로 만들었음 (rename True이므로)


# 사용 예시
# Index 사용 → Index Error 주의
>>> p1[0] + p2[1]
50


# Key 사용
# Index 사용보다는 key로 접근하는게 더 좋음 → 가독성이 더 좋음 / Index Error 발생 X
# 클래스 변수 접근 방식처럼 key로 접근
>>> p1.x + p2.y
50
```

<br>
### Unpacking

```python
# Dict to Unpacking
temp_dict = {'x': 75, 'y': 55}
p5 = point1(**temp_dict)


>>> p5
Point(x=75, y=55)



# Tuple to Unpacking
temp_tuple = (55, 75)
p6 = point1(*temp_tuple)


>>> p6
Point(x=55, y=75)



# Named Tuple Unpacking
x, y = p3

>>> x
45

>>> y
20
```

<br>
### Defaults값 지정

```python
# default값 지정
# defaults 인자에 값 주기
point5 = namedtuple('Point', 'x y z', defaults=(1,2,3))


# 아래와 같이 인자를 주지않으면 defaults 값으로 설정됨
p7 = point5()


>>> p7
Point(x=1, y=2, z=3)


# 인자를 준 값은 전달된 인자로, 전달받지 못한 인자는 defaults 값으로 지정됨
p8 = point5(4,5)


>>> p8
Point(x=4, y=5, z=3)
```

<br>
### NamedTuple Method

```python
# 네임드 튜플 메소드


# _make() : 새로운 객체 생성
temp = [52, 38]
p9 = point1._make(temp)


>>> p9
Point(x=52, y=38)



# _fields : 필드 네임 확인
>>> p1._fields
('x', 'y')


>>> p4._fields
('x', 'y', '_2', '_3')


>>> p8._fields
('x', 'y', 'z')



# _asdict() : OrderedDict 반환
# named tuple을 dict 형태로 변환
>>> p1._asdict()
{'x': 10, 'y': 35}


>>> p4._asdict()
{'x': 10, 'y': 20, '_2': 30, '_3': 40}
```

<br>

## NamedTuple vs DataClass

### DataClass

```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int = 10
# 위의 age: int = 10 과 같은 방식으로 default값 지정 가능


person = Person("Ben", 34)


>>> person
Person(name='Ben', age=34)


>>> person.age
34


# dataclass → mutable
# dataclass → key로 접근 가능
person.age = 50


# mutable하기에 위와 같은 방법으로 값을 변경할 수 있음
>>> person
Person(name='Ben', age=50)


# dafault 예시
person2 = Person("Dana")


>>> person2
Person(name='Dana', age=10)

```

<br>
### Immutable DataClass

```python
# frozen 옵션을 이용하여 dataclass를 immutable하게 만들 수 있음

from dataclasses import dataclass

# frozen=True를 이용하여 immutable한 dataclass를 만들 수 있음
@dataclass(frozen=True)
class Person:
    name: str
    age: int = 10

    
person = Person("Ben", 34)


>>> person
Person(name='Ben', age=34)


>>> person.age
34



# immutable한 dataclass이므로 값 변경 불가능 (에러 발생)
>>> person.age = 50
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "<string>", line 3, in __setattr__
dataclasses.FrozenInstanceError: cannot assign to field 'age'
```

<br>
### NamedTuple

```python
# NamedTuple -> immutable

from collections import namedtuple

# namedtuple 정의
Person = namedtuple("Person", ["name", "age"])

person = Person("Ben", 30)

>>> person
Person(name='Ben', age=30)



# index로 접근 가능
>>> person[0]
'Ben'



# key로 접근 가능
>>> person.name
'Ben'



# namedtuple은 immutabled -> 값 변경 불가능 (에러 발생)
>>> person.name = "Dana"
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: can't set attribute
```

<br>
### DataClass vs NamedTuple vs Object

![image](https://user-images.githubusercontent.com/60612551/145665932-f2895f03-1466-484b-8840-878b9de4082f.png)

---

#### Reference

[DataClass vs NamedTuple](http://www.legendu.net/misc/blog/dataclass-vs-namedtuple-in-python/) <br>
[DataClass vs NamedTuple vs Object image](https://www.geeksforgeeks.org/difference-between-dataclass-vs-namedtuple-vs-object-in-python/)

<br>