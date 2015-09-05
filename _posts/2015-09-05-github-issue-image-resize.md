---
layout: post
title: Github issue에 첨부 image 크기 조절하기
---

github issue에 이미지를 끌어다 놓으면 다음과 같은 markdown이 자동으로 생성된다.

```
![파일이름](cloud.githubusercontent.com업로드된 URL)
```

이 이미지는 width가 100%로 잡히기 때문에 이미지 크기를 조절하고 싶다면 `width` attribute를 적용하면 된다.

```
<img src="cloud.githubcontents.com에 업로드된 URL" width="40%">
```

참고로, `style` attribute는 안 먹는다.


## 참고
- http://stackoverflow.com/a/27830083/2260566
- http://blog.davidebbo.com/2014/11/using-issues-for-github-pages-screenshots.html
