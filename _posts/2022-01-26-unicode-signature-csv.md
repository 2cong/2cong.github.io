---
layout: article
title: BOM이 포함된 UTF-8 문서 디코딩하기
tags: CSV UTF-8
aside:
  toc: true
---

## 이슈 사항
업로드된 CSV 파일을 처리하는 기능을 만들었다. 해당 코드의 처리 방식은 다음과 같다.

1. 업로드된 CSV 파일을 UTF-8로 디코딩
   - 파일 형식이 UTF-8이 아니면 UnicodeDecodeError 발생
2. 업로드한 CSV 파일의 header가 정해둔 format `['path', 'tags']`과 일치하는지 확인
   - format과 맞지 않으면 ValidationError 발생
3. 파일 처리

그런데 파일의 header가 format과 일치하는데도 ValidationError가 발생한 경우가 있었다 🤔 <br>
특이한 점은 에러가 발생한 파일을 다른 방식으로 저장하니 같은 에러가 발생하지 않았다는 점이었다.

- 엑셀의 `CSV UTF-8(쉼표로 분리)`를 이용해서 저장 → ValidationError 발생 
- 엑셀의 `쉼표로 구분된 값`을 이용해서 저장 → 에러 발생하지 않고 잘 처리됨 🤷‍♀️

<br>
내가 생각한 이상한 점은 2개였다.
1. 왜 format이 맞는데 ValidationError가 발생했는지
2. `쉼표로 구분된 값`을 이용해 저장한 파일은 UTF-8 형식이 아닌데 왜 UnicodeDecodeError가 발생하지 않았는지

-----

## 문제 확인 1
먼저 ValidationError가 발생한 파일부터 확인해보았다. <br>
decoding한 파일의 header를 확인해보니 아래와 같이 예상하지 못했던 \ufeff라는 문자열이 포함되어 있었다.

```bash
['\ufeffpath', 'tags']
```

\ufeff가 무엇인지 찾아보니 해당 [문자열은 BOM("Byte Order Mark")](https://stackoverflow.com/questions/17912307/u-ufeff-in-python-string/17912811) 이라고 하였다.

### BOM

BOM은 문서의 가장 앞에 추가하여 해당 문서가 어떤 방식으로 인코딩이 되었는지 알려주는 일종의 signature 이다.
다만 UTF-8에서 바이트 순서는 어떤 의미도 없다고 한다.
<br>

따라서 이와 같이 BOM이 포함된 UTF-8 문서에서는 해당 문자열을 제거해 주어야 한다. <br>
해당 문자열을 제거하려면 decode 방식을 변경 하면 된다! [utf-8-sig를 이용하여 decode](https://docs.python.org/3/library/codecs.html#encodings-and-unicode)를 하면 처음 3bytes에 위의 signature가 나타나는 경우 해당 내용을 뛰어넘고 디코딩한다고 한다. 

```python
# 변경 전
file_stream = StringIO(csv_file.stream.read().decode("utf-8"), newline=None)

# 변경 후
file_stream = StringIO(csv_file.stream.read().decode("utf-8-sig"), newline=None)
```
이렇게 decode 방식을 변경하고 나니 header에 \ufeff이 포함되지 않게 디코딩되었고 ValidationError 발생 없이 파일이 처리가 되었다!

- [참고](https://stackoverflow.com/questions/8898294/convert-utf-8-with-bom-to-utf-8-with-no-bom-in-python)
- [참고2](https://en.wikipedia.org/wiki/Byte_order_mark)

-----

## 문제 확인 2
`쉼표로 구분된 값`으로 저장된 파일에서 에러가 발생하지 않았던 이유를 알아보기 위해, 인코딩된 파일들의 형식을 확인해보았다. <br>

<img width="643" alt="image" src="https://user-images.githubusercontent.com/60612551/227759548-4615b87f-1c33-493e-90e2-bee706b8fb53.png">

test_utf_8.csv 파일은 `UTF-8(쉼표로 분리)` 를 이용해서 저장한 파일이고, test.csv 파일은 `쉼표로 구분된 값` 을 사용하여 저장한 파일이다.
(test_utf_8.csv 파일에 (with BOM)이라는 표시가 되어있음을 확인할 수 있었다..! (with BOM) 파일이었어서 위의 디코딩 에러가 발생했던거였다 😆)

### ASCII 와 UTF-8
에러가 발생하지 않았던 파일의 형식은 ASCII 였다..!
ASCII는 영문 알파벳을 사용하는 대표적인 문자 인코딩으로 영어가 아닌 다른 문자를 표현하기에는 어려움이 있다. 그래서 나온 방식이 Unicode(국제 표준 문자표)이다.

UTF-8은 유니코드를 인코딩(encoding)하는 방식이다.
UTF-8은 가변 인코딩 방식으로 한 문자를 나타내기 위해서 1~4 byte를 사용하는데, 1byte로 표현 가능한 문자(ASCII)는 1byte로 표현하고 더 많은 byte가 필요할 때는 더 많은 byte를 사용하게 된다.
그러므로 [ASCII 인코딩은 UTF-8의 부분 집합이다. 일반적인 ASCII 문자열은 올바른 UTF-8 문자열이며, 따라서 하위 호환성이 보장된다](https://ko.wikipedia.org/wiki/UTF-8)


- [Unicode, UTF-8 참고](https://jeongdowon.medium.com/unicode%EC%99%80-utf-8-%EA%B0%84%EB%8B%A8%ED%9E%88-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-b6aa3f7edf96)
- [UTF-8, ASCII 참고](https://velog.io/@kim-jaemin420/ASCII-Unicode-%EC%95%84%EC%8A%A4%ED%82%A4%EC%BD%94%EB%93%9C%EC%99%80-%EC%9C%A0%EB%8B%88%EC%BD%94%EB%93%9C)

<br>
위의 test.csv 파일은 영어로만 이루어진 문서였다. 따라서 엑셀의 `쉼표로 구분된 값`을 이용해서 인코딩 했을 때 ASCII로 인코딩된 것이다.
만약 test.csv 파일에 한글 등 다른 문자가 포함되어 있었다면 아래와 같이 다른 형식으로 인코딩이 될 것이다.

<img width="559" alt="image" src="https://user-images.githubusercontent.com/60612551/227763565-4f8d5f55-6601-46a7-a776-cebf4ca9e753.png">

이렇게 인코딩 형식이 다른 파일을 업로드하니 예상과 같이 UnicodeDecodeError가 발생했다 😆
<img width="767" alt="image" src="https://user-images.githubusercontent.com/60612551/227763638-9a0dafb6-e96b-476d-9e27-5dd09692e404.png">

<br>