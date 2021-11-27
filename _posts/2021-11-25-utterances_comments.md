---
layout: article
title: TeXt Theme에 utterances 적용하기
tags: jekyll TeXt_Theme utterances
aside:
  toc: false
---

[TeXt Theme](https://tianqi.name/jekyll-TeXt-theme/docs/en/quick-start)에 utterances를 이용하여 댓글 기능 추가하기!<br>
많은 사람들이 Jekyll 블로그에 utterances를 이용하여 댓글 기능을 추가하고 있길래 나도 utterances를 사용해보기로 했다.

<!--more-->

---

## utterances 🔮

[utterances](https://utteranc.es)는 GitHub issue를 기반으로 하는 댓글 시스템이다. 블로그에서 댓글을 달면 repo의 이슈에 댓글이 달린다 😎<br>
utterances는 광고도 없고 무료이며 연동도 쉽다!

utterances 사이트에 나온 것처럼 [utterances app](https://github.com/apps/utterances)을 설치하고 utterances를 설정만 하면 된다.

설정이 끝나고 나면 아래와 같은 script를 얻을 수 있다. 이 script를 원하는 html 파일에 붙여넣기 하면 댓글 기능을 사용할 수 있다.
<br>

```html

<script src="https://utteranc.es/client.js"
        repo="2cong/2cong.github.io"
        issue-term="title"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>

```
<br>
<br>
아래에 링크를 걸어둔 블로그를 참고하면 더 자세한 설명을 볼 수 있다.

⭐️ utterances를 설정할 때 참고하면 좋은 블로그 : [Jekyll 블로그에 utterances로 댓글 기능 추가하기](https://madplay.github.io/post/jekyll-blog-comments-with-utterances)

---

## TeXt Theme에 utterances 적용하기 💪


위에서 얻은 script를 jekyll TeXt Theme에 적용해보자! 

제일 간단한 방법은 comment 기능을 넣고 싶은 위치에 해당 스크립트를 붙여넣는 것이다. <br>
이 방법을 사용하면 댓글을 무척 간단하게 적용할 수 있지만, 매 글마다 스크립트를 붙여주어야한다는 치명적인 단점이 있다..! 🤨

스크립트를 매번 붙여넣기 하지 않고 댓글을 적용하기 위해서는 몇 가지 세팅이 필요하다.
<br>
<br>

### 세팅하기 🤓

#### 1. _config.yml 파일 수정하기

먼저 ``` _config.yml``` 파일 에서 comments의 **provider** 부분을 수정한다.<br>
기본적으로 제공하는 댓글 서비스로는 disqus, gitalk, valine이 있는데, 나는 위의 제공하는 서비스를 사용하지 않을 것이기에 아래와 같이 **custom**을 입력하였다.


```yaml

# _config.yml

## => Comments
##############################
comments:
  provider: custom # false (default), "disqus", "gitalk", "valine", "custom"

```

#### 2. comments.html에 script 내용 적용하기

TeXt Theme의 화면들은 ```_layouts/page.html```을 바탕으로 구현된다.<br>
```page.html```을 확인해보니 댓글은 ``comments.html`` 파일의 내용을 사용하고 있었다.

아래의 이미지에서 해당 내용을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/60612551/143446361-31eb083a-d186-4ad9-90f7-d2ce7e977668.png)

<br>
provider를 custom으로 설정해두었으므로 ```comments.html```은  ```_includes/comments-providers/custom.html```에 있는 내용을 사용한다.
결과적으로 `custom.html`에 위에서 얻은 script 내용을 붙여넣기 하면 매 글마다 script를 추가 하지 않아도 해당 내용이 적용된다.


#### 3. push해서 확인하기

원하는 페이지에 script를 붙여넣기를 했을 때에는 로컬 서버에서도 comment가 추가된 화면을 확인할 수 있다. <br>
그러나 위의 방법으로 세팅한 후 로컬 서버로 확인하면 아무런 변화가 없다..😳!

git push를 하고 github 블로그 주소로 들어가면 comment 창이 생긴 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/60612551/143528240-e17fbf51-453b-4cca-93c4-6aa0579911fd.png)

댓글을 달면 이렇게 Issues에 등록된다! 나는 issue-term을 **title**로 지정했기에 내 블로그 포스트 제목으로 Issue가 등록되었다 😆

![image](https://user-images.githubusercontent.com/60612551/143528414-7a9447f6-b95d-4b85-95ff-9b8a983228f2.png)

<br>

### Blog Post에만 댓글 창 보이게 하기 


위의 과정을 거치고 나니 댓글 창이 생겼다! 그러나.. 사진과 같이 메인 페이지를 비롯한 모든 페이지에 댓글 창이 존재하게 되었다..😳

![image](https://user-images.githubusercontent.com/60612551/143600477-cddd66a7-9c41-446f-aa04-daa5a4ab4038.png)

나는 post에만 댓글 창이 보이게 만들고 싶었는데.. 😭 그래서 post에만 댓글이 나오도록 코드를 수정하기로 했다..!
<br>

#### 해결 방법 😎

아래 이미지의 내용이 `page.html`에서 댓글에 대한 정보가 있는 곳 이다.
여기에서 _comment 조건에 따라 댓글이 표시되는지 아닌지를 설정할 수 있다.

![image](https://user-images.githubusercontent.com/60612551/143446361-31eb083a-d186-4ad9-90f7-d2ce7e977668.png)


_comment는 `page.html`의 아래 부분을 통해서 정의된다.

![image](https://user-images.githubusercontent.com/60612551/143669505-e71ec2c4-ad5c-49f8-ab2c-0e220aaa710c.png)

##### 1. variables.yml 수정
post 글을 제외한 나머지는 댓글이 없었으면 좋겠으니 `_data/variables.yml`의 comment를 false로 변경하였다.


```yml

# _data/variables.yml

  page:
    mode: normal
    type: webpage
    article_header:
      align: left
      theme: light
    articles:
      show_cover: true
      show_excerpt: false
      show_readmore: false
      show_info: false
    show_title: true
    show_edit_on_github: false
    show_date: true
    show_tags: true
    show_author_profile: true
    show_subscribe: false
    full_width: false
    sharing: false
    comment: false
    license: false
    pageview: false
    search: default

```

<br>
그러면 모든 페이지에 댓글이 사라진다!
아까와 다르게 메인 페이지에 댓글 창이 사라진 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/60612551/143685216-1f3857b2-e749-4193-9824-f06e75603fdb.png)


##### 2. article.html 수정
post 글은 `_layout/article.html`의 layout을 사용한다. 
따라서 `article.html`에 아래와 같이 comment: true 내용을 추가한다.

```html
# article.html

---
layout: page
type: article
comment: true
---
```

그러면 `article.html`의 layout을 사용하는 post글에 댓글 창이 생긴다 😆


##### 3. about.md  수정

`article.html`를 수정한 후에 about 페이지에도 댓글 창이 생겼다 😭 <br>
about 페이지도 `article.html`의 layout을 사용하기 때문이다.

about 페이지에도 comment에 관련된 내용을 추가해보자..!
이번에는 `about.md`를 수정했다. 

아래와 같이 comment: false 라는 내용을 추가했다.
```html
# about.md

---
layout: article
titles:
  # @start locale config
  en      : &EN       About
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  关于
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  關於
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  ko      : &KO       소개
  ko-KR   : *KO
  fr      : &FR       À propos
  fr-BE   : *FR
  fr-CA   : *FR
  fr-CH   : *FR
  fr-FR   : *FR
  fr-LU   : *FR
  # @end locale config
comment: false
# key: page-about
---
```

위의 내용을 수정하고 나니 about 페이지에 댓글 창이 사라졌다!

---

이렇게 comment와 관련된 내용을 수정하여 post 글에만 댓글 창이 보이도록 수정하였다.<br>
여태껏 화면을 다루어보는 일을 거의 해보지 않았기 때문에 이 내용을 어떻게 수정해야 막막했다.
그렇지만 여러가지 시도 끝에 원하는 모습으로 만들어서 무척 뿌듯하고 또 그 과정에서 많은 공부가 되었던 것 같다. 

