---
title: Mac arm64 (apple silicon)에 protobuf 소스 파일로 설치하기 
date: 2024-01-14 01:45:30 +0900
categories: [TROUBLESHOOTING]
tags: [troubleshooting, protobuf, mac-arm64, arm64]
---

## 문제 상황

---
[Streamlit contribution guide](https://github.com/streamlit/streamlit/wiki/Contributing)의 로컬 개발 환경 세팅을 따라가던 중, 로컬 시스템의 [protobuf](https://protobuf.dev/) 버전과 pip install로 설치된 protobuf의 버전을 일치시켜야 한다는 부분을 읽었지만 난관에 부딪혔습니다.

로컬 시스템이 사용하는 protobuf의 버전은 다음 명령어로 확인할 수 있는데요.
```bash
protoc --version
```

시스템의 protobuf 버전(`25.1`)과 pip install로 설치된 protobuf 버전(`3.20.3`)이 다르다는 점은 금방 깨달았지만, protobuf가 일부 버전에 대해 osx-universal binary나 osx-arm64 버전을 제공하지 않기에 설치에 애를 먹었습니다.

여러 방법을 고민하던 중, protobuf를 소스 파일로부터 설치 하기로 결정했고, 다음과 같은 방식을 통해 protobuf `3.20.3` 버전을 정상적으로 설치할 수 있었습니다. 

이 글이 비슷한 문제를 겪으시는 분들께 조금이라도 도움이 되었으면 좋겠습니다.

## 로컬 작업 환경

---
+ macOS Monterey 12.6.3
+ OSX arm64 (Apple Silicon)
+ zshell
+ 2024년 1월 기준 작동하는 것 확인

## 소스 파일 다운 받기

---
[원하시는 protobuf 릴리즈 페이지](https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3) (제 경우는 `3.20.3`)에서, "Source Code" 라고 표시된 `zip` 또는 `tar.gz` 파일을 다운로드 받습니다.

압축 해제 후, 해당 protobuf 폴더로 들어갑니다.
```bash
cd protobuf-3.20.3/
```

## 소스 파일에서 빌드하기

---

Homebrew를 통해 필요한 빌드 툴을 설치합니다.

```bash
brew install autoconf  
brew install automake  
brew install Libtool
```

다음 명령어를 `protobuf` 디렉토리의 최상위 폴더에서 실행해 protobuf를 로컬에 설치합니다.

```bash
autoreconf -i
./autogen.sh
./configure
make
sudo make install

export PATH=/opt/usr/local/bin:$PATH
```

이제 다시 protobuf 버전 확인을 하면 원하는 버전이 나옵니다.

```bash
protoc --version
# 이제 다음과 같이 나와야 합니다: libprotoc 3.20.3
```

## 동일한 영어 포스트

---
[troubleshooting - installing protobuf from source in mac arm64 (apple silicon)](https://notiona.github.io/posts/protobuf-mac-arm64-eng/)

## 참고한 글, 문서

---
+ [Streamlit Contribution Guide](https://github.com/streamlit/streamlit/wiki/Contributing)
+ [Protobuf version 3.20.3 release page](https://github.com/protocolbuffers/protobuf/releases/tag/v3.20.3)
+ [install-protobuf-on-m1-mac](https://medium.com/@danny4410.eecs04/install-protobuf-on-m1-mac-852e4afa619f)

---

> 아직 부족한 점이 많아 미흡하거나 틀린 내용이 있으면 댓글로 알려주시면 감사하겠습니다. 제안이나 질문도 언제든지 남겨주세요! 🙇‍♂️
{: .prompt-info}
