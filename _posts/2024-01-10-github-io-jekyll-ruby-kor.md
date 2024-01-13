---
title:  Jekyll과 chirpy 테마 활용해 github.io 블로그 셋업하기
date: 2024-01-10 01:47:00 +0900
categories: [TOOLS, BLOG]
tags: [github-io, jekyll, chirpy, blog, ruby, troubleshooting]
---

몇년째 새해 소망으로 `github.io` 기술 블로그를 적어두었는데, 미루고 미루다 드디어 여기까지 오게 되었습니다.

첫 포스팅 주제를 고민하던 중, 마침 그동안 블로그를 셋업하며 찾은 정보를 담으면 좋을 것 같다는 생각이 들어 첫 포스트를 올립니다.

다른 상업적인 솔루션(네이버 블로그, 티스토리 등)에 비해서는 그리 간단하지는 않았지만, 제 취향대로 얼마든 커스터마이징 할 수 있다는 점이 `github.io`의 가장 큰 매력인것 같습니다.

재미있게 읽어주세요! 😄 저와 비슷한 셋업을 찾으시는 분들께 조금이라도 도움이 되었으면 좋겠습니다.

## 로컬 작업 환경

---
+ Mac OS Monterey 12.6.3
+ zshell
+ 2024년 1월 기준 작동하는 것 확인

## 배경지식

---
> 이 글을 제대로 이해하기 위해서는 다음과 같은 배경 지식 및 스킬이 필요해요.
{: .prompt-info }
+ 간단한 Git 커맨드 (git add, commit, push, pull)
+ GitHub 계정 만드는 방법

## 테마 고르기

---
제가 생각했을 때 `github.io`의 가장 큰 장점은 100% 커스터마이징이 가능하면서도 이미 어느 정도 잘 디자인된 형식을 그대로 이용할 수 있다는 것입니다.

