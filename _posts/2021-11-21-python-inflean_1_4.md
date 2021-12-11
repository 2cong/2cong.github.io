---
layout: article
title: 우리를 위한 프로그래밍 | Ch 4. 파이썬 시퀀스
tags: Python
aside:
  toc: true
---

[우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)을 듣고 정리한 내용

<!--more-->

---

# Chapter 04 - 01

## Container

- 여러 item을 가질 수 있는 type

### Container 분류
![image](https://user-images.githubusercontent.com/60612551/145666720-1e8a7aeb-b0ab-4d41-90a5-a868748fadaa.png)

- <span style="color:#DD2525">Sequences</span> : 순서가 있는 자료형
    - built-in sequence types : list, str, tuple, bytes
    - 모든 Sequence객체는 Container객체
    - **__len__()**과 **__getitem__()** 메소드를 가지고 있음
- Set types : 순서와 중복이 없는 자료형
- Mappings : key와 value가 있는 자료형 key를 통해 value를 찾아냄
    - dict → 순서 없음 / OrderedDict → 순서 있음
    - dict
        - **__len__()**과 **__getitem__()** 를 가지고 있으나 key를 이용하여 value를 찾는다는 점에서 sequence 보다는 mapping으로 고려됨

### Container vs Flat

- Container
    - 서로 다른 자료형을 담을 수 있음
    - list, tuple, collections.deque
  
- Flat
    - 단일 자료형만 담을 수 있음
    - str, bytes, bytearray, array.array, memoryview

```python
# Container : 서로 다른 자료형[list, tuple, collections.deque]
# Flat : 한 개의 자료형[str, bytes, bytearray, array.array, memoryview]


test = [1, 1.0, 'abc']


# Container의 경우 다른 자료형을 가질 수 있음
>>> test
[1, 1.0, 'abc']


# Flat의 경우 한 개의 자료형만 가질 수 있음
import array


type = 'f' # float 32


# 정의한 타입과 다른 타입이 들어와서 에러 발생
>>> array.array(type, test)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be real number, not str
```

<br>

## Immutable vs Mutable

- Immutable
    - 불변형
    - 선언 후 변경 불가능
    - tuple, str, bytes
- Mutable
    - 가변형
    - 선언 후 변경 가능
    - list, bytearray, array.array, memoryview, deque

```python
# 가변(list, bytearray, array.array, memoryview, deque) vs 불변(tuple, str, bytes)
# 리스트 및 튜플 고급


# Immutable Type
# str type -> immutable이므로 변경 불가
chars = '+_)(*&^%$#@!~)'
>>> chars[2]
')'


# 변경하려고 할 때 error 발생
>>> chars[2] = 'k'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment


# tuple type -> immutable이므로 변경 불가
test_tuple = (1,2,3,4)


>>> test_tuple[1]
2


# 변경하려고 할 때 error 발생
>>> test_tuple[1] = 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment



# Mutable Type
# list type -> mutable이므로 변경 가능
test_list = [1,2,3,4]


>>> test_list[1]
2


# mutable type의 경우 잘 변경됨
test_list[1] = 5


>>> test_list
[1, 5, 3, 4]
```

<br>

## List Comprehension

```python
# 지능형 리스트(Comprehending Lists)


chars = '+_)(*&^%$#@!~)'


# Non Comprehending Lists
code_list1 = []

for s in chars:
     code_list1.append(ord(s))

     
>>> code_list1
[43, 95, 41, 40, 42, 38, 94, 37, 36, 35, 64, 33, 126, 41]



# Comprehending Lists
code_list2 = [ord(s) for s in chars]


>>> code_list2
[43, 95, 41, 40, 42, 38, 94, 37, 36, 35, 64, 33, 126, 41]



# Comprehending Lists + Map, Filter
# code_list3 방식이 속도 약간 우세
code_list3 = [ord(s) for s in chars if ord(s) > 40]
code_list4 = list(filter(lambda x : x > 40, map(ord, chars)))


>>> code_list3
[43, 95, 41, 42, 94, 64, 126, 41]


>>> code_list4
[43, 95, 41, 42, 94, 64, 126, 41]
```

- ord () 와 chr ()
    
    ```python
    ord(c) : 문자의 유니코드 값을 돌려주는 함수
    # c -> a character
    
    chr(i) : 유니코드 값을 입력받아 그 코드에 해당하는 문자를 출력하는 함수
    # i -> integer
    
    >>> ord('a')
    97
    
    >>> chr(97)
    'a' 
    ```
    

- map() 함수
    
    ```python
    # map 함수
    map(function, iterable) : iterable의 원소를 하나씩 꺼내서 인자로 받은 함수 적용하는 함수
    
  
    # map 객체 반환
    >>> map(float, [1,2,3,4,5])
    <map object at 0x7f912113aa00>
    
  
    # map 객체에 for문을 이용하여 list로 생성
    >>> [i for i in map(float, [1,2,3,4,5])]
    [1.0, 2.0, 3.0, 4.0, 5.0]
    
  
    # list 함수 이용하여 list로 생성
    >>> list(map(float, [1,2,3,4,5]))
    [1.0, 2.0, 3.0, 4.0, 5.0]
    ```
    

- filter() 함수
    
    ```python
    # filter 함수
    # equivalent to the generator expression (item for item in iterable if function(item))
    filter(function, iterable) : iterable의 원소를 하나씩 꺼내서 인자로 받은 함수 적용 -> True 인 것만 반환
    
  
    # filter 객체 반환
    >>> filter(lambda x: x > 40, [39, 40, 41, 42])
    <filter object at 0x7f8ef0a3a970>
    
  
    # filter 객체에 for문을 이용하여 list로 생성
    >>> [i for i in filter(lambda x: x > 40, [39, 40, 41, 42])]
    [41, 42]
    
  
    # list 함수 이용하여 list로 생성
    >>> list(filter(lambda x: x > 40, [39, 40, 41, 42]))
    [41, 42]
    ```
    

## Iterator

- Iterable(순환가능)
    - **for 구문**을 통해 **반복적으로** 하나씩 데이터를 꺼내올 수 있는 객체
    - **__iter__()**을 구현하고 있는 객체
    - list, dict, set, bytes, tuple, range
- Iterator
    - next() 함수를 구현한 객체를 말함
    - next() 함수 : 순환 가능한 객체의 다음 원소 반환
- iter():
    - iterable형 객체를 iterator 객체로 만드는 파이썬 내장함수
        
    ![image](https://user-images.githubusercontent.com/60612551/145667195-d05b67bc-77f5-4931-9473-1ef825810c80.png)
        

### iterable한 객체
```python
# 아래의 예시에서 x는 iterable 객체
# y는 iterator 객체
x = [1, 2, 3]
y = iter(x)


>>> type(x)
`<class 'list'>`


>>> type(y)
`<class 'list_iterator'>`


# iter() 함수를 이용하여 iterable형 객체를 iterator로 만들 수 있음
# iterator는 아래와 같이 next()함수를 가지고 있음
>>> next(y)
1

>>> next(y)
2

>>> next(y)
3
```

### iterable하지 않은 객체
```python
# iterable하지 않은 객체는 __iter__()을 구현하고 있지 않기 때문에 iter()함수 사용 불가능
# 따라서 iterable하지 않은 객체는 iter()함수를 사용하여 iterator 객체로 바꿀 수 없음


z = 12345


# int는 iterable하지 않음
# iter() 함수 사용시 에러 발생
>>> k = iter(z)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not iterable 
```

### iterable & sequence

- iterable
    - 내부적으로 매직 메소드 **\_\_iter\_\_()** 을 구현하고 있음
- sequence
    - 내부적으로 매직 메소드 **\_\_len_\_()**과 **\_\_getitem_\_()**을 구현하고 있음
    
    ```python
    # python list
    >>> a=[1,2,3,4,5]
    
  
    # list는 __iter__()이 구현되어 있음
    >>> a.__iter__()
    <list_iterator object at 0x7f91206609a0>
    
  
    # list는 __getitem__()이 구현되어 있음
    >>> a.__getitem__(3)
    4
    
  
    # list는 __len()__이 구현되어 있음
    >>> a.__len__()
    5
    
  
    # 위와 같이 list는 __iter__(), __getitem__(), __len()__ 이 구현되어 있음
    # ⭐️ 따라서 list는 iterable이자 sequence
    ```
    
<br>

## Generator

- 함수안에 **yield 키워드**를 사용
- <span style="color:#0067a3">**iterator를 생성해주는 함수**</span>
- 모든 generator는 iterator
- [함수 내부에서 사용된 데이터(local 변수 등)들이 메모리에 그대로 유지됨](https://bluese05.tistory.com/56)
    - 일반적인 함수
        - 함수 사용이 종료되면 결과 값을 호출부로 반환
        - 반환 후 함수 종료 → 메모리 상에서 클리어
    - generator
        - generator 함수가 실행 중 yield를 만나면 해당 함수는 그 상태로 정지됨
        - next() 호출자에게 반환 값을 전달
        - 함수는 종료되는 것이 아니라 **그 상태로 유지됨 (함수 내부에서 사용된 데이터들이 메모리에 유지됨)**
        
        ```python
        # 일반 함수
        def common_function(n):
            i = 0
            while i < n:
                print(f'{i} 전')
                print(i)
                print(f'{i} 후')
                i += 1
        
      
        # 한번에 실행       
        >>> common_function(5)
        0 전
        0
        0 후
        1 전
        1
        1 후
        2 전
        2
        2 후
        3 전
        3
        3 후
        4 전
        4
        4 후
        ```
        
        ```python
        # generator
        def test_generator(n):
            i = 0
            while i < n:
                print(f'{i} 전')
                yield i
                print(f'{i} 후')
                i += 1
        
      
        a = test_generator(5)
        
      
        >>>a
        <generator object test_generator at 0x7fee34616270>
        
      
        # next()를 이용하여 다음 값 호출
        # yield 구문 이후부터 실행하지 않고 멈춤
        >>>next(a)
        0 전
        0
        
      
        # 다시 한번 next()를 호출하면 yield 구문 이후부터 실행
        # yield 구문 전까지 실행
        >>> next(a)
        0 후
        1 전
        1
        
      
        >>> next(a)
        1 후
        2 전
        2
        
      
        >>> next(a)
        2 후
        3 전
        3
        ```
  

![image](https://user-images.githubusercontent.com/60612551/145667523-c19e386b-50bc-4698-91ed-8d2abee403c4.png)

### Generator 생성 방법

```python
# Generator 생성 
# Generator : 한 번에 한 개의 항목을 생성 (메모리 유지X)


# Generator Expression(Generator Comprehension으로도 불림)을 이용하여 생성
test_g_1 = (i for i in range(0,10))


>>> test_g_1
<generator object <genexpr> at 0x7fee346169e0>


>>> next(test_g_1)
0


>>> next(test_g_1)
1


>>> next(test_g_1)
2


# yield를 이용하여 생성
def test_g(n):
    i = 0
    while i < n:
        yield i 
        i += 1

        
test_g_2 = test_g(10)


>>> next(test_g_2)
0


>>> next(test_g_2)
1


>>> next(test_g_2)
2
```

### generator 예제

```python
# array (flat, immutable)
import array

chars = 'fdkdhakfldj'


# generator 생성
test_g = (ord(s) for s in chars)


# Array로 생성
array_g = array.array('I',  (ord(s) for s in chars))


>>> type(test_g)
`<class 'generator'>`


# generator 다음 값 호출
>>> next(test_g)
102

>>> next(test_g)
100


# array의 경우
type(array_g)
`<class 'array.array'>`


# array는 iterator가 아니므로 next()호출 X
>>> next(array_g)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: 'array.array' object is not an iterator


# array를 generator로 변환하기
converted_g = (i for i in array_g)


>>> type(converted_g)
`<class 'generator'>`
```

```python
# generator 반환
test_g_2 = ('%s' % c + str(n) for c in ['A', 'B', 'C', 'D'] for n in range(1,11))

>>> test_g_2
<generator object <genexpr> at 0x7fee34616a50>


# next()를 이용하여 다음 값 가져오기
>>> next(test_g_2)
'A1'


>>> next(test_g_2)
'A2'


>>> next(test_g_2)
'A3'


# for문을 이용하여 다음 값 가져오기
>>> for i in test_g_2:
        print(i)
    
A4
A5
A6
A7
...
D8
D9
D10

# A4부터 print된 이유 : next()로 A1 ~ A3 까지 불러왔으므로 그 이후인 A4부터 시작됨
```

<br>

## yield from

iterable한 객체에서 yield 할 경우에 사용

```python
# for문을 이용하여 generator 사용
def test_generator(a):
    for i in a:
        yield i
        
        
tg = test_generator([1,2,3])



>>> next(tg)
1

>>> next(tg)
2

>>> next(tg)
3



# yield from을 사용하여 generator 생성
# iterable한 객체를 yield할 때 사용할 수 있음
def test_generator_2(a):
    yield from a

    
tg = test_generator_2([1,2,3])


>>> next(tg)
1

>>> next(tg)
2

>>> next(tg)
3
```

<br>

## 얕은 복사 vs 깊은 복사

```python
# 전부 다른 id 값을 생성
marks1 = [['~'] * 5 for _ in range(5)]


# 하나의 주소값이 5개 복사됨
marks2 = [['~'] * 5] * 5


# 보기에는 같아보이나 내부 id 구성이 다름
>>> marks1
[['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~']]


>>> marks2
[['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~']]



# 위의 결과 값은 다름
# marks1은 전부 다른 id로 생성됨
>>> [id(i) for i in marks1]
[140661057858432, 140661057855616, 140661057879616, 140661051114496, 140661057878208]


# marks2는 1개의 주소값이 복사됨
>>> [id(i) for i in marks2]
[140661057858816, 140661057858816, 140661057858816, 140661057858816, 140661057858816]



# 값을 수정하는 경우
marks1[0][1] = 'X'
marks2[0][1] = 'X'


# marks2는 하나의 주소값을 사용하기 때문에 전부 다 변경됨
>>> marks1
[['~', 'X', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~'], ['~', '~', '~', '~', '~']]


>>> marks2
[['~', 'X', '~', '~', '~'], ['~', 'X', '~', '~', '~'], ['~', 'X', '~', '~', '~'], ['~', 'X', '~', '~', '~'], ['~', 'X', '~', '~', '~']]
```

- 얕은 복사 (shallow copy)
    
    복사 후 동일한 id 값을 가지고 있음 
    
    ```python
    # 얕은 복사
    test_list = [1,2,3,4,5]
    copy_list = test_list
    
    >>> copy_list
    [1, 2, 3, 4, 5]
    
  
    >>> id(test_list)
    140661057953024
    
  
    >>> id(copy_list)
    140661057953024
    
  
    # test_list와 copy_list의 id값은 같음
    >>> test_list is copy_list
    True
    
  
    # id 값이 같기에 한 개의 list를 변경해도 두 list의 값은 같음
    test_list[2] = 100
    
    >>> test_list
    [1, 2, 100, 4, 5]
    
  
    >>> copy_list
    [1, 2, 100, 4, 5]
    ```
    

- 깊은 복사 (deep copy)
    
    복사 후 다른 id를 가짐
    
    ```python
    # 깊은 복사
    import copy
    
    test_list = [1,2,3,4,5]
    copy_list = copy.deepcopy(test_list)
    
    >>> copy_list
    [1, 2, 3, 4, 5]
    
  
    >>> id(test_list)
    140661057899456
    
  
    >>> id(copy_list)
    140661057760192
    
  
    >>> copy_list == test_list
    True
    
  
    # test_list와 copy_list의 id값은 다름
    >>> copy_list is test_list
    False
    
    test_list[2] = 100
    
    >>> test_list
    [1, 2, 100, 4, 5]
    
  
    # id 값이 다르기에 원본 list의 값을 변경해도 사본 list의 값이 변경되지 않음
    >>> copy_list
    [1, 2, 3, 4, 5]
    ```

---    

# Chapter 04 - 02

## Tuple Advanced

```python
# Tuple Advanced
# Unpacking


>>> divmod(100, 9)
(11, 1)


>>> divmod(*(100, 9))
(11, 1)


>>> print(*(divmod(100, 9)))
11 1



# 변수에 할당되는 값 확인
# 예시 1
x, y, *rest = range(10)


>>> x, y, rest
0 1 [2, 3, 4, 5, 6, 7, 8, 9]



# 예시 2
x, y, *rest = range(2)


>>> x, y, rest
0 1 []



# 예시 3
x, y, *rest = 1, 2, 3, 4, 5


>>> x, y, rest
1 2 [3, 4, 5]
```

<br>

## id 재할당 : Mutable vs Immutable

```python
# Mutable(가변) vs Immutable(불변)


# immutable 
l = (15, 20, 25)


# mutable
m = [15, 20, 25]


>>> id(l)
140661057878464


>>> id(m)
140661057867968



# *= 를 사용하는 경우
# 불변형은 불변이기에 id 재할당 됨
# 가변형은 가변이기에 기존의 id 계속 사용
l *= 2
m *= 2


>>> id(l)
140661057808032


>>> id(m)
140661057867968



# * 를 사용하는 경우
# 아래의 경우는 전부 id 값이 다름
# 곱한 값을 새로 재할당한 것이기 때문!
l = l*2
m = m*2


>>> id(l)
140661057973408


>>> id(m)
140661057886400

# *는 새로운 객체를 만들고 *=는 기존 객체를 수정
```

<br>

## sort vs sorted

```python
# sort vs sorted 
# reverse, key=func

# sorted : 정렬 후 새로운 객체 반환 -> 원본 수정 x
f_list = ['orange', 'apple', 'mango', 'papaya', 'lemon', 'strawberry', 'coconut']


sorted(f_list)
>>> ['apple', 'coconut', 'lemon', 'mango', 'orange', 'papaya', 'strawberry']


>>> sorted(f_list, reverse=True)
['strawberry', 'papaya', 'orange', 'mango', 'lemon', 'coconut', 'apple']


# key를 주면 정렬 방식 지정 가능 -> 함수를 key로 지정할 수 있음
>>> sorted(f_list, key=len)
['apple', 'mango', 'lemon', 'orange', 'papaya', 'coconut', 'strawberry']


>>> sorted(f_list, key=lambda x: x[-1])
['papaya', 'orange', 'apple', 'lemon', 'mango', 'coconut', 'strawberry']


>>> sorted(f_list, key=lambda x: x[-1], reverse=True)
['strawberry', 'coconut', 'mango', 'lemon', 'orange', 'apple', 'papaya']


# 원본은 수정되지 않음
f_list
['orange', 'apple', 'mango', 'papaya', 'lemon', 'strawberry', 'coconut']



# sort : 정렬 후 객체 직접 변경 -> 원본 수정
# sort는 원본 수정이므로 반환값 X
# 반환 값 확인(None)

# sort는 반환값 없음! 
>>> f_list.sort()


# 원본이 수정됨
>>> f_list
['apple', 'coconut', 'lemon', 'mango', 'orange', 'papaya', 'strawberry']


# sort도 reverse, key 옵션 사용 가능
>>> f_list.sort(key=lambda x: x[-1], reverse=True)


>>> f_list
['strawberry', 'coconut', 'mango', 'lemon', 'apple', 'orange', 'papaya']
```

<br>

## List vs Array

- List
    - 융통성
    - 컨테이너이므로 다양한 자료형 사용 가능
    - 범용적 사용
- Array
    - list와 거의 호환됨
    - 숫자일 때는 array 사용하는 것이 더 좋음 (ex : nd.array)

→ 여기서의 list, array는 python의 list, array를 의미함

---

# Chapter 04 - 03

## 해시테이블 (HashTable)

- Key에 Value를 저장하는 구조
    - key 값의 연산 결과에 따라 직접 접근이 가능한 구조
    - key 값에 해시함수 적용 → 고유한 index 생성 → key에 대한 value 위치를 알 수 있음
- 적은 리소스로 많은 데이터를 효율적으로 관리할 수 있음
- python에서는 dict가 hash table의 예시

### hash()

- 객체의 hash값을 return
- hash value → int
- immutable한 객체에만 hash 함수 사용할 수 있음

```python
# Hash 값 확인
t1 = (10, 20, (30, 40, 50))
t2 = (10, 20, [30, 40, 50])


# hash 함수를 사용하려면 immutable해야함
>>> hash(t1)
465510690262297113


# 리스트는 mutable이라서 hash 함수 사용 불가능 -> 에러 발생
>>> hash(t2)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: unhashable type: 'list'
```

<br>

## Dict 심화 1

### setdefault

- d.setdefault(key, value)
    
    → 여기서 d는 dictionary 객체
    
- 인자로 받은 키가 해당 dictionary에 있을 때는 **그 키의 값을 반환**
- 인자로 받은 키가 해당 dictionary에 없을 때는 dict에 key와 value를 저장하고 **value를 return**

```python
# Dict -> Key 중복 허용 X

# Dict Setdefault 예제
# tuple에서 dict 만들 때 권장됨
source = (('k1', 'val1'),
            ('k1', 'val2'),
            ('k2', 'val3'),
            ('k2', 'val4'),
            ('k2', 'val5'))


new_dict1 = {}
new_dict2 = {}



# No use setdefault
for k, v in source:
    if k in new_dict1:
        new_dict1[k].append(v)
    else:
        new_dict1[k] = [v]

        
>>> new_dict1
{'k1': ['val1', 'val2'], 'k2': ['val3', 'val4', 'val5']}



# Use setdefault -> 더 빠름
for k, v in source:
    new_dict2.setdefault(k, []).append(v)

    
>>> new_dict2
{'k1': ['val1', 'val2'], 'k2': ['val3', 'val4', 'val5']}



# dict comprehesion
# key 중복되면 나중 값으로 덮어씌워짐
new_dict3 = {k : v for k , v in source}


>>> new_dict3
{'k1': 'val2', 'k2': 'val5'}
```

### **defaultdict**

- default 값을 갖는 dictionary
- defaultdict()는 인자로 주어진 객체의 기본값을 dict의 초기값으로 지정함

```python
from collections import defaultdict

# 기본값을 list로 지정
d = defaultdict(list)


for k,v  in source:
    d[k].append(v)

    
>>> d
defaultdict(`<class 'list'>`, {'k1': ['val1', 'val2'], 'k2': ['val3', 'val4', 'val5']})


>>> d.keys()
dict_keys(['k1', 'k2'])


# d에는 k3라는 인자가 없기에 기본 값인 []가 추가됨
d['k3']

>>> d
defaultdict(`<class 'list'>`, {'k1': ['val1', 'val2'], 'k2': ['val3', 'val4', 'val5'], 'k3': []})
```

---

# Chapter 04 - 04

## Dict 심화 2

### MappingProxyType

```python
# immutable Dict
# 수정 불가능한 dict (read only dict)
from types import MappingProxyType

d = {'key1': 'value1'}


# Read Only
# read only는 fronzen이라고 많이들 함
d_frozen = MappingProxyType(d)


>>> d_frozen
mappingproxy({'key1': 'value1'})



# d와 d_frozen의 id값이 다르기 때문에
>>> d is d_frozen
False


>>> d == d_frozen
True



# mutable한 dict는 수정 가능
d['key2'] = 'value2'


>>> d
{'key1': 'value1', 'key2': 'value2'}



# immutable한 dict는 수정 불가
# 에러 발생
>>> d_frozen['key1'] = 'value2'
Traceback (most recent call last):
  File "<input>", line 1, in <module>
TypeError: 'mappingproxy' object does not support item assignment
```

<br>

## Set 심화

```python
s1 = {'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'}
s2 = set(['Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'])


# set은 중복 값 불가능
>>> s1
{'Kiwi', 'Orange', 'Apple'}


>>> s2
{'Kiwi', 'Orange', 'Apple'}


# 아래의 방법으로 set 생성 가능
s3 = {3}
s4 = set()
```

### frozenset

- immutable한 set

```python
s1 = {'Kiwi', 'Orange', 'Apple'}
s5 = frozenset({'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'})


>>> s5
frozenset({'Kiwi', 'Orange', 'Apple'})


# mutable한 set에 값 추가
s1.add('Melon')


>>> s1
{'Kiwi', 'Orange', 'Melon', 'Apple'}



# frozenset은 immutable하기에 값 추가 불가
# 에러 발생
>>> s5.add('Melon')
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: 'frozenset' object has no attribute 'add'
```

### set 선언 최적화

```python
# 선언 최적화
# dis : 바이트 코드 -> 파이썬 인터프리터 실행
from dis import dis

>>> dis('{10}')
  1           0 LOAD_CONST               0 (10)
              2 BUILD_SET                1
              4 RETURN_VALUE


# 아래의 방식이 더 오래걸림
>>> dis('set([10])')
  1           0 LOAD_NAME                0 (set)
              2 LOAD_CONST               0 (10)
              4 BUILD_LIST               1
              6 CALL_FUNCTION            1
              8 RETURN_VALUE


# 지능형 집합(Comprehending Set)
# unicodedata.name(char) : chr 문자에 할당된 이름을 문자열로 반환
from unicodedata import name

>>> {name(chr(i), '') for i in [1,10,100,1000]}
{'', 'COPTIC CAPITAL LETTER HORI', 'LATIN SMALL LETTER D'}
```

---

#### Reference

[Container 분류](https://www.youtube.com/watch?v=89A5yfs9Av4)<br>
[iterator, generator image](https://nvie.com/posts/iterators-vs-generators/)

<br>
