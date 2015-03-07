---
layout: post
title: Github Pages에 Jekyll custom plugin 적용하기
---

## TL;DR

[Github Pages에서 지원하는 Jekyll plugin](https://pages.github.com/versions/)외의 것을 쓰려면
최종 html/css/js 파일이 들어가는 `_site` 디렉토리를 `master` branch로 변경한다.


## 히스토리

다음 내용은 [블로그 이슈](https://github.com/gumpcha/gumpcha.github.io/issues/2)에서 진행된 내용을 정리했다.

[두 번째 블로그](http://gumpcha.github.io/blog/mac-sleep-restart-shutdown/) 글을 올리고 혹시나 해서 윈도우에서 확인해 보니, 애플 아이콘이 깨져서 안 보인다. 검색해 보니 unicode 문제라고 하고, 웹에 게시할 때는 쓰지 말라고 해서 `font-awesome`의 애플 아이콘을 적용하면 되겠다 싶었다.

[Font Awesome icons Jekyll tag plugin](http://ryancmorrissey.com/blog/2014/01/25/font-awesome-icons-jekyll-tag-plugin/)을 설치하고 다음과 같이 수정했다.

```
-- sleep: <U+F8FF> -> Sleep
-- restart: <U+F8FF> -> Restart
-- shutdown: <U+F8FF> -> Shut Down
+- sleep: {% icon fa-apple %} -> Sleep
+- restart: {% icon fa-apple %} -> Restart
+- shutdown: {% icon fa-apple %} -> Shut Down
```

local에서 확인하니 잘 나와서 `push`를 했는데 github에서 메일이 한 통 날라온다.

> The page build failed with the following error:
>
> The tag `icon` in `_posts/2014-12-23-mac-sleep-restart-shutdown.md` is not a recognized Liquid tag. For more information, see https://help.github.com/articles/page-build-failed-unknown-tag-error.

github pages는 새로 push가 되면 보안 문제 때문에 `--safe` 모드로 jekyll processing을 하기 때문에 github가 제공하는 plugin이 아니면 liquid tag를 인식하지 못 하는 문제이다.

해결 방법을 말로 설명하면, 로컬에서 `jekyll build`나 `jekyll serve`를 해서 `_site` 디렉토리에 최종 html/css/js 파일을 생성한 다음 push를 해야 되는 상황.

기술적인 해결법은 sources 브랜치에서 편집/작성한 글을 _site 디렉토리에 생성한 다음 _site 디렉토리를 master 브랜치로 설정하면 된다.


## 사전 작업

1. `.gitignore`에서 _site 삭제하고 _site 전체를 `git add`

    ```bash
    (master)$ vi .gitignore
    ... delete _site
    (master)$ git add .gitignore _site
    (master)$ git ci -m 'Prepare source/master branch for custom jekyll plugin'
    ```
1. source branch를 생성하고 기존의 master와 함께 push

    ```bash
    (master)$ git checkout -b source
    (source)$ git push --all
    ```
1. 웹으로 github 저장소 `Settings` 메뉴로 들어가 Default branch를 source로 변경
1. local에서 remote 저장소의 default branch 적용

    ```bash
    (source)$ git remote set-head origin -a
    ```
1. local/remote의 master branch 모두 삭제

    ```bash
    $ git push origin :master
    $ git branch -d master
    ```


## 최종 Workflow

1. `source` branch에서 새 글을 작성하거나, 사이트 내용 변경
1. `jekyll serve`로 내용 확인
1. 추가/변경된 내용 commit

    ```bash
    (source)$ git commit -am 'Add new post'
    ```
1. _site를 master branch로 재적용. 사전 준비가 끝나고 나서 처음 push할 때는 local에 master가 없으므로 `git branch -d master`는 건너뜀

    ```bash
    (source)$ git branch -D master
    (source)$ git checkout -b master
    (master)$ git filter-branch --subdirectory-filter _site/ -f
    ```
1. source/master branch 모두 push

    ```bash
    (master)$ git push --all
    ```

위 Workflow에서 사용하는 여러 명령어들은 git alias나 [간단한 쉘 스크립트](https://github.com/gumpcha/gumpcha.github.io/commit/b487e1bc507981f3b1af210463031cd678bacc4e), [grunt task](http://james-oldfield.co/blog/jekyll-plugins-with-gh-pages/), travis-ci 등을 이용해 한 번에 적용할 수 있다.


## 참고

- http://davidensinger.com/2013/04/deploying-jekyll-to-github-pages/
- http://drewsilcock.co.uk/custom-jekyll-plugins/
