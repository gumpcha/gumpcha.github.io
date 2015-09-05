---
layout: post
title: git 저장소에 이미 commit 된 파일 무시하기
---

로그나 바이너리등 git로 관리가 불필요한 파일인데 `.gitignore`에 추가하는 것을 빼먹고 이미 commit하는 경우가 있다.

이럴 경우, working directory에는 남겨두고 staging area에서만 삭제해야 하므로 `git rm --cached` 명령어로 해당 파일을 더 이상 추적하지 않게 하고 다시 commit을 해 준 다음,


```sh
$ git rm --cached log.txt
$ git commit -m 'untrack log.txt'
```

`.gitignore`에 해당 파일을 추가해 주고 `.gitignore`도 commit 해 주면 된다.

```sh
$ vi .gitignore
...
log.txt # 추가
...
$ git commit -am 'ignore log.txt'
```
