# CLAUDE.md

이 저장소는 Fuwari(Astro) 테마 기반 개인 기술 블로그다.
배포 주소: https://robot-hyunwoo.github.io/

## 명령어

```bash
corepack enable                  # pnpm 활성화 (최초 1회)
pnpm install --frozen-lockfile   # 의존성 설치
pnpm dev                         # 개발 서버 → http://localhost:4321/
pnpm build                       # 프로덕션 빌드 (dist/) — push 전 반드시 통과 확인
```

## 구조

- `src/content/posts/*.md` — 블로그 글 (Fuwari frontmatter 스키마 필수)
- `src/content/spec/about.md` — About 페이지
- `src/config.ts` — 사이트/프로필/내비바 설정
- `src/assets/images/` — 아바타·배너·글 이미지
- `.github/workflows/deploy.yml` — main push 시 GitHub Pages 자동 배포
- `design/` — 초기 디자인 시안 (참고용 HTML)
- `.claude/skills/notion-post/` — 노션 글 발행 파이프라인 스킬 (`/notion-post`)
- `.claude/agents/notion-converter.md` — 노션 → 마크다운 변환 에이전트
- `.claude/agents/blog-deployer.md` — 빌드·push·배포 확인 에이전트

## 글 frontmatter 스키마

```yaml
---
title: "제목"
published: 2026-07-22   # 블로그 발행일 (노션 작성일 아님)
updated: 2026-07-25     # (선택) 수정 시 추가 — 피드는 updated ?? published 내림차순 정렬
description: "한 줄 요약"
tags: [SLAM, ROS]
category: SLAM        # SLAM | ROS | Sensor | Code | AI | Autonomous | Project | System | Book
                      # 표기 규칙: 첫 글자만 대문자 (SLAM·ROS·AI 같은 약어는 예외)
draft: false          # true면 프로덕션에서 숨김
lang: ko
---
```

**published 날짜 규칙 (정렬 정확도):**

- 같은 날 여러 글을 발행하면 날짜만으로는 순서가 파일명순으로 떨어진다. **같은 날 2편 이상 발행 시**, 각 글의 published에 **시각 + KST 타임존**을 붙여 발행 순서를 구분한다 (최신이 위로): `published: 2026-07-22T16:10:00+09:00`.
- **타임존(`+09:00`)을 반드시 명시**한다. 시각만 쓰면 아카이브(브라우저 KST 계산)가 UTC로 해석해 15:00 이후 글이 다음 날로 넘어간다. 화면 표시는 날짜만 나오지만 정렬·아카이브 버킷은 이 값을 쓴다.
- 하루에 1편만 발행하면 `published: YYYY-MM-DD`(날짜만)로 충분하다.

## 글 발행 흐름

노션에서 글을 가져와 발행할 때는 `/notion-post <제목 또는 URL>` 스킬을 사용한다
(노션 MCP 커넥터 필요). 수동 발행은: 마크다운 작성 → `pnpm build` 통과 확인 →
commit → push → GitHub Actions가 자동 배포 (2~3분).

**push 후 배포 확인 (필수):** push로 끝내지 말고 배포 완료까지 확인한다.

1. `gh run watch --exit-status <run-id>` (또는 `gh run list`)로 "Deploy to GitHub Pages" 워크플로우가 success로 끝나는지 확인
2. 변경된 페이지·이미지 URL을 curl로 확인 (HTTP 200 및 내용 반영):
   `curl -s -o /dev/null -w "%{http_code}" https://robot-hyunwoo.github.io/<경로>`

## 규칙

- 코드 주석은 영어로 작성한다.
- push 전 로컬 `pnpm build`가 성공해야 한다 (빌드 실패 상태로 push 금지).
- 업스트림 Fuwari 대비 커스텀 패치가 있다 (README "업스트림 관련 메모" 참고):
  - `src/styles/markdown.css` 상단 `@import "./main.css";` — 삭제 금지
  - `src/components/widget/Profile.astro` — bio의 `\n` 줄바꿈 렌더링
- Fuwari 테마 업데이트 시 위 패치가 유지되는지 확인한다.
