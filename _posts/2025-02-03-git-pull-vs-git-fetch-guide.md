---
layout: post
title: "git pull vs git fetch: 차이점과 활용법"
date: 2025-02-03
categories: [Git]
tags: [git, git-commands, git-pull, git-fetch, version-control]
description: "Git을 사용할 때 git pull과 git fetch는 자주 쓰이지만, 이를 처음 접하는 개발자들은 두 명령어의 차이를 자주 혼동합니다. 이 글에서는 두 명령어의 개념과 차이점을 명확하게 설명하고, 적절한 사용 방법을 살펴보겠습니다."
keywords: [git pull, git fetch, git 명령어, git 버전 관리, git merge, git rebase, 깃 사용법]
image:
  path: /assets/img/posts/2025-02-03-git-pull-vs-git-fetch-guide/git.png
  alt: "Git 로고 이미지"
---

# git pull vs git fetch: 차이점과 활용법

Git을 사용할 때 `git pull`과 `git fetch`는 자주 쓰이지만, 이를 처음 접하는 개발자들은 두 명령어의 차이를 자주 혼동합니다.

이 글에서는 두 명령어의 개념과 차이점을 명확하게 설명하고, 적절한 사용 방법을 살펴보겠습니다.

## 1. `git fetch`란?

`git fetch`는 원격 저장소의 변경 사항을 가져오지만, 이를 즉시 로컬 브랜치에 병합하지 않습니다. 즉, 최신 변경 사항을 확인하고 싶지만, 직접 병합할지 여부를 결정하려는 경우에 유용합니다.

`git pull`에 비해 안정적인 방식으로 원격 저장소의 변경 사항을 가져옵니다.

### 사용법

```bash
git fetch origin
```

위 명령어는 `origin`(기본 원격 저장소)에서 최신 변경 사항을 가져오지만, 현재 작업 중인 브랜치에는 영향을 주지 않습니다.

### 주요 특징

- 원격 저장소의 최신 상태를 로컬로 동기화
- 현재 브랜치를 변경하지 않음
- 변경 사항을 직접 확인한 후 수동으로 병합 가능

## 2. `git pull`이란?

`git pull`은 `git fetch`와 [`git merge`](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge-%EC%9D%98-%EA%B8%B0%EC%B4%88)를 한 번에 수행하는 명령어입니다. 즉, 원격 저장소의 변경 사항을 가져온 뒤 자동으로 현재 브랜치와 병합합니다.

`git fetch`보다 빠르게 최신 변경 사항을 반영할 수 있지만, 자동 병합이 이루어지므로 예상치 못한 충돌이 발생할 가능성이 있습니다.

### 사용법

```bash
git pull origin main
```

이 명령어는 `origin` 저장소의 `main` 브랜치에서 변경 사항을 가져오고, 현재 체크아웃한 브랜치에 병합합니다.

### 주요 특징

- 원격 저장소의 변경 사항을 가져오고 즉시 병합
- 빠른 업데이트 가능하지만, 예상치 못한 충돌 발생 가능
    - [Git의 Merge conflict와 이를 해결하는 방법](https://www.freecodecamp.org/korean/news/how-to-resolve-merge-conflicts-in-git/)

## 3. `git fetch` vs `git pull` 차이 비교

| 기능 | `git fetch` | `git pull` |
| --- | --- | --- |
| 변경 사항 가져오기 | ✅ 있음 | ✅ 있음 |
| 자동 병합 | ❌ 하지 않음 | ✅ 병합 시도함 |

## 4. 언제 `git fetch`와 `git pull`을 사용해야 할까?

- **`git fetch` 사용 시기**:
    - 원격 저장소의 변경 사항을 확인하고 싶을 때
    - 자동 병합 없이 수동으로 코드 리뷰 후 병합하고 싶을 때
- **`git pull` 사용 시기**:
    - 빠르게 최신 변경 사항을 반영해야 할 때
    - 협업 프로젝트에서 빈번한 업데이트가 필요할 때

## 5. 활용

### `git fetch` 후 변경 사항 확인

```bash
git fetch origin
```

이후 원격 브랜치와의 차이를 비교:

```bash
git diff origin/main
```

변경된 커밋 로그만 간략히 확인하려면:

```bash
git log --oneline main..origin/main
```

### `git pull`로 최신 변경 사항 반영

```bash
git pull origin main
```

이 명령어는 최신 변경 사항을 가져오고 병합까지 수행합니다. 만약 충돌 없이 깔끔한 히스토리를 유지하고 싶다면 `--rebase` 옵션을 사용할 수도 있습니다.

```bash
git pull --rebase origin main
# (git fetch + git rebase origin/main)과 동일
```

## 결론

`git pull`과 `git fetch`는 유사한 기능을 수행하지만, 병합 여부에서 큰 차이가 있습니다. 실무에서는 충돌을 방지하기 위해 `git fetch`로 먼저 변경 사항을 확인한 후, 필요에 따라 `git merge`나 `git pull --rebase`를 사용하는 것이 좋습니다. 물론 프로젝트의 특성과 상황에 맞게 적절한 명령어를 선택하는 것이 가장 중요합니다.

## Reference

- [https://git-scm.com/docs/git-pull](https://git-scm.com/docs/git-pull)
- [https://git-scm.com/docs/git-fetch](https://git-scm.com/docs/git-fetch)
- [https://www.freecodecamp.org/korean/news/git-fetch-vs-pull/](https://www.freecodecamp.org/korean/news/git-fetch-vs-pull/)
- [https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch](https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch)