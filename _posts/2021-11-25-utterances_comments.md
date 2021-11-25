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

## utterances

[utterances](https://utteranc.es)는 GitHub issue를 기반으로 하는 댓글 시스템이다. 블로그에서 댓글을 달면 repo의 이슈에 댓글이 달린다 🤓 <br>
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
아래에 링크를 걸어둔 블로그를 참고하면 더 자세한 설명을 볼 수 있다 🙇🏻‍♀️ 

utterances를 설정할 때 참고하면 좋은 블로그 : [Jekyll 블로그에 utterances로 댓글 기능 추가하기](https://madplay.github.io/post/jekyll-blog-comments-with-utterances)

---

## TeXt Theme에 utterances 적용하기


위에서 얻은 script를 jekyll TeXt Theme에 적용해보자! 💪


댓글을 적용하려면 몇 가지 세팅이 필요하다. 

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
결과적으로 `custom.html`에 위에서 얻은 script 내용을 붙여넣기 하면 댓글을 사용할 수 있다!


#### 3. push해서 확인하기









