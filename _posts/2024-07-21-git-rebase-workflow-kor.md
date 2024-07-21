---
title: Git Rebase & Fast-forward 사용법
date: 2024-07-21 01:36:00 +0900
categories: [SOFTWARE]
tags: [git]
---

## 나한테 이 글이 필요할까?
> 혼자서 사용하는 Git은 어느 정도 할 줄 알지만, 여러 명이서 하는 Git이 익숙하지 않다면! 이 글이 많은 도움이 될 거예요.
{: .prompt-info}


## Git 브랜치 전략이 무엇이고 왜 필요한가?

---

여러 사람이 동시에 작업하는 소프트웨어 개발 환경에서는 필연적으로 여러 사람이 하나의 코드 저장소에서 서로 다른 기능을 개발하게 됩니다.
이 과정에서 코드 사이의 충돌이 발생하고, 여러 코드를 하나로 모아야 하기 때문에 혼자서 작업할 때와는 전혀 다른 버전 관리를 필요로 합니다.
따라서 서로 다른 코드를 병합하기 위한 하나의 브랜칭 전략을 정하고, 팀의 모든 구성원이 이에 동의하는 과정이 필요합니다.

Git의 "브랜칭 전략"이란 두 개의 서로 다른 코드, 또는 브랜치를 하나로 합치는 방법론입니다.
브랜칭 전략이 필요한 상황은 예컨데 다음과 같습니다.
`main` 브랜치에서 분기된 `feature_branch`가 있고, `feature_branch`는 새로운 피처에 해당하는 커밋을 푸시하여 원래 분기된 지점에서부터
변화가 생겼다고 생각해보겠습니다. 이때 당연하지만 `main` 브랜치에도 `feature_branch`와 분기된 이후에 새로운
커밋이 있을 수 있습니다. 이 상황은 다음과 같습니다. 이 상황에서는 두 브랜치를 바로 합칠 수 없습니다.

![img_1.png](/assets/img/2024-07-21-git-rebase-workflow/img_1.png){: width="400" }

이렇게 갈라진 두 브랜치를 어떻게 합칠 수 있을까요?
이 질문을 해결하기 위한 방법이 바로 "병합(**merge**)" 전략과 "리베이스 후 패스트 포워드(**rebase and fast-forward**)" 전략입니다.
각 팀의 상황이나 내부 회사 정책에 따라 둘 중 하나를 선택해 활용하면 됩니다.

이 글에서는 두 가지 코드 병합 전략을 비교합니다. 이 중에서도 "rebase and fast-forward" 전략을 직접 사용하는 예시를 소개한 후, 
이 전략의 장단점을 논의하겠습니다.

