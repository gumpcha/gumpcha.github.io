---
layout: post
title: Bootstrap 3 grid system 이해하기
---

## 소개

Bootstrap 3 grid system은 브라우져 크기에 따라 한 줄에 최대 12 컬럼의 컨텐츠를 어떻게 배치할 것인가를 정해주고,
기본 구조는 다음과 같아야 한다.

```
.container
  .row
    .col-xs-#
    .col-xs-#
  .row
    .col-xs-#
    .col-xs-#
```

Column class는 브라우져 크기에 따라 다음 네 가지로 정의된다.

 Column class | Desc.
--------------|-------
.col-xs-# | Extra Small(핸드폰, 768px 이하)
.col-sm-# | Small(태블릿, 768px 이상)
.col-md-# | Medium(데스크탑, 992px 이상)
.col-lg-# | Large(데스크탑, 1200px 이상)

알아둘 것

* 1 row에 12 column이 넘어가면 자동으로 다음 row로 처리된다.
* **Mobile first** 정책이므로, 아무것도 지정하지 않으면 `.col-xs-1`이 지정된다.


## 예제

총 4개의 column을 사이즈별로 다음처럼 구성하고 싶은 경우

1. 모바일에서는 한 줄에 1 컬럼
1. 태블릿과 Medium 데스크탑에서는 한 줄에 2 컬럼
1. Large 데스크탑에서는 한 줄에 4 컬럼

이렇게 지정하면

```
<div class="row">
    <div class="col-sm-6 col-lg-3">
        column 1
    </div>
    <div class="col-sm-6 col-lg-3">
        column 2
    </div>
    <div class="col-sm-6 col-lg-3">
        column 3
    </div>
    <div class="col-sm-6 col-lg-3">
        column 4
    </div>
</div>
```

모바일에서는 `.col-xs`를 따로 지정하지 않았기 때문에 디폴트로 `.col-xs-1`이 적용되고

```
| column 1 |
| column 2 |
| column 3 |
| column 4 |
```

태블릿과 Medium 데스크탑에서는 `.col-sm-6`가 적용되며

```
| column 1 | column 2 |
| column 3 | column 4 |
```

Large 데스크탑에서는 `.col-lg-3`이 적용된다.

```
| column 1 | column 2 | column 3 | column 4 |
```


## 참조

* [Understanding the Bootstrap 3 Grid System](https://scotch.io/tutorials/understanding-the-bootstrap-3-grid-system)
* [Bootstrap grid system 공식 설명](http://getbootstrap.com/css/#grid)
* [Bootstrap grid system 공식 예제](http://getbootstrap.com/examples/grid)
