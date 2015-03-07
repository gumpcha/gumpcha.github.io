---
layout: post
title: 나만의 PopClip Extension 만들기
---


## 0. 목표 및 결과

마우스로 선택한 텍스트들을 `- `로 시작하는 목록으로 변경해 클립보드에 복사해 놓는 [**HyphenList**](https://github.com/gumpcha/my-popclip-extensions/tree/master/HyphenList.popclipext)라는 extension을 만드는 과정을 설명한다.


## 1. 템플릿 준비 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/1be4d61a2b1f5ae92e66564bc365d54a930c9464)

[공식 문서의 General Overview 섹션](https://github.com/pilotmoon/PopClip-Extensions#general-overview)을 보면 PopClip extension은 다섯 가지 타입의 Action이 가능하다.

- Service: 선택된 텍스트를 Mac OS X Service에게 넘김
- AppleScript: 선택된 텍스트를 AppleScript에게 넘김
- Shell Script: 선택된 텍스트를 Shell Script에게 넘김
- URL: 선택된 텍스트를 URL encoding해서 특정 URL 호출
- Keypress: 특정 키 조합을 누름

여기서는 간단하게 `sed`를 쓸 것이기 때문에 Shell Script 타입의 샘플 Extension인 [Say](https://github.com/pilotmoon/PopClip-Extensions/tree/master/source/Say)를 템플릿으로 사용한다.

폴더 이름이 `.popclipext`란 확장자로 끝나야 Finder에서 더블 클릭을 해서 extension을 설치할 수 있으므로 `Say`를 `HyphenList.popclipext`라고 이름을 바꿔서 시작한다.

개인 extension은 디지털 서명 문제 때문에 설치할 때, 설치 여부를 확인하는 창이 계속 뜨는데 이 확인 절차를 건너뛰려면 Terminal 에서 다음 명령어를 치고 PopClip을 재기동 하면 된다.

```sh
$ defaults write com.pilotmoon.popclip LoadUnsignedExtensions -bool YES
```


## 2. Main Script 작성 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/bff167127104692eeb5c5c28f671c7518b9022fa)

1. Main Script 이름은 `add-hyphens.sh`
1. PopClip은 선택된 텍스트를 `$POPCLIP_TEXT` 환경 변수에 담아 `add-hyphens.sh`을 호출
1. `add-hyphens.sh`에서는 `sed`를 써서 `$POPCLIP_TEXT`의 모든 줄 처음에 `- `를 추가
1. 최종 `add-hyphens.sh` 쉘 스크립트는 다음과 같고

    ```sh
    echo "$POPCLIP_TEXT" | sed 's/^/- /'
    ```
1. 테스트는 이렇게 해 본다

    ```sh
    $ POPCLIP_TEXT="123\n456\n789" ./add-hyphens.sh
    - 123
    - 456
    - 789
    $
    ```


## 3. Config.plist 수정

남은 일은 `Config.plist`에 필요한 내용을 하나씩 수정하면 된다.

### Main Script 이름 적용 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/bff167127104692eeb5c5c28f671c7518b9022fa)

작성한 Main Script 이름을 지정해 준다.


### After Action 지정 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/f5262b8915f0fabe1695a8de9bc25b1cffd14899)

Main Script가 실행되기 전/후에 수행할 PopClip의 [Before/After Action]( https://github.com/pilotmoon/PopClip-Extensions#before-and-after-keys) 중, 스크립트가 변환한 텍스트를 clipboard에 복사해 넣으면 되므로, `copy-result`로 지정한다.

실행했을 때, 성공하면 `Copied`가 표시되고 에러가 난다면 `X`가 표시된다.

### Extension Identifier 변경 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/efa64f2889b85e745be0c06158f41032cc7ffce8)

Reverse DNS 스타일의 prefix 지정.

### Name/Description - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/56d20014e1d606b78ee2d72d2bcae7036a09c3e6)

이름, 설명 지정.

### Icon 변경 - [관련 커밋](https://github.com/gumpcha/my-popclip-extensions/commit/0dcc7c462008285f2faf523e4b6b32d51fa1ad82)

아이콘은 PopClip popup 화면이나 설정 화면에서 필요하고, 최소 256x256 픽셀의 PNG 파일이면 된다. 여기선 http://simpleicon.com/list.html 의 아이콘을 가져다 쓴다.



## 4. 참고

* [Official PopClip Extensions Document](https://github.com/pilotmoon/PopClip-Extensions)
* [Create Your Own Custom Extension for PopClip](http://computers.tutsplus.com/tutorials/create-your-own-custom-extension-for-popclip--mac-50637)