> 이 글의 예시는 [제 레포](https://github.com/notiona/git-rebase-ff-workflow)에서 확인하실 수 있습니다.
{: .prompt-info}


## How are the strategies different?

이 글의 예시에서는 설명의 편의를 위해 자동 병합/리베이스가 가능하다고 가정하겠습니다.
즉, `main`과 `feature_branch`사의의 충돌 (컨플릭트)가 없다는 뜻입니다.

### Merge Strategy:

![img_2.png](/assets/img/2024-07-21-git-rebase-workflow/img_2.png){: width="400" }

Merge 전략은 `main` 브랜치에 `Merge branch feature_branch`라는 새로운 커밋을 생성하여 두 브랜치의 변경 사항을 하나로 합치는 전략입니다.

한 가직 주목한 점은, 병합이 끝난 후에도 `feature_branch`가 여전히 `main` 브랜치와 갈라져 있다는 점입니다.
`main` 브랜치의 관점에서 보면, 브랜치가 병합될 때 `feature_branch`의 길이와 관계없이 `feature_branch`의 모든 변경 사항을 포함하는
단 하나의 머지 커밋만 새로 추가됩니다.


### Rebase & Fast-forward Strategy

Rebase & Fast-forward 전략은 가장 최신 `main` 브랜치 위에 `feature_branch`의 모든 커밋을 하나하나 적용 시켜 나가는 방식입니다.
Git 용어로 이것을 "rebase"라고 하며,
이는 `feature_branch`의 "분기점(base)"를 `main commit 1`에서 `someone's commit 2`(최신 `main` 브랜치의 끝)
로 이동시킨다는 점에서 그 의미를 유추할 수 있습니다.

![img_3.png](/assets/img/2024-07-21-git-rebase-workflow/img_3.png){: width="400" }

그 다음, 커밋 히스토리를 다음과 같이 일직선으로 폅니다. Git 용어로 이것을 "fast forwarding"이라고 하는데,
이 용어는 Git이 커밋 히스토리를 추적하는 방식과 커밋간 전환하는 방식과 관련이 있습니다.
특정 버전이나 커밋을 체크아웃할 때, Git은 각 커밋의 변경사항을 일일이 적용해 나가며 브랜치를 체크아웃 하는 것이 아닌,
"HEAD"를 C/C++ 포인터가 다른 값을 가리키는 것과 유사하게 HEAD만 해당 커밋을 가리키도록 합니다.

이전에는 최신 `main` 브랜치가 `someone's commit 2`를 가리키고 있었지만, `feature_branch`의 커밋이 리베이스되면서 모든
커밋이 일직선에 놓이게 되었습니다. 이제 Git이 이러한 변경 사항을 통합하기 위해 해야 할 일은 HEAD를 `my feature 2`로 이동(앞으로 밀기, fast-forward)하는
것뿐입니다. 이 과정을 Git에서는 "fast-forwarding"이라고 합니다.

![img_4.png](/assets/img/2024-07-21-git-rebase-workflow/img_4.png){: width="400" }

개인적으로 저는 merge 전략보다 이 전략을 선호하는데, 위 그림처럼 병합 후 깔끔한 일직선의 커밋 히스토리를 남길 수 있기 때문입니다.
물론 팀마다 상황별로 적절한 방법이 다르겠지만, 제 개인적인 경험 상 rebase 전략이
특히 유용하다고 생각하는 예시는 `git bisect` 작업을 수행할 때입니다. 

`git bisect` 작업은 이진 검색을 기반으로 코드베이스에 버그를 도입한 최초의 커밋을 찾을 때 사용됩니다.
Merge 전략에서는 feature 브랜치의 모든 변경 사항이 `main` 브랜치의 단 하나의 merge 커밋에 전부 포함되기 때문에
`git bisect`의 강점을 충분히 발휘할 수 없습니다.

또한, merge 전략에서는 `main` 외의 브랜치에서 분기된 브랜치들이 있거나, feature 브랜치에서 최신 `main`의 작업 사항을
따라잡기 위해 머지 커밋이 많이 생긴다면 브랜치 히스토리가 정말 정말!! 복잡해질 수 있습니다.
이러한 상황을 흔히 "머지 지옥"이라고 부릅니다. 🥲

![img_5.png](/assets/img/2024-07-21-git-rebase-workflow/img_5.png)

물론, 여기서 저는 제가 더 좋아하는 방법의 좋은 점만 소개하고 있지만, 머지 전략도
당연히 모든 구성원이 책임감 있게 사용한다면 충분히 효과적으로 사용될 수 있습니다.
그럼에도 불구하고 이 글에서는 제가 더 선호하는 **리베이스 및 빠른 병합 전략**과 그 일반적인 작업 방식에 대해 다뤄보겠습니다.
리베이스 방식의 단점과 주의사항은 이 글 막바지에서 다루겠습니다.


## 로컬 작업 환경

---
+ macOS Sonoma 14.2.1
+ OSX arm64 (Apple Silicon)
+ zshell
+ git version 2.38.1
+ 2024년 7월 기준 작동하는 것 확인


## 배경지식

---
> 이 글을 제대로 이해하기 위해서는 다음과 같은 배경 지식 및 스킬이 필요해요.
{: .prompt-info }

+ 기초 Git 사용법 (git pull, pull requests, git merge, git rebase 와 같이 협업 환경에서 git을
사용할 때를 제외한 Git 지식)


## Rebase and Fast-forward Workflow

---

### 1. 최신 Main 브랜치 받아오기
컨플릭트가 나는 상황을 최대한 방지하기 위해 브랜치를 만들기 전 최대한 최신 main 브랜치를 가져오는 것이 좋습니다.

```shell
git checkout main
git pull
```
### 2. 피처 브랜치 만들기

```shell
git checkout -b feature_branch
```

### 3. 내 코드 작업하기!

```shell
# 좋은 코드 쓰기...
```

### 4. 로컬에 git add, git commit

```shell
git add .
git commit -m "Useful commit message"
```

여기까지, Git 커밋 히스토리를 앞서 본 예제처럼 설정했습니다.
유일한 차이점은 두번째 CLI 형 그림에서는 Git log의 위쪽이 가장 최신 커밋이라는 점입니다.

![img_1.png](/assets/img/2024-07-21-git-rebase-workflow/img_1.png){: width="400" }
![img_6.png](/assets/img/2024-07-21-git-rebase-workflow/img_6.png)

### 5. 커밋을 논리적 단위로 나누기 위해 커밋 squashing

깃 커밋은 논리적 요소로 나뉜 것이 좋지만, 가끔 그 원칙을 지키지 못하는 경우가 생깁니다.
가령 사소한 linting 이나 작은 수정 작업으로 인해 남기고 싶지 않은 조그마한 커밋이 생길 수 있습니다.
이 경우에는, 사소한 린팅 작업한 한 커밋이 있는데, 이를 가장 최신 커밋에 뭉게버리고("squash") 싶다고 가정해봅시다.

![img_7.png](/assets/img/2024-07-21-git-rebase-workflow/img_7.png)

이 경우 "lint" 커밋은 이전 커밋인 "my feature 2"에 스쿼시 하고 싶습니다.

```shell
git rebase -i HEAD~n # n은 고치고 싶은 최근 커밋의 개수
```

![img_8.png](/assets/img/2024-07-21-git-rebase-workflow/img_8.png)

이 명령어를 실행하면, 가장 최근의 n개의 커밋이 있는 텍스트 편집기 창이 열립니다.
이전 커밋에 스쿼시하고 싶은 커밋에 대해, ‘p’(pick)를 ‘f’(fixup)으로 변경하면 됩니다.

![img_9.png](/assets/img/2024-07-21-git-rebase-workflow/img_9.png)

저장하고 편집기를 종료하면, ‘f’로 표시한 커밋들이 이전 커밋에 뭉게져 마치 두개의 커밋이 원래 하나였던 것처럼 보입니다!

한 가지 주목할 점은, "my feature 2"의 커밋 해시가 바뀌었다는 점입니다.
리베이스를 통해 이 커밋은 커밋 메시지만 같지 완전히 다른 커밋이 되었습니다.
이는 `git rebase`의 중요한 특징입니다. `git rebase`는 커밋 히스토리를 재작성하는 기능으로, 커밋 히스트로리를 다시 쓴다는 것은
이것이 파괴적인 (경우에 따라 비가역적인) 연산이라는 의미입니다.

![img_10.png](/assets/img/2024-07-21-git-rebase-workflow/img_10.png)


### 6. 최신 main 가져오기

위 작업을 하는 사이에도 `main` 브랜치에 새 커밋이 들어왔을 수도 있으니, 한번 더 pull을 진행합니다.

```shell
git pull
```

### 7. main 브랜치에 rebase

이 rebase 명령어가 가장 최근 `main` 브랜치 위에 `feature_branch`에서 이루어진 모든 변경 사항을 적용하는 역할을 수행합니다.
즉 다음 두 개의 그림처럼 커밋 히스토리가 바뀝니다.

```shell
git rebase main
```

![img_3.png](/assets/img/2024-07-21-git-rebase-workflow/img_3.png){: width="400" }
![img_4.png](/assets/img/2024-07-21-git-rebase-workflow/img_4.png){: width="400" }

이제 `feature_branch`의 모든 변경 사항이 가장 최신 `main` 브랜치 위에 일직선으로 놓이게 되었습니다.

![img_11.png](/assets/img/2024-07-21-git-rebase-workflow/img_11.png)

### 8. Remote에 피처 브랜치 push

이제 일직선이 된 피처 브랜치를 remote로 푸쉬합니다.

```shell
git push -u origin feature_branch
```

> 이미 피처 브랜치를 원격에 푸시한 상태에서, 위에서 한 것처럼 최신 `main` 브랜치에 대해 리베이스를 재차 수행하면,
> 피처 브랜치의 커밋들은 다시 모두 새로운 Git 해시값를 가지게 됩니다. 이 경우 새로 리베이스된 변경 사항을 원격과
> 동기화하기 위해서는 `git push`가 아닌 강제 푸쉬(`git push -f`)를 해야만 새로 리베이스 된 피처 브랜치를 원격에 반영할 수 있습니다.
{: .prompt-warning}

> 대부분의 경우처럼 피처 브랜치를 나만 작업하고 있다면, 얼마든지 강제 푸쉬(force push)를 해도 됩니다.
> 하지만 여러 사람이 동일한 피처 브랜치에서 작업하고 있는 경우, 강제 푸쉬를 진행하는데 있어 **매우매우매우!! 조심**해야 합니다.
> 동일한 브랜치에서 작업 중인 동료들과 논의하고, 뒤에서도 후술하겠지만 내 명령어의 결과로 인해 누군가의 작업물이 사라지지 않도록
> 각별한 주의를 기울여야 합니다.
{: .prompt-danger}

### 9. GitHub에서 PR 만들기

![img_12.png](/assets/img/2024-07-21-git-rebase-workflow/img_12.png)

“Compare & pull request”를 클릭 해 pull request를 생성합니다.

![img_13.png](/assets/img/2024-07-21-git-rebase-workflow/img_13.png)

작업에 따라 적절한 Pull Request 내용을 작성하고, 리뷰어를 지정하고 라벨, 프로젝트 등을 추가합니다.


### 10. main 브랜치에 rebase 방식으로 피처 브랜치 병합하기

리뷰어가 PR을 승인하고, 모든 검사가 통과했고, 머지 컨플릭트가 없는 경우,
"Rebase and merge" 옵션을 선택하고 병합을 확인합니다.

![img_15.png](/assets/img/2024-07-21-git-rebase-workflow/img_15.png)

> "Create a merge commit"은 위에서 언급한 머지 전략입니다. 그 외의 옵션인 "Squash and merge"은 "Rebase and merge"와
> 완전히 동일하지만, 풀 리퀘스트에 있는 모든 커밋이 단 하나의 커밋으로 스쿼시 되어 머지된다는 점에서만 차이가 있습니다.
{: .prompt-info}

변경 사항이 성공적으로 병합된 후에는 피처 브랜치를 삭제할 수 있습니다.

이제 원격에도 깔끔하고 일직선의 커밋 히스토리가 만들어졌습니다!

![img_16.png](/assets/img/2024-07-21-git-rebase-workflow/img_16.png)

> 이 글의 예시는 [제 레포](https://github.com/notiona/git-rebase-ff-workflow)에서 확인하실 수 있습니다.
{: .prompt-info}

## Rebase fast-forward 전략의 단점과 주의점

---

이제 전체 이 rebase and fast-forward 전략의 전체 워크플로우를 설명했으니, 제 경험을 바탕으로 이 전략의 단점과 주의사항에 대해 논의하겠습니다.
일직선의 커밋 히스토리를 만들기 위해 감수해야 하는 비용이라고 저는 생각하고 있습니다.

### 최신 main을 따라잡을 때 발생하는 컨플릭트

피처 브랜치에서 작업하는 동안, 당연하지만 remote의 최신 main 브랜치에 새로운 커밋이 있을 수 있습니다.
이를 따라잡기 위해, 모든 사용자는 `git rebase origin/main` 명령어를 자주 실행해줘야 합니다.
만약 충돌이 발생한다면 이를 해결해야 하는데, 리베이스 전략을 사용할 때의 충돌 해결이 머지 전략보다 훨씬 더 번거로울 수 있습니다.
(Git 컨플릭 해결은 이 글의 주제를 벗어나기에 더 깊게 서술하진 않겠습니다) 리베이스 전략에서는 최근 `main` 위에 피처 브랜치의 커밋을 하나하나 적용하는
특성 상 피처 브랜치의 각 커밋마다 발생할 수 있는 컨플릭트를 하나하나 해결해야 하며,
이는 컨플릭트가 발생하더라도 한 번의 병합 커밋으로 컨플릭트를 해결할 수 있는 머지 전략과는 다릅니다.
최악의 경우, 피처 브랜치의 모든 커밋에 대해 컨플릭트를 일일히 해결해야 할 수도 있습니다..! 피처 브랜치의 커밋 개수가 많을수록, 최신 main을 많이 놓쳤을 수록
컨플릭트를 해결하는 비용이 커집니다.

### 여러 명이 같은 피처 브랜치에 작업할 때 서로의 작업물을 날릴 수도 있는 위험

위에서 보았듯이, 리베이스를 수행할 때 리베이스된 브랜치의 모든 커밋이 새로 작성된 것으로 간주되고,
이는 `git rebase`가 머지와 달리 파괴적인 작업이라는 것을 의미합니다. 일반적으로 하나의 피처 브랜치는 한 사람이 사용하지만,
여러 사람이 같은 브랜치에서 작업할 때는 리베이스 전략에 추가적인 주의와 더 많은 소통이 요구됩니다. 

여러 사람이 같은 피처 브랜치에서 작업할 경우, `git push -f`로 인해 이미 원격에 푸시된 다른 사람의 작업을 덮어쓰거나 없애버릴 가능성이 있기 때문입니다..!
항상 `git push -f`는 항상 위험하며 많은 주의와 신중함이 필요합니다. 큰 힘에는 큰 책임이 따릅니다.

## 동일한 영어 포스트

---
[Git Rebase & Fast-forward Workflow Tutorial](https://notiona.github.io/posts/git-rebase-workflow-eng/)


## 참고한 글, 문서

---

- [Git 공식 문서](https://git-scm.com/doc)
- [머지 지옥 이미지](https://www.tugberkugurlu.com/archive/resistance-against-london-tube-map-commit-history-a-k-a--git-merge-hell#google_vignette)


> 아직 부족한 점이 많아 미흡하거나 틀린 내용이 있으면 댓글로 알려주시면 감사하겠습니다. 제안이나 질문도 언제든지 남겨주세요! 🙇‍♂️
{: .prompt-info}
