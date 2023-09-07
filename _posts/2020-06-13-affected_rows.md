---
layout: article
title: Affected Rows 
tags: mysql
aside:
  toc: true
---

pymysql에서 cursor.execute() 메서드를 사용하면 쿼리를 DB 서버에 보낼 수 있다.

```python
cursor.execute(query, value)
```

이 때 return 되는 값이 의미하는 것은 무엇일까? 

이 값은 affected rows로 방금 실행된 쿼리문에 영향을 받은 줄 수(데이터의 열 수)이다. 실제로 영향을 받은 값을 의미하기도 한다!

보통 INSERT, UPDATE, REPLACE, DELETE 에서 영향을 받은 줄 수를 의미한다.

### INSERT
Insert에서의 반환값은 삽입된 줄 수 이다. 한 줄을 입력했다면 return 되는 값은 1이다.

### UPDATE
Update에서 반환값은 실제로 변경된 줄 수를 의미한다. 만약 값을 변환하는 쿼리문을 실행했을 때 기존 데이터에 변화가 없는 경우는 쿼리문이 오류없이 잘 실행되었다고 해도 return이 0이다.

### DELETE
Delete에서 반환값은 삭제된 열 수를 의미한다.

### SELECT
select 문에서는 값의 변화 등이 없기 때문에 affected rows가 0 이라고 생각할 수 있으나 그렇지 않다!

select 문에서 execute 함수가 반환하는 값은 변경된 열의 갯수가 아니라 조회된 열의 갯수이다.

### ERROR
최근의 질의가 실패한 경우 -1을 반환한다.

이를 이용하여 쿼리의 에러 처리를 할 수 있다.

```python
 def find_option_code(self, db, code):
     try:
         with db.cursor(pymysql.cursors.DictCursor) as cursor:
             query = """
             SELECT size_id, color_id FROM options
             WHERE code = %s
             """

             affected_row = cursor.execute(query, code)
             if affected_row == -1:   # 에러처리
                 raise Exception('EXECUTE_FAILED')

             return cursor.fetchone()
     except Exception as e:
         raise e
```

위와 같이 affected_row가 -1인 경우 (최근의 쿼리가 실패한 경우) 에러 처리를 할 수 있다.

<br>