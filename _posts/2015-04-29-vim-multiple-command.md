---
layout: post
title: Vim에서 여러 명령어 동시에 실행하기
---

`|`를 쓰면 된다.

예를 들어, 전체 내용 중 one을 ONE으로 바꾸고, two를 TWO로 바꾸고, 빈 줄을 지우려면

```
:%s/one/ONE/g | %s/two/TWO/g | g/^$/d
```

라고 하면 된다.