시작하기 전에 테마를 정해야 하는데..! 여기서도 인기 있는 테마는 몇개 정해져 있더라고요. 인기 있는 무료 테마 목록이 정리된 사이트 몇개만 공유해봅니다.
- [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
- [https://jekyll-themes.com/](https://jekyll-themes.com/)
- [https://jekyllthemes.io/free](https://jekyllthemes.io/free)

다양한 후보가 있기 때문에 선택 기준을 명확히 정하는 것이 좋습니다. 저 같은 경우 다음과 같은 사항을 중점적으로 고려했습니다.
- 심플한 (지나치게 화려하지 않은) UI
- LNB(좌측 네비게이션 바)에 카테고리 필드가 존재할 것
- 깔끔한 기본 글꼴, 코드 글꼴
- 깔끔한 한국어 글꼴
- "About" 페이지가 있을 것

제 최종 선택은 [chirpy](https://chirpy.cotes.page/)였습니다!

그리고 이 테마를 사용하려면 ruby, jekyll, 그리고 git이 필요합니다.

## ruby, jekyll 설치

---
이번 기회에 찾아보니 `github.io` 호스팅 쪽은 대부분 ruby의 jekyll이라는 패키지 위에 대부분 만들어져 있어서, 해당 툴들(ruby, jekyll)을 설치해야 합니다.

> 이 글을 읽는 많은 분들 처럼 저도 루비 경험이 1도 없어서,, 크게 걱정하지 않으셔도 됩니다. 
{: .prompt-warning}

[루비 공식 문서](https://www.ruby-lang.org/en/downloads/)에 의하면, 루비를 설치하는 좋은 방법 중에 하나가 [rbenv](https://github.com/rbenv/rbenv), 라서 이걸로 진행했습니다.

> TIL: macOS 시스템은 기본적으로 루비가 설치되어 있지만, 루비 개발자들은 기본 시스템 루비를 쓰는 걸 극구 말리더라고요. [그 이유만 구구절절하게 설명한 웹사이트](https://dontusesystemruby.com/#/) 도 있습니다. 😅
{: .prompt-info}

파이썬에 익숙한 저로서는 한 시스템의 여러 언어 버전을 지원한다는 점에서 syntax도 그렇고 rbenv가 python의 pyenv와 상당히 유사해 보였습니다.

```bash
brew install rbenv ruby-build
```

다음 명령어로 stable version을 고르고 다운 받으면 됩니다. 저는 `3.1.4`를 골랐습니다.

```bash
# list latest stable versions:
rbenv install -l
```
```bash
# install a Ruby version:
rbenv install 3.1.4
```

방금 설치한 루비 버전을 global 버전으로 세팅하고, ruby가 제대로 된 루비를 바라보고 있는지 확인합니다.

```bash
rbenv global 3.1.4
```
```bash
# /usr/bin/ruby 이 아닌 방금 설치한 /Users/username/.rbenv/shims/ruby 이 나와야 합니다
which ruby
```

저는 zsh를 쓰고 있어서, `rbenv init`을 `.zshrc`에 추가해 터미널을 새로 켜도 적용되게 설정했습니다.
```bash
echo 'eval "$(/opt/homebrew/bin/rbenv init - zsh)"' >> ~/.zshrc
```

버전 확인을 해줍니다. Gem은 루비의 패키지 매니저 (python의 pip) 같고, ruby를 설치할 때 이미 함께 설치되었을 겁니다.

```bash
ruby -v
gem -v
```
이제 jekyll을 설치하면 됩니다.
```bash
gem install jekyll
```

## 테마 레포 Fork하기

---
여기서부터는 워낙 [Chirpy 공식 가이드](https://chirpy.cotes.page/posts/getting-started/)가 잘 되어있어 가이드를 따라 가시면 됩니다.

Chirpy에서 제안하는 두 셋업 방법 중(chirpy starter, GitHub fork), 저는 커스터마이징을 위해 후자를 골랐습니다. Fork 진행 후 나의 GitHub 계정에 있는 레포를 clone 해 이제부터는 로컬에서 편한 IDE로 작업하시면 됩니다.

> Fork 후 `jekyll-theme-chirpy`와 같이 된 레포 이름을 `[GitHub username].github.io`의 형태로 꼭 바꾸어야 해요!
{: .prompt-info}

> `bash tools/init` 명령어 이후 로컬 `master`와 `origin/master` 가 diverge 되었고 conflict가 발생한다고 하면 불필요한 파일을 제거한 YOURS(local `master`) 를 accept해 conflict를 해결하면 됩니다.
{: .prompt-warning}

## 로컬 환경 작업 시 페이지 확인

---
다음 명령어를 통해 `localhost:4000`에 업데이트 되는 코드를 볼 수 있습니다.

```bash
bundle exec jekyll s
```

## 배포

---
실제 배포는 이미 chirpy에서 셋업을 잘 해놓아서 `master` 브랜치에 push 하는 것만으로도 GitHub Action을 trigger 시켜 자동으로 CICD가 진행되지만, repo 상단의 GitHub Actions에 들어가 수동으로 배포를 진행할 수도 있습니다.

## 선택 사항: 좌측 사이드바 커스터마이징

---
지금까지만 해도 충분히 깔끔하지만, 저는 개인적으로 왼쪽 사이드바를 다음과 같이 조금 더 커스터마이징 하고 싶었습니다.
+ 배경 이미지 넣기
+ 텍스트 및 버튼 색 연하게 바꾸어 가독성 높이기

저는 CSS를 잘 몰라서 주로 `_sass/addon/commons.scss` 파일의 요소를 한 줄 한 줄 수정해보고 로컬 환경에서 확인해보면서,,, 각 selector의 효과를 확인하며 진행했습니다.

구체적인 수정사항은 [제 커밋](https://github.com/notiona/notiona.github.io/commit/6ea64ed8c7bcc6714756eb6c1d8c611ef07d1e2a)에서 확인하실 수 있습니다.

## 글쓰기

---
이제 새로운 글을 써야 하는데, 이 부분 또한 [chirpy의 새 글 가이드](https://chirpy.cotes.page/posts/write-a-new-post/)를 참고하시면 됩니다.  chirpy와 jekyll에 국한된 문법이 조금 있지만 모든 글이 마크다운 형식이라 큰 진입 장벽은 아닌 것 같습니다.

## 동일한 영어 포스트

---
[github.io blog setup using jekyll & chirpy theme](https://notiona.github.io/posts/github-io-jekyll-ruby-eng/)

## 참고한 글, 문서

---
+ [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
+ [https://www.ruby-lang.org/en/downloads/](https://www.ruby-lang.org/en/downloads/)
+ [https://chirpy.cotes.page/posts/getting-started/](https://chirpy.cotes.page/posts/getting-started/)
+ [https://chirpy.cotes.page/posts/write-a-new-post/](https://chirpy.cotes.page/posts/write-a-new-post/)

---

> 아직 부족한 점이 많아 미흡하거나 틀린 내용이 있으면 댓글로 알려주시면 감사하겠습니다. 제안이나 질문도 언제든지 남겨주세요! 🙇‍♂️
{: .prompt-info}
