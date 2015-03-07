---
layout: post
title: 쉘에서 계산하기
---

# TL;DR

정수만 계산하려면 bash만으로 가능하고, 실수까지 계산하려면 `bc`란 유틸리티를 쓰면 된다.

# 정수 계산

bash 연산자 expr 사용

```bash
$ expr 1 + 2        # + 앞 뒤에 빈 칸 필요
3
```

bash 내장 명령어 let 사용

```bash
$ let a=1
$ echo $a
1
$ let b=a+2
$ echo $b
3
```

bash 연산자 $((...)) 사용

```bash
$ a=1
$ echo $((a+2))
3
```


# 실수 계산

`bc`란 유틸리티를 사용. 계산 결과가 0 이하이면 .33 처럼 표시되므로 추가적인 0을 붙여줘야 됨.

```bash
$ echo "1/3" | bc
0
$ echo "1/3" | bc -l
.33333333333333333333
$ echo "scale=2; 1/3" | bc -l
.33
$ echo "scale=4; 1/3" | bc -l
.3333
$ bc -l <<< "scale=2; 1/3"
.33
```


# 참고

* [Performing Math calculation in Bash](https://www.shell-tips.com/2010/06/14/performing-math-calculation-in-bash/)
* [Advanced Bash-Scripting Guide - Chapter 13. Arithmetic Expansion](http://www.tldp.org/LDP/abs/html/arithexp.html)
