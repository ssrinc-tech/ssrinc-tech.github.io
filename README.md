# SSR Tech Blog

블로그: https://ssrinc-tech.github.io

## 글 쓰는 법

### 1. 글 파일 만들기

`_posts/` 폴더에 파일을 만듭니다. 이름은 **`날짜-영문-제목.md`**

```
_posts/2026-11-20-spring-boot-mcp-server.md
```

### 2. 맨 위에 머리말, 아래에 본문 작성

```markdown
---
title: Spring Boot로 MCP 서버 만들기
date: 2026-11-20 14:00:00 +0900
categories: [Backend, MCP]
tags: [spring-boot, mcp]
author: jwhan
---

## 배경

본문은 마크다운으로 씁니다.
```

- `author` 는 [`_data/authors.yml`](_data/authors.yml) 의 아이디 (처음이면 본인 추가)
- `date` 가 미래 시각이면 그때까지 글이 안 보입니다

### 3. 이미지

**① 파일 넣기**: 글 이름과 같은 폴더를 만들고 이미지를 넣습니다.

```
assets/img/posts/2026-11-20-spring-boot-mcp-server/inspector.png
```

**② 글에서 불러오기**: 이미지가 나올 자리에 한 줄 씁니다.

```markdown
![Inspector 화면](/assets/img/posts/2026-11-20-spring-boot-mcp-server/inspector.png)
```

- `[ ]` 안은 이미지 설명, `( )` 안은 이미지 파일 위치
- 경로는 꼭 **`/assets/`** 로 시작 (앞의 `/` 를 빼면 이미지가 안 뜸)
- 파일 이름은 영문, 공백 없이

### 4. 올리기

```powershell
git pull
git checkout -b post/spring-boot-mcp-server
git add .
git commit -m "글: Spring Boot로 MCP 서버 만들기"
git push -u origin post/spring-boot-mcp-server
```

GitHub 에서 **Pull Request** 생성 → 리뷰 → **Merge** 하면 1~2분 뒤 블로그에 반영됩니다.

> GitHub 웹에서도 가능: `_posts` → **Add file → Create new file** → 커밋 시 "Create a new branch and start a pull request"

## ⚠️ 주의

공개 저장소입니다. 글과 **스크린샷**에 고객사명, 내부 IP, API 키, 사내 소스코드가 없는지 확인하세요.
