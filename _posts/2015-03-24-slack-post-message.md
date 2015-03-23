---
layout: post
title: slack.com 특정 채널에 메세지 보내기
---

몇 가지 방법이 있는 것 같은데, 두 가지만 정리해 본다.

# [Slackbot Remote Integration](https://my.slack.com/services/new/slackbot) 사용

메세지만 보내는 용도라면, Slackbot Remote Integration 방법으로 간단하게 특정 채널로 메세지를 보낼 수 있다.

[Slackbot 설정 페이지](https://my.slack.com/services/new/slackbot)에서 `Add Slackbot Integration` 클릭하고, 하단 Integration Settings 항목의 `Webhook URL`에 `channel` 파라미터 추가해 HTTP POST 전송하면 된다.

팀 이름이 example인 경우의 curl 예제(대문자는 파라미터)

```sh
$ curl --data "CHAT_MESSAGE" 'https://EXAMPLE.slack.com/services/hooks/slackbot?token=TOKEN&channel=%23CHANNEL_NAME'
```


# [Web API](https://api.slack.com/web) 사용

사용자 관리, 채널 관리, Direct Message 관리, 채팅 메세지 관리 등을 할 수 있는 [Web API](https://api.slack.com/web)를 써서 메세지를 보낼 수도 있다. Slackbot Remote Integration과는 다르게 사용자 이름이나 기타 설정등을 변경할 수 있는 등, 세밀한 제어가 필요한 경우 사용하면 되겠다.

[Web API](https://api.slack.com/web) 페이지에서 token을 생성한 다음, [메세지 포스팅에 필요한 파라미터](https://api.slack.com/methods/chat.postMessage)를 설정해서 HTTP POST로 전송한다.

채널명은 `#`이 붙은 `#channel_name` 형태로 지정해야 된다.

curl 예제(대문자는 파라미터)

```sh
$ curl -X POST -d "token=TOKEN&channel=CHANNEL_NAME&text=CHAT_MESSAGE&username=USERNAME" https://slack.com/api/chat.postMessage
```
