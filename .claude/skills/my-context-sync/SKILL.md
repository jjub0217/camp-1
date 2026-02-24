---
name: my-context-sync
description: 나의 컨텍스트 싱크. Notion, GitHub에서 정보를 수집하고 하나의 문서로 정리한다. "싱크", "sync", "정보 수집" 요청에 사용.
triggers:
  - "싱크"
  - "sync"
  - "정보 수집"
  - "컨텍스트 싱크"
---

# My Context Sync

흩어진 정보를 한곳에 모아 정리하는 스킬.

Notion, GitHub에서 최근 정보를 수집하고,
하나의 마크다운 문서로 통합한다.

## 소스 정의

### 소스 1: Notion

| 항목 | 값 |
|------|-----|
| MCP 도구 | `@notionhq/notion-mcp-server` + `claude.ai Notion Connector` |
| 수집 범위 | 커들마켓(단독) 워크스페이스 |
| 메인 페이지 | `30ff2b30-7961-8198-bbd5-fbfba975ed5d` |

```yaml
databases:
  - name: "TASK DISTRIBUTION (커들마켓 | To do List)"
    id: "30ff2b30-7961-81f3-92a4-e9ac6a1dbb86"
    data_source: "collection://30ff2b30-7961-81a5-b511-000b5ad5df72"
    상태옵션: [시작 전, 개발 진행 중, 개발 완료, 배포 완료]
    속성: [이름, 상태, 담당자, 마감기한, 역할, PR Link]

  - name: "DAILY SCRUM (커들마켓 | Daily Scrum)"
    id: "30ff2b30-7961-817e-9601-d836505c5b04"
    data_source: "collection://30ff2b30-7961-8188-b8e3-000b2e8ee8aa"
    속성: [이름, 구분, 작성일시, 참여자]

  - name: "QA (표)"
    id: "30ff2b30-7961-81c7-b762-fe1b721d56b4"
    data_source: "collection://30ff2b30-7961-8123-9218-000be0dabe38"

  - name: "TROUBLE SHOOTINGS (커들마켓 | Trouble Shooting)"
    id: "30ff2b30-7961-81ef-9e09-c0b983057b31"

  - name: "Team Schedule Calendar (커들마켓 | Schedule List)"
    id: "30ff2b30-7961-8138-990e-e9ea63d1c151"
```

수집 방법:
```
2개 MCP를 조합하여 Notion 데이터를 수집한다.

[도구 로딩 — ToolSearch 키워드]
  - Connector 도구: ToolSearch(query="+notion fetch search create") → notion-fetch, notion-search, notion-create-pages 등
  - 기존 API 도구: ToolSearch(query="+notion API") → API-post-search, API-retrieve-a-database 등

[정상 작동하는 도구]
  - mcp__notion__API-post-search: 페이지/DB 검색 (⚠️ page_size는 10 이하로 제한, query에 반드시 검색어 포함)
  - mcp__notion__API-retrieve-a-database: DB 스키마 조회
  - mcp__notion__API-retrieve-a-page: 페이지 속성 조회
  - mcp__notion__API-get-block-children: 페이지 콘텐츠 조회
  - mcp__claude_ai_Notion__notion-fetch: DB/페이지 상세 조회 (⚠️ 페이지 ID 또는 DB ID만 사용. view://, collection:// 등의 URL은 전달 불가)
  - mcp__claude_ai_Notion__notion-search: DB 내 검색

[절대 사용 금지 — MCP 서버 버그]
  - mcp__notion__API-query-data-source ❌ 절대 호출하지 마라. ToolSearch로 로딩도 하지 마라. URL 라우팅 버그로 항상 400 에러 반환.
  - mcp__notion__API-retrieve-a-data-source ❌ 동일한 버그.

[병렬 호출 주의]
  실패 가능성이 있는 도구(notion-fetch 등)와 다른 도구를 병렬 호출하면,
  하나가 실패할 때 나머지도 연쇄 취소된다. 확실한 호출만 병렬로 묶을 것.

호출 예시:
  # DB 스키마 확인
  mcp__notion__API-retrieve-a-database(database_id="30ff2b30-7961-81f3-92a4-e9ac6a1dbb86")
  # DB 내 태스크 검색
  mcp__claude_ai_Notion__notion-search(query="태스크", data_source_url="collection://30ff2b30-7961-81a5-b511-000b5ad5df72")
  # 페이지 상세 조회 (ID만 사용, view:// 불가)
  mcp__claude_ai_Notion__notion-fetch(id="30ff2b30-7961-81f3-92a4-e9ac6a1dbb86")
  # 최근 페이지 검색 (page_size 10 이하, query 필수)
  mcp__notion__API-post-search(query="Daily Scrum", page_size=10, sort={"direction":"descending","timestamp":"last_edited_time"})
```

