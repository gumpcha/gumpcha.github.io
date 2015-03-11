---
layout: post
title: git에서 symbolic link 파일 처리하기
---

## 문제

git 저장소에 **저장소 외부의 다른 파일**을 가르키도록 `ln -s`로 symbolic link를 생성하면 저장소 외부 파일의 변경 내역이 관리되지 않는다.


## 문제 발생 맥락

블로그 포스팅용 파일(`blog-posting.md`)이 블로그 저장소 외부에서 따로 관리하는 파일(`~/Wiki/blog-posting.md`)과 중복이 되서, `blog-posting.md`가 `~/Wiki/blog-posting.md`를 symbolic link하도록 블로그 저장소를 변경했다.

```
blog-repo $ rm blog-posting.md
blog-repo $ ln -s ~/Wiki/blog-posting.md blog-posting.md
blog-repo $ git status
...
	typechange: blog-posting.md
...
```

처음 보는 `gis status`의 `typechange`라는게 실행 퍼미션 정도가 바뀐 건가 하고, 그냥 commit.

나중에 git commit history를 보니 파일 내용은 없어지고, `~/Wiki/blog-posting.md` 라는 내용만 남아 있다. 실제 내용이야 html 파일로 남아 있긴 하지만 뭔가 찝찝해 해결책을 찾아보기로 했다.


위 내용에 대한 실제 커밋은 다음과 같은나 간단한 설명을 위해 파일 이름을 임의로 정해서 설명했다.

1. [regular file을 symbolic link로 변경한 커밋](https://github.com/gumpcha/gumpcha.github.io/commit/9851a01b03c85585a4d9d86151e962a5b3b55733)
1. [softlink를 hardlink로 바꾼 다음 커밋](https://github.com/gumpcha/gumpcha.github.io/commit/37c96daaa5e6e892a64778efdf879a71477e7c96)


## 분석

처음 symbolic link 파일을 만들 때 습관적으로 `ln -s`라고 쳐서 softlink 파일이 생성되었고,
git는 softlink된 파일은 파일 내용이 아니라 target file의 **full path**를 저장한다.

또한, `git status`에 `typechange`라고 나오면 symbolic link와 regular file이 서로 바뀐 경우이다.


## 해결 방법

softlink가 아닌 hardlink로 symbolic link를 만들어서 저장소 밖의 원본 파일이 변경되어도 저장소에서 변경 내역을 인식하게 만든다.

```
blog-repo $ rm blog-posting.md
blog-repo $ ln ~/Wiki/blog-posting.md blog-posting.md
blog-repo $ git status
...
  typechange: blog-posting.md
...
```

softlink와 hardlink에 대한 자세한 내용은 [Hard Link vs Soft Link](http://www.geekride.com/hard-link-vs-soft-link/)를 참고.
