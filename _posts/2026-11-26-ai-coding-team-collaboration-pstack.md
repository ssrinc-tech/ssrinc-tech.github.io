---
title: "AI 코딩 도구를 활용한 팀 협업 1편 — pstack으로 검증 절차 공유하기"
description: "Claude Code에서 pstack으로 프로젝트의 검증 스킬을 만들고, 팀원이 같은 절차를 쓰고 개선할 수 있도록 Git으로 관리하는 방법을 살펴봅니다."
date: 2026-11-26 18:00:00 +0900
categories: [Development, AI]
tags: [ai-coding, claude-code, pstack, git]
published: false
author: seminkim
---

## 들어가며

AI 코딩 도구에 버그 수정을 맡길 때마다 같은 말을 덧붙인다고 해 보겠습니다.

> 기존 API 응답 형식은 유지해 주세요. 수정하기 전에 버그를 재현하고, 수정한 뒤에는 샘플 데이터로 앱을 띄워 같은 동작을 다시 확인해 주세요.

같은 저장소를 쓰는 팀원도 새 대화를 열 때마다 이 규칙과 절차를 설명합니다. 누군가는 앱을 직접 띄워 보고, 누군가는 테스트 결과만 PR에 남깁니다. 리뷰어는 그때마다 어떤 조건에서 무엇을 확인했는지 다시 묻게 됩니다.

‘AI 코딩 도구를 활용한 팀 협업’ 시리즈에서는 이렇게 개인의 대화에 흩어져 있던 규칙과 작업 절차를 저장소에 옮기고, 팀원이 Git으로 함께 관리하는 방법을 살펴봅니다. 첫 편에서는 **pstack을 써서 앱의 검증 절차를 팀이 재사용할 수 있는 파일로 만드는 과정**을 다룹니다.

## 1. pstack이란?