추출할 정보:
- TASK DISTRIBUTION: 진행 중인 태스크 (상태: "시작 전", "개발 진행 중"), 기한 초과 태스크 (마감기한 < 오늘 & 상태 ≠ "배포 완료"), 기한 임박 태스크 (마감기한이 3일 이내)
- DAILY SCRUM: 가장 최근 Daily Scrum 내용 (전일 업무, 금일 예정)
- 최근 업데이트된 페이지

### 소스 2: GitHub

| 항목 | 값 |
|------|-----|
| MCP 도구 | `gh` CLI (v2.83.1) |
| 수집 범위 | 최근 7일 |
| 대상 레포 | `jjub0217/cuddle-market` |

수집 방법:
```
gh CLI를 사용하여 커들마켓 레포에서 PR, 이슈, 커밋 정보를 수집한다.
별도 MCP 연결 없이 터미널에서 직접 실행 가능.

호출 예시:
  gh pr list --repo jjub0217/cuddle-market --state open --json title,url,createdAt
  gh issue list --repo jjub0217/cuddle-market --state open --json title,url,labels
  gh api repos/jjub0217/cuddle-market/commits --jq '.[0:10]'
```

추출할 정보:
- 열린 PR 목록 및 리뷰 상태
- 나에게 할당된 이슈
- 최근 커밋 내역

## 실행 흐름

이 스킬이 트리거되면 아래 순서로 실행한다.

### 1단계: 병렬 수집

2개 소스를 **동시에** 수집한다. 서로 의존성이 없으므로 병렬 실행이 가능하다.

```
수집 시작
  ├── [소스 1] Notion 태스크 수집           ─┐
  └── [소스 2] GitHub PR/이슈 수집         ─┘ 병렬 실행
수집 완료
```

각 소스 수집은 subagent(Task 도구)로 실행한다.
subagent prompt에는 반드시 위 "수집 방법"의 도구 제한사항을 포함해야 한다:

```
Task(description="Notion 수집", prompt="
  [금지] mcp__notion__API-query-data-source, mcp__notion__API-retrieve-a-data-source는 절대 호출하지 마라.
  [금지] mcp__claude_ai_Notion__notion-fetch에 view://, collection:// URL을 전달하지 마라. 페이지/DB ID만 사용.
  [제한] mcp__notion__API-post-search는 page_size 10 이하, query에 반드시 검색어 포함.
  [제한] 실패 가능성이 있는 도구를 다른 도구와 병렬 호출하지 마라.
  [ToolSearch] Connector 도구는 ToolSearch(query='+notion fetch search create')로 로딩.
  ... (수집할 내용)
")
Task(description="GitHub 수집", prompt="gh CLI로 열린 PR, 이슈, 최근 커밋을 수집하라. --repo jjub0217/cuddle-market 플래그 필수.")
```

### 2단계: 결과 통합

수집된 정보를 하나의 문서로 합친다.

통합 규칙:
- 소스별 섹션으로 구분
- 각 섹션에서 "하이라이트" (중요 항목 3개 이내)를 선별
- 액션 아이템을 문서 하단에 모아서 정리
- 수집 실패한 소스는 "수집 실패" 표시와 함께 사유 기록

### 3단계: 문서 저장

결과 파일을 저장한다.

```
저장 위치: sync/YYYY-MM-DD-context-sync.md
```

### 4단계: 리포트

실행 결과를 사용자에게 보고한다.

