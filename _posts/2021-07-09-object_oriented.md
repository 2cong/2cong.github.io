---
layout: article
title: 객체지향
tags: 객체지향
aside:
  toc: false
---


[프로그래밍 응용: 객체지향](http://www.kocw.net/home/cview.do?cid=42e5d809846bebec)을 보고 정리한 내용

<!--more-->

---

## 객체지향

### 객체지향의 개념

- 현실 세계의 사물 또는 개념을 객체로 표현하는 방식
- 객체와 객체간의 상호작용이라는 관점!

### 객체지향 기법의 특징

- 세상을 상호작용하는 객체의 집합으로 이해
- 객체 = 데이터 + 기능


    ```
    자동차를 예로 들면
    
    자동차의 번호판, 색 등이 데이터
    자동차가 움직이는 것, 가스 배출 등의 기능
    ```
    
- 객체간의 상호 작용 : 메시지를 이용한 객체간 통신
- 현실 세계를 자연스럽게 표현

-----

## 객체와 클래스

### 객체의 정의

- 하나의 단위로 인지할 수 있는 대상
  
- 현실 세계에 존재하는 물체 또는 추상적 개념을 포함
  
- 필요한 자료구조 (데이터)와 그 자료구조를 처리하는 연산(기능)으로 구성
  

    ```
    데이터와 기능의 다양한 표현
    Data : Data, Attribute, Property, Field
    기능 : 기능, 연산, Operation. Behavior, Method
    ```
    

### 클래스의 정의

- Class : 객체의 유형
- <span style="color:#d0250d">공통의 성질</span>을 가지는 객체를 <span style="color:#0067a3">하나의 유형</span>으로 정의 (객체 생성의 기본 Template을 제공)
- 클래스는 정의에 따라 객체가 생성
    
    → 객체는 클래스의 Instance
    
    → 하나의 클래스에서 여러 개의 객체 생성 가능
    

### 객체 vs 클래스

![image](https://user-images.githubusercontent.com/60612551/143244123-9480efb0-9bef-4432-b271-a866b6a41ab8.png)
- 객체
    
    → 프로그램이 실행되는 동안 상태를 저장하고 있는 저장소
    
    → 객체는 동작이 규정되어있으며 관련된 데이터도 명확
    
    → 프로그래밍이란 객체를 정의하고 이를 동작시키는 명령을 나열하는 것
    
- 클래스
    
    → 객체 정의를 위한 틀 (<span style="color:#0067a3">객체를 생성하는 틀</span>)
    
    → 클래스 안에 데이터와 메소드를 정의
    
    → 메소드 안에는 메소드 내부에서만 사용하는 지역 변수(데이터) 존재
    

---
## 메소드 정의와 호출

### Method

- 클래스 내부에서 정의된 명령어 집합
- 처리결과를 **Return 할 수 있음**
- 클래스 내부의 메소드에서 다른 메소드 호출 가능

![image](https://user-images.githubusercontent.com/60612551/143251602-993e49f2-4cb2-4d48-836b-5c75152863f3.png)

---
## 오버로딩과 생성자

### Overloading

- 하나의 클래스 내에 인수의 개수 또는 데이터 형이 다른 <span style="color:#0067a3">동일한 이름의 메소드를 여러 개 정의하는 것</span>
- 메소드 호출은 정의된 인수에 의해 <span style="color:#0067a3">인수의 수와 데이터 형이 일치하는 메소드가 호출됨</span>
- Python에서는 Overloading을 정식으로 지원하지 않음

### Constructor

- 생성자
- 객체 생성과 함께 자동으로 호출되는 메소드
- 클래스와 같은 이름을 가지며 반환값이 없음
- 필드 값을 초기화하는 경우 많이 이용
- Python의 ``__init__``

---

## 상속의 개요

### 상속 (Inheritance)

- 다른 클래스를 계승하는 기능
    
    → 부모의 클래스의 필드와 메소드를 자식 클래스가 계승(상속)받음
    
- 부모 클래스 : <span style="color:#0067a3">Super Class</span>
- 자식 클래스 : Sub Class
- Super Class와 Sub Class는 1:N의 관계
    
    → 단일 Super Class에서 다수의 Sub Class 생성 가능
    
    → 단일 Sub Class는 단일 Super Class를 가짐
    

![image](https://user-images.githubusercontent.com/60612551/143251983-be8c4ea6-2e22-4819-b4ce-1ef8f9dbf3bc.png)

### 상속의 개념 정리

- 상속관계의 클래스들은 계층구조를 구성 가능
- 자식(Sub) 클래스는 부모(Super)의 모든 요소(필드와 메소드)를 상속
    
    → 자식 클래스에서 추가적으로 필요한 새로운 필드와 메소드를 정의 가능
    
- 자식 클래스는 부모 클래스의 확장 개념
- 상속 개념을 이용하여 소프트웨어의 재사용(Reuse) 지원
    
    → 중복의 감소

---

## 접근 제한자 (한정자, Access Modifier)


- Java에서는 필드(멤버 변수)와 메소드에 4종류의 접근 제한자를 제공
    
    → 멤버 변수 : Class 내에 선언되는 변수
    

- public : 클래스가 접근 가능하면 필드와 메소드에 <span style="color:#0067a3">항상 접근 가능</span>
- protected : 클래스의 <span style="color:#0067a3">하위(sub) 클래스와 같은 패키지(폴더) 클래스</span>에서만 사용 가능
- private : 클래스 내에서만 접근 가능
    
    → 하위(sub) <span style="color:#0067a3">클래스</span>에서도 접근 불가
    
- 접근 제한자가 없는 경우(default) : <span style="color:#0067a3">동일 패키지(폴더) 내부</span> 클래스에서 사용 가능

![image](https://user-images.githubusercontent.com/60612551/143252431-d715dd3f-98f0-435a-861a-37186526ec6e.png)

![image](https://user-images.githubusercontent.com/60612551/143252498-e6072339-49b7-412a-9a11-35765cef23f5.png)

→ 접근이 제한된 필드에 대한 접근은 <span style="color:#0067a3">**메소드를 이용**</span>하여 접근 가능

- python에서는 접근 제한자가 없음
    
    → 암묵적인 컨벤션으로 접근 제한자를 정함 (실제로 접근이 제한되지는 않음)
    
![image](https://user-images.githubusercontent.com/60612551/143252634-922c9630-4595-4dd6-84c3-5c4596d6af49.png)


---

## 설정자와 접근자

### 설정자와 접근자

- 필드 값의 지정과 반환을 위한 메소드로 설정자와 접근자 활용
    
    → 외부와 통신하는 역할을 담당하는 메소드
    
    → 외부에서 필드에 접근이 불가능하더라도, 접근 가능한 <span style="color:#0067a3">메소드</span>를 통해서 필드 활용
    

- 설정자 (Mutator, <span style="color:#DD2525">Setter</span>)
    
    → 필드 값의 <span style="color:#0067a3">변경</span>과 <span style="color:#0067a3">지정</span>하는 역할을 담당하는 메소드
    
    → return 값 없음
    
- 접근자 (Accessor, <span style="color:#DD2525">Getter</span>)
    
    → 현재 특정 필드 값을 <span style="color:#0067a3">반환</span>해주는 역할을 담당하는 메소드
    

참고 : [자바 - Getter와 Setter 메소드](https://kephilab.tistory.com/54)


### Overriding

- Super Class에 정의된 메소드를 Sub Class에서 재정의 (메소드의 재정의)
    
    → 상속된 메소드와 <span style="color:#DD2525">동일한 이름, 동일한 인수</span>를 가진 메소드를 새롭게 정의 가능
    
    → 만약 메소드의 이름만 같고 parameter의 type, 개수 등이 다르면 overloading 됨
    

### Overriding vs Overloading

![image](https://user-images.githubusercontent.com/60612551/143253538-e9ef5fcd-f6c7-4b21-babd-774d26114a40.png)

- 접근 제한
    
    overloading - 접근 제한 상관 없음
    
    <span style="color:#0067a3">overriding</span> - 자식 class의 메소드가 부모 class 메소드의 접근 제한과 동일하거나 더 넓어야 함 
    
    → 부모 class : private / 자식 class : public (가능)
    
    → 부모 class : public / 자식 class : private (불가능)
    

### Overriding : super

- 하위 클래스에서 상위 클래스를 표현
    
    → 상위 클래스 : super
    
    → 상위 클래스의 생정자 : super()
    
---

## 수식자

### 수식자 : final

- 필드, 메소드 또는 클래스에 final 수식어 사용
    - 필드 :  <span style="color:#0067a3">필드의 값을 변경 불가능(상수)</span> → 대입 시 오류 발생
    - 메소드 : <span style="color:#0067a3">Overriding으로 확장이 불가능</span>함 (하위 클래스에서 재정의 불가능)
        
        → Overloading은 됨
        
    - 클래스 : 더 이상 <span style="color:#0067a3">상속이 불가능</span> (하위 클래스를 가질 수 없음)

→ python에서는 **상수를 표시**할 때는 **대문자와 _**를 이용하여 명시적으로 상수 표현

→ 아래와 같이 typing.Final 사용하여 상수 정의할 수 있으나 실제로 재 할당을 막지는 않음

```python
from typing import Final

a: Final = 1

a = 2
```

### 수식자 : static

- 필드와 메소드에 static 수식어 사용 → <span style="color:#0067a3">객체 생성 없이 사용가능 (클래스명.필드명 또는 클래스명.메소드명)</span>
    - 필드 (클래스 변수)
        
        → 여러 객체가 생성되더라도 단 하나의 static 필드만 생성됨
        
        → 동일 클래스의 모든 객체는 static 필드의 값을 공유함
        
    - 메소드 (클래스 메소드)
        
        → 동일 클래스의 모든 객체에서 같은 동작을 수행함
        
        → <span style="color:#0067a3">static 필드만을 이용해야 함</span>
        
- 참고 ) [static method vs instance method](https://jihyehwang09.github.io/2020/03/21/java-static-method-and-instance-method/)
- 참고 ) [static](https://mangkyu.tistory.com/47)

---

## 추상클래스와 추상메소드

### 추상 클래스 (Abstract Class)

- 추상 클래스
    
    - 추상 메소드를 포함하는 클래스
    
    - 키워드 abstract 사용
    
    - <span style="color:#DD2525">객체 생성이 불가능</span>
    
- 추상 메소드 정의
    
    - 반드시 하위 클래스에서 이 메소드가 구현(구체화)되어야 함
    
    - 키워드 abstract로 지정
    
    - <span style="color:#DD2525">구현 코드가 없고, 선언만 됨</span>
    
    - 추상 메소드 정의는 <span style="color:#0067a3">추상 클래스에서만 가능</span>함
    
- 키워드 abstract
    
    - 클래스 정의나 메소드 정의에서만 사용하며 <span style="color:#0067a3">변수 선언에는 사용할 수 없음</span>
    

- 추상 클래스는 직접적으로 객체 생성이 불가능하므로 Sub Class에서 추상 메소드를 Overriding 하여야 객체 생성이 가능함

예시)

Animal이 추상 클래스, cry가 추상 메소드인 경우 

![image](https://user-images.githubusercontent.com/60612551/143254236-44ee9e58-db64-447b-9311-730e202c67e6.png)

## 인터페이스

### 인터페이스의 필요성과 예

![image](https://user-images.githubusercontent.com/60612551/143254516-d70caf88-efd8-4782-a7fa-2ab890eadae4.png)

### 인터페이스 (Interface)

- 인터페이스는 객체와 객체 사이의 간의 상호 작용을 나타냄
- 클래스와 유사하나 <span style="color:#0067a3">public static final 변수(클래스 상수)</span>와 <span style="color:#0067a3">추상 메서드</span>로 구성
    
    (인터페이스는 모든 메서드가 추상 메서드이므로, 메서드의 abstract 키워드 생략 가능)
    
- 다중 상속과 유사한 개념 제공을 위한 구조

- 인터페이스의 구현
    - 클래스에서 인터페이스를 이용할 수 있도록 하는 것을 구현(implement)라고 함
    - 키워드 <span style="color:#0067a3">implements</span>를 사용
    - 여러 개의 인터페이스를 구현하는 것이 가능
    - 반드시 메소드를 <span style="color:#0067a3">public으로 overriding</span> 함

### 인터페이스 : 상속

- 인터페이스는 상속이 가능함
- 다수의 인터페이스 상속을 통해 새로운 인터페이스 정의가 가능
- 다중 상속 가능함 (interface는 interface간의 상속만 가능)

### 인터페이스 : 상속과 구현

- 클래스에 대한 상속과 인터페이스의 구현을 동시에 가능함
    
    → 키워드 extends를 implements보다 먼저 기술함
    

### 인터페이스의 장점

- 서로 관련성이 떨어지는 클래스를 특별한 상속 관계 없이 연결하여 사용 가능
- 하나 이상의 클래스를 통해서 구현해야 하는 메소드를 선언 가능
- 클래스 내용을 알지 못해도, 인터페이스를 통해 접근 가능

### 인터페이스 vs 추상 클래스

- 공통점
    - 모두 추상 메소드 (Abstract Method)를 보유

- 차이점
    - 인터페이스는 외부 메소드명을 명확하게 하고, 여러 클래스에서 사용되는 상수를 정의
    - 추상 클래스는 일부 메소드를 하위 클래스 구현에 위임하고, 직접적인 클래스로 부터의 객체 생성을 막는데 사용 (다형성 구현에 사용)
    - 추상 클래스는 <span style="color:#0067a3">부분적으로 미완성</span> 상태이나 인터페이스는 <span style="color:#0067a3">완전한 미완성</span> 상태
        
        → 추상 클래스는 필드와 완성된 메소드를 가질 수 있음
        
    - 인터페이스는 다중 상속과 다중 구현이 가능

![image](https://user-images.githubusercontent.com/60612551/143254691-51d6d38d-c7d0-468d-bcec-b78937f74864.png)


---

## 다형성 (Polymorphism)

### 다형성
- 여러가지 형태를 가지는 것
- 하나의 메소드나 클래스가 다양한 방법으로 동작이 되는 것
    
    → Overriding, Overloading
    
- 객체들의 타입이 다르면 똑같은 메시지가 전달되더라도 서로 다른 동작을 하는 것
    
    → 메시지 전달 = 메소드 호출 (Method Invocation)
    
    → 동물 abstract sound(), 고양이 sound(), 강아지 sound()
    
- 하나의 클래스의 객체로 다른 클래스의 객체를 참조할 수 있는 특성

### 상향 형변환 (Cast)

- 서브 클래스의 객체는 super 클래스 객체처럼 취급가능
- Super 클래스 참조 변수로 sub 클래스 객체를 참조할 수 있음
    
    → 상속 받은 일부분만을 사용 가능
    
![image](https://user-images.githubusercontent.com/60612551/143254778-0f3f743d-ce33-4602-a2e9-115d2b7c0cb6.png)

### 객체 다형성

- 상속받은 클래스의 객체는 Super 클래스와 하위 클래스 (this)로 모두 취급 가능
- <span style="color:#0067a3">sub class의 객체는 super class의 객체로 생성가능함</span>

![image](https://user-images.githubusercontent.com/60612551/143254978-67ffb955-a234-47e9-a543-994593da382d.png)


- sub class에서 Overriding할 메소드는 super class에서 추상 메소드로 정의하면 편리함

### 다형성의 이용

- 메소드의 매개 변수로 sub class 보다 super class 참조 변수를 이용
    
    → super class로 선언하면 하위 모든 클래스의 객체를 모두 사용 가능
    
    → 다형성을 이용하는 전형적인 방법 
    

### Object 클래스

- <span style="color:#0067a3">Object 클래스는 모든 클래스의 슈퍼 클래스</span>
    - Object 클래스의 메소드는 모든 클래스에서 사용 가능함
    - 모든 클래스의 객체는 Object 클래스의 객체로 취급 가능함

### 클래스와 객체 응용 : instanceof

- 연산 instanceof
    - 특정 객체가 특정 클래스의 객체인지 여부를 조사하기 위한 연산
    - 특정 객체가 특정 인터페이스의 구현 여부를 조사하기 위한 연산
- python에서는 isinstance

---

### 참고

- 자바에서 변수의 종류

![image](https://user-images.githubusercontent.com/60612551/143255041-24cc2839-af27-4b44-a342-bd37343a8c9e.png)

[전역변수(Global Variable)와 지역변수(Local Variable)](https://velog.io/@ikerbm94/%EC%A0%84%EC%97%AD%EB%B3%80%EC%88%98Global-Variable%EC%99%80-%EC%A7%80%EC%97%AD%EB%B3%80%EC%88%98Local-Variable)

[멤버변수 : 클래스 변수, 인스턴스 변수](https://ramees.tistory.com/16)

[python에서의 클래스 변수와 인스턴스 변수](https://wikidocs.net/1744)

- Python에서 추상화 Class
    
    [class 정리 - 추상클래스(abstract class)](https://wikidocs.net/16075)
    
    추상 클래스와 raise NotImplementedError 차이는?
    
    [abc 추상클래스](https://whereami80.tistory.com/188)
    

- 인터페이스 vs 추상클래스
    - 인터페이스 : 전부 추상 메서드
        
        → 상호 작용을 위해서 미리 정의된 약속
        
    - 추상 클래스 : 추상 메서드 + 일반 메서드

- Casting
    
    [타입변환 (Casting)](https://velog.io/@sezzzini/Java-Casting)
    

- Python Static method vs Jave Static method
    
    → 자바는 정적 메서드와 클래스 메서드가 동일한데 비하여 파이썬은 두개를 나눔 (class method, static method)
    
    → python : [class method vs static method](https://digiconfactory.tistory.com/entry/파이썬-클래스-2-정적-메서드와-클래스-메서드)
  
<br>
<br>