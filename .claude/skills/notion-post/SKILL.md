---
name: notion-post
description: 노션 글을 가져와 Fuwari 마크다운으로 변환하고, 카테고리를 확인한 뒤 커밋·배포·배포 확인까지 수행하는 블로그 발행 파이프라인 (notion-converter/blog-deployer 에이전트 오케스트레이션)
---

# 노션 → 블로그 발행 파이프라인

사용자가 `/notion-post <노션 페이지 제목 또는 URL>`을 실행하면 아래 단계를 수행한다.
인자가 없으면 먼저 어떤 노션 글을 발행할지 물어본다.

이 파이프라인은 두 개의 저장소 에이전트를 오케스트레이션한다:
- `notion-converter` — 노션 가져오기 + 마크다운 변환 (1~2단계)
- `blog-deployer` — 빌드 검증 + 커밋·push + 배포 확인 (4~6단계)

카테고리 확정(3단계)만 메인 루프에서 사용자와 질의응답으로 처리한다.

## 1~2단계 — 변환 (notion-converter 에이전트에 위임)

Agent 도구로 `notion-converter`를 실행하고 노션 페이지 URL/제목을 전달한다.

반환값 처리:
- `NOTION_MCP_UNAVAILABLE` → 사용자에게 Notion 커넥터 연결을 요청하고 중단
- `EMPTY_PAGE: ...` → 본문이 없다고 알리고 중단
- 후보 목록 → AskUserQuestion으로 어느 페이지인지 확인 후 재실행
- 정상 → `SLUG:`, `CATEGORY_CANDIDATES:`, 마크다운 전문을 확보

## 3단계 — 카테고리/메타 질의응답 (메인 루프)

1. 기존 카테고리 수집: `grep -h "^category:" src/content/posts/*.md | sort -u`
2. AskUserQuestion으로 확인:
   - **카테고리**: 에이전트가 제안한 후보를 추천(첫 옵션)으로, 기존 카테고리를 선택지로 제시
     (기본 후보: SLAM, ROS, Sensor, Code, AI, Autonomous, Project, System, Book)
   - 제목/태그/설명 요약을 보여주고 수정할 부분 확인
3. frontmatter의 `category: TBD`를 확정값으로 바꾸고 `src/content/posts/<슬러그>.md`로 저장한다.
   - 같은 슬러그 파일이 이미 있으면 덮어쓰기 전에 사용자에게 확인한다.
   - `<!-- IMAGE_FAILED: ... -->` 주석이 있으면 사용자에게 알린다.

## 4~6단계 — 배포 (blog-deployer 에이전트에 위임)

Agent 도구로 `blog-deployer`를 실행하고 커밋 메시지(`New post: <제목>`)와 슬러그를 전달한다.

반환값 처리:
- `DEPLOYED: ...` → 사용자에게 최종 글 URL 보고:
  `https://robot-hyunwoo.github.io/hyunwoo_blog/posts/<슬러그>/`
- `FAILED at ...` → 원인을 사용자에게 보고하고, 수정 가능하면 수정 후 blog-deployer 재실행

## 주의사항

- 노션 원문은 수정하지 않는다 (읽기 전용).
- 빌드 실패 상태로 push하지 않는다 (blog-deployer가 보장).