```
싱크 완료!

수집 결과:
  Notion: 15개 태스크
  GitHub: 5개 PR, 8개 이슈

하이라이트 3건:
  1. [GitHub] PR #42 리뷰 요청 대기 중
  2. [Notion] 기한 초과 태스크 2건
  3. [GitHub] 이슈 #15 답변 필요

액션 아이템 3건:
  - [ ] PR #42 코드 리뷰
  - [ ] Notion 기한 초과 태스크 처리
  - [ ] GitHub 이슈 #15 답변

파일 저장: sync/2026-02-22-context-sync.md
```

## 출력 포맷

출력 옵션 (2가지 동시 사용):
1. **Markdown 파일** (기본) -- `sync/YYYY-MM-DD-context-sync.md`에 저장
2. **Notion Daily Scrum 페이지** -- 커들마켓 | Daily Scrum DB에 페이지 생성

### 출력 1: Markdown 파일

저장 위치: `sync/YYYY-MM-DD-context-sync.md`

```markdown
# Context Sync - 2026-02-22

> 자동 수집 시각: 09:00

## 하이라이트
- **[GitHub]** 최근 머지된 PR 요약
- **[Notion]** 진행 중 태스크 현황
- **[Notion]** 기한 초과 태스크 (마감기한 < 오늘 & 상태 ≠ 배포 완료)

## Notion (TASK DISTRIBUTION)
### 칸반보드 현황
- 시작 전: N건
- 개발 진행 중: N건
- 개발 완료: N건
- ⚠️ 기한 초과: N건 (마감기한이 지났지만 배포 완료되지 않은 태스크)

## GitHub (jjub0217/cuddle-market)
### 최근 커밋
- feat: 이메일 중복체크 로딩 상태 추가 (#336)
- fix: 회원가입 성공 후 로딩 상태 해제 수정 (#338)

### 열린 PR / 이슈
- (있으면 표시)

## 액션 아이템
- [ ] 오늘 처리할 태스크 목록
```

### 출력 2: Notion Daily Scrum 페이지

대상 DB: `커들마켓 | Daily Scrum` (collection://30ff2b30-7961-8188-b8e3-000b2e8ee8aa)

페이지 속성:
```yaml
이름: "YYYY년 M월 D일"          # 예: "2026년 2월 22일"
구분: "개발"
작성일시: "YYYY-MM-DD"          # 예: "2026-02-22"
참여자: [강주현]
```

페이지 내용 (기존 템플릿 형식에 맞춤):
```markdown
## 전일 업무 진행상황 보고
| 강주현 | (GitHub 최근 커밋 + Notion 완료 태스크에서 자동 추출) |

## 금일 예정 업무 보고
| 강주현 | (Notion 칸반보드 "시작 전"/"개발 진행 중" 태스크 + GitHub 열린 이슈에서 자동 추출) |

## 회의 안건
- -

## 추가 논의사항
- -
```

Notion 페이지 생성 방법:
```
mcp__claude_ai_Notion__notion-create-pages 도구를 사용하여
커들마켓 | Daily Scrum DB에 위 형식의 페이지를 생성한다.
```

## 커스터마이징 가이드

### 소스 추가하기

새로운 소스를 추가하려면 "소스 정의" 섹션에 같은 형식으로 추가한다:

```markdown
### 소스 N: Linear (예시)

> Linear는 소프트웨어 개발팀에서 많이 쓰는 프로젝트 관리 도구.
> Notion 칸반보드와 비슷하지만 개발 워크플로우에 특화되어 있다.

| 항목 | 값 |
|------|-----|
| MCP 도구 | `mcp__claude_ai_Linear__list_issues` |
| 수집 범위 | 나에게 할당된 이슈 |

수집 방법:
  mcp__claude_ai_Linear__list_issues 호출.

추출할 정보:
- 진행 중인 이슈
- 이번 주 마감 이슈
```

### 소스 제거하기

사용하지 않는 소스는 해당 "소스 N" 섹션 전체를 삭제한다.
실행 흐름의 병렬 수집 부분에서도 해당 줄을 제거한다.

### 수집 주기 변경하기

기본은 수동 실행이다. 자동 실행을 원하면 CLAUDE.md에 스케줄을 추가한다:

```markdown
## 스케줄
- context-sync: 매일 09:00 실행
```

### 출력 위치 변경하기

"3단계: 문서 저장"의 경로를 원하는 위치로 수정한다.
예: `reports/`, `docs/daily/` 등.
