---
layout: post
title: 맥(Mac) 잠자기/재기동/종료 방법 총정리
---

크게 다음 네 가지 방법이 가능하다.

- 시스템 메뉴
- 전역 단축키
- Alfred
- Terminal

## 시스템 메뉴

- sleep:  -> Sleep
- restart:  -> Restart
- shutdown:  -> Shut Down


## 전역 단축키

- sleep: `command` + `option` + `power` 혹은 1.5초간 `power`
- display sleep: `shift` + `control` + `power`
- restart: `command` + `control` + `power`
- shutdown: `command` + `control` + `option` + `power`
- sleep/restart/shutdown 선택 창: `control` + `power`


## Alfred

- sleep: sleep
- restart: restart
- shutdown: shutdown


## Terminal

- sleep: `$ sudo shutdown -s now`
- restart: `$ sudo shutdown -r now`
- shutdown: `$ sudo shutdown -h now`

나중에 하기

- 30분 뒤에 shutdown 하기: `$ sudo shutdown -h +30`
- 특정 시각(2014/12/23 13:30)에 restart 하기: `$ sudo shutdown -r 1412231330`


## 참고

- [OS X: Keyboard shortcuts](http://support.apple.com/en-us/HT201236)
- [Terminal 101: Properly Shutting Down Your Mac
](http://www.maclife.com/article/columns/terminal_101_properly_shutting_down_your_mac)
- [[간단한 팁] 맥을 가장 빠르게 재시동∙잠자기∙종료시키는 방법 [OS X 10.9.2 개정판]](http://macnews.tistory.com/841)