pstack은 Lauren Tan(`poteto`)이 자신의 개발 방식을 정리해 공개한 AI 코딩 에이전트용 플러그인입니다. 에이전트가 따를 작업 절차(플레이북), 특정 작업을 맡길 때 쓰는 지침(스킬), 변경 결과를 실행해서 확인하는 등의 작업 원칙을 묶었습니다. 이 글의 명령과 파일 경로는 Claude Code 기준입니다. [pstack 원본 저장소 README](https://github.com/cursor/plugins/blob/main/pstack/README.md), [pstack의 Claude Code 어댑터](https://github.com/cursor/plugins/blob/main/pstack/docs/harness/claude-code.md)

Claude Code에서 `/pstack:poteto-mode`로 작업을 요청하면, pstack은 요청에 맞는 플레이북을 고르고 각 단계에서 필요한 스킬을 사용합니다. 버그 수정이라면 먼저 증상을 재현하고 원인을 찾은 뒤, 수정한 결과를 같은 방법으로 확인하는 흐름입니다. 작업을 요청할 때 ‘무엇을 확인하면 끝인지’까지 적어 두면, 리뷰어도 같은 기준으로 결과를 확인할 수 있습니다. [pstack의 작업 요청 가이드](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/02-poteto-mode.md), [Claude Code용 버그 수정 플레이북](https://github.com/cursor/plugins/blob/main/harness/pstack/claude-code/skills/poteto-mode/playbooks/bug-fix.md)

## 2. 저장소에 팀의 규칙과 검증 절차 남기기

‘기존 API 응답 형식을 유지한다’처럼 여러 작업에 공통으로 적용할 규칙은 저장소 루트의 `CLAUDE.md`에 적을 수 있습니다. ‘샘플 데이터로 앱을 띄우고, 어떤 화면에서 어떤 결과를 확인한다’처럼 순서가 있는 일은 프로젝트 스킬로 둘 수 있습니다. Claude Code는 `.claude/skills/<스킬-이름>/SKILL.md`를 프로젝트 스킬로 읽습니다. [Claude Code 프로젝트 지침 문서](https://code.claude.com/docs/en/memory#choose-where-to-put-claude-md-files), [Claude Code 스킬 문서](https://code.claude.com/docs/en/skills)

pstack의 `/pstack:create-verification-skill`은 저장소를 살펴보고 앱을 시작하는 방법, 기능을 실행하는 방법, 결과를 남기는 방법을 프로젝트 스킬로 정리합니다. 기존 테스트나 실행 스크립트가 있으면 활용하고, 생성 과정에서 대표 기능 하나를 실제로 실행해 절차가 동작하는지 확인합니다. 이 스킬을 Git에 커밋하면 다음 팀원도 같은 절차를 불러다 쓸 수 있습니다. [pstack의 Claude Code 어댑터](https://github.com/cursor/plugins/blob/main/pstack/docs/harness/claude-code.md), [검증 스킬 생성 절차](https://github.com/cursor/plugins/blob/main/harness/pstack/claude-code/skills/create-verification-skill/SKILL.md)

## 3. 가상의 재고 관리 앱에 적용해 보기

`inventory-web`이라는 가상의 저장소를 예로 들어 보겠습니다.

1. **검증 스킬을 만듭니다.** `inventory-web`에서 Claude Code를 열고 대화창에 아래 두 명령을 차례로 입력합니다. 첫 명령으로 pstack을 배포하는 마켓플레이스를 등록하고, 두 번째 명령으로 플러그인을 설치합니다. [pstack 공식 설치 안내](https://github.com/cursor/plugins/blob/main/pstack/README.md#install), [Claude Code 플러그인 설치 문서](https://code.claude.com/docs/en/plugins/install)

   ```text
   /plugin marketplace add cursor/plugins
   /plugin install pstack@cursor-plugins
   ```

   설치 후 새 세션에서 `/pstack:setup-pstack`으로 모델을 설정합니다. 설정 중에 검증 스킬 생성 제안이 나오면 수락하거나, `/pstack:create-verification-skill`을 직접 실행합니다. 생성된 스킬 경로를 `.claude/skills/verify-inventory-web/SKILL.md`라고 가정하겠습니다. 여기에는 샘플 데이터로 앱을 띄우는 방법과 재고 목록을 검증하는 절차가 들어갑니다. [pstack 초기 설정 가이드](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/01-setup.md), [pstack의 Claude Code 어댑터](https://github.com/cursor/plugins/blob/main/pstack/docs/harness/claude-code.md#paths)

2. **다른 팀원이 검증 절차를 리뷰합니다.** 스킬 파일을 PR에 올립니다. 리뷰어가 자기 환경에서 따라 해 보니 샘플 데이터가 한 페이지뿐이라 페이지 이동을 확인할 수 없습니다. 여러 카테고리의 상품을 넣고, 한 카테고리만 골라도 두 페이지 이상 나오도록 실행 절차를 보완합니다. 리뷰어가 다시 확인한 뒤 병합합니다.
3. **다음 버그 수정에서 같은 스킬을 씁니다.** pstack을 설치한 팀원이 ‘2페이지로 이동하면 카테고리 필터가 풀린다’는 버그를 맡았습니다. 팀원은 `/pstack:poteto-mode`를 실행하면서 저장소에 있는 검증 스킬을 사용하라고 요청합니다. 검증만 따로 할 때는 Claude Code에서 `/verify-inventory-web`을 직접 실행할 수 있습니다. [pstack의 검증 가이드](https://github.com/cursor/plugins/blob/main/pstack/docs/guide/06-verify-and-ship.md#create-a-project-verification-skill), [Claude Code 스킬 호출 문서](https://code.claude.com/docs/en/skills#how-a-skill-gets-its-command-name)

   ```text
   /pstack:poteto-mode 카테고리 필터를 적용하고 2페이지로 이동하면 필터가 풀리는 버그를 재현하고 수정해 주세요.
   verify-inventory-web 스킬로 2페이지에서도 선택한 카테고리의 상품만 보이는지 확인해 주세요.
   작업 범위는 로컬 수정과 검증까지입니다. PR에 적을 수 있도록 수정 전후 결과를 남겨 주세요.
   ```

버그 수정 PR에 재현 단계와 수정 전후 결과가 있으면, 리뷰어도 저장소에 있는 스킬을 따라 같은 조건에서 확인할 수 있습니다. 나중에 샘플 데이터를 만드는 방법이 바뀌면 스킬도 PR로 갱신합니다. 변경 이유와 확인 결과가 Git에 남으니, 다음 팀원은 최신 절차와 함께 그 절차가 왜 바뀌었는지도 볼 수 있습니다.

## 마치며

팀에서 반복하는 검증 한 가지를 골라 저장소에 기록하면, 다음 사람은 실행 방법부터 다시 설명할 필요 없이 작업의 완료 조건을 적는 데 집중할 수 있습니다. 절차가 빠져 있거나 앱과 어긋나는 부분은 팀원이 직접 재현해 보고 PR로 고칠 수 있습니다.

다음 편에서는 Claude Code의 hooks, 사용자 정의 commands, `CLAUDE.md`, `.claude/settings.json`을 Git으로 관리하고, 팀원이 PR로 변경 사항을 리뷰하며 함께 개선하는 과정을 다룹니다. [Claude Code hooks](https://code.claude.com/docs/en/hooks), [사용자 정의 commands와 스킬](https://code.claude.com/docs/en/skills), [프로젝트 지침](https://code.claude.com/docs/en/memory), [공유 설정](https://code.claude.com/docs/en/settings)
