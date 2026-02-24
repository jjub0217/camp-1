# Day 2 MCP 딥다이브 — 복습 노트

## 1. MCP란?

**MCP = Model Context Protocol**

AI 앱(Claude Code)과 외부 도구(Slack, Notion, GitHub 등)를 연결하는 **오픈 표준**.

> USB-C가 충전기, 모니터, 외장하드를 하나의 포트로 연결하듯,
> MCP는 다양한 외부 서비스를 하나의 규격으로 Claude에 연결한다.

### MCP가 없으면 vs 있으면

```
MCP 없이                              MCP로
┌──────────┐                         ┌──────────┐
│ Claude   │                         │ Claude   │
│ Code     │                         │ Code     │
└────┬─────┘                         └────┬─────┘
     │                                    │ MCP (표준 규격)
     │ ❌ 각각 다른 방식으로               │
     │    연결해야 함                 ┌────┴────┐
     │                               │ MCP     │
┌────┼──────┐                        │ Server  │
│    │      │                        └────┬────┘
▼    ▼      ▼                             │
Slack Notion Calendar             ┌───────┼───────┐
(각각 다른 API)                    ▼       ▼       ▼
                                 Slack  Notion  Calendar
                                 (전부 같은 방식)
```

### MCP의 3가지 핵심 요소

| 요소 | 역할 | 비유 |
|------|------|------|
| **Host** | AI 앱 (Claude Code) | 내 컴퓨터 |
| **Client** | 연결을 관리하는 중간자 | USB-C 포트 |
| **Server** | 외부 도구 제공자 | USB-C 기기 (모니터, 충전기) |

```
┌─────────────────────────────────────┐
│         Host (Claude Code)          │
│                                     │
│  ┌─────────┐     ┌─────────┐       │
│  │ Client 1│     │ Client 2│       │
│  └────┬────┘     └────┬────┘       │
└───────┼───────────────┼─────────────┘
        │               │
   ┌────▼────┐     ┌────▼────┐
   │ Slack   │     │ Notion  │
   │ Server  │     │ Server  │
   └─────────┘     └─────────┘
```

### MCP Server가 제공하는 3가지

| 종류 | 설명 | 예시 |
|------|------|------|
| **Tools** | Claude가 실행할 수 있는 기능 | "메시지 보내기", "파일 읽기" |
| **Resources** | Claude가 참조할 수 있는 데이터 | DB 스키마, 파일 내용 |
| **Prompts** | 미리 만든 대화 템플릿 | "PR 리뷰해줘" 슬래시 명령 |

> 가장 많이 쓰는 건 **Tools**. "Slack 메시지 읽어줘"라고 하면 Claude가 MCP를 통해 Tool을 호출한다.

---

## 2. MCP 서버 연결 방식 (transport)

서버가 **어디서 실행되느냐**에 따라 연결 방법이 다르다. 딱 2가지만 기억하면 된다:

| 방식 | 서버 위치 | 비유 | 언제 쓰나 |
|------|-----------|------|-----------|
| **HTTP** | 클라우드 | **웹사이트 접속** | Notion, Sentry 등 클라우드 서비스 |
| **stdio** | 내 컴퓨터 | **앱 설치** | 로컬 파일, DB, npx 서버 |
| SSE | 클라우드 (구버전) | HTTP의 이전 버전 | 거의 안 씀 |

```
HTTP (클라우드)                    stdio (내 컴퓨터)
┌──────────┐                      ┌──────────┐
│ Claude   │                      │ Claude   │
│ Code     │                      │ Code     │
└────┬─────┘                      └────┬─────┘
     │ 인터넷으로 접속                  │ 내 컴퓨터에서 실행
     ▼                                 ▼
┌──────────┐                      ┌──────────┐
│ Notion   │ ← 회사 서버에서      │ Context7 │ ← 내 컴퓨터에서
│ 서버     │   이미 돌아가는 중    │ 서버     │   npx로 실행
└──────────┘                      └──────────┘
```

> **기억법**: "웹사이트처럼 접속 = HTTP, 앱처럼 설치 = stdio"

---

## 3. 명령어 정리

### 서버 추가: `claude mcp add`

```
claude mcp add --transport [방식] [이름] [주소]
```

길어 보이지만, 한 단어씩 뜯어보면 간단하다:

| 입력 | 의미 | 비유 |
|------|------|------|
| `claude` | Claude Code 실행 | "클로드야," |
| `mcp` | MCP 기능 사용 | "MCP 관련해서," |
| `add` | 서버를 추가해줘 | "하나 추가해줘" |
| `--transport http` | HTTP 방식으로 | "웹으로 연결" |
| `notion` | 서버의 이름(별명) | "이름은 notion이야" |
| `https://mcp.notion.com/mcp` | 서버의 실제 주소 | "주소는 여기야" |

> 즉, **"클로드야, MCP 서버 하나 추가해줘. HTTP 방식으로, 이름은 notion이고, 주소는 이거야"** 라는 말이다.

#### HTTP 방식 예시 (클라우드 서비스)

```bash
# Notion 연결
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Sentry 연결
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# GitHub 연결
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

#### stdio 방식 예시 (내 컴퓨터에서 실행)

```bash
# Context7 — 프로그래밍 문서 실시간 검색
claude mcp add --transport stdio context7 -- npx -y @upstash/context7-mcp@latest

# Fetch — 웹페이지 가져오기
claude mcp add --transport stdio fetch -- npx -y @anthropic-ai/mcp-fetch@latest

# Filesystem — 로컬 파일 시스템 접근
claude mcp add --transport stdio filesystem -- npx -y @modelcontextprotocol/server-filesystem /path/to/folder
```

> stdio에서 `--` 뒤에 오는 건 MCP 서버를 실행하는 명령어다.

### 서버 관리 명령어

| 명령어 | 하는 일 |
|--------|---------|
| `claude mcp list` | 설치된 서버 목록 보기 |
| `claude mcp get [이름]` | 특정 서버 상세 정보 확인 |
| `claude mcp remove [이름]` | 서버 제거 |

### Claude Code 안에서 확인

| 명령어 | 하는 일 |
|--------|---------|
| `/mcp` | 연결된 MCP 서버 목록 + 상태 확인 |

### 저장 범위 (scope)

```bash
# 기본: 이 프로젝트에서만, 나만 사용
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 모든 프로젝트에서 사용 (user scope)
claude mcp add --transport http --scope user notion https://mcp.notion.com/mcp
```

| scope | 저장 위치 | 누가 쓰나 |
|-------|-----------|-----------|
| `local` (기본) | 이 프로젝트, 나만 | 나 혼자 |
| `project` | 프로젝트 폴더 (.mcp.json) | 팀 전체 (git 공유) |
| `user` | 내 컴퓨터 전체 | 모든 프로젝트에서 나만 |

---

## 4. 대표적인 MCP 서버 5개

| 서버 | 하는 일 | transport |
|------|---------|-----------|
| **Notion** | 노션 페이지/DB 읽기·관리 | HTTP |
| **GitHub** | PR, 이슈, 코드 리뷰 | HTTP |
| **Sentry** | 에러 모니터링, 로그 분석 | HTTP |
| **Context7** | 프로그래밍 라이브러리 문서 검색 | stdio |
| **Fetch** | 웹페이지 내용 가져오기 | stdio |

---

## 5. /mcp 명령어로 도구 탐색

### /mcp란?

Claude Code 안에서 `/mcp`를 입력하면 **연결된 MCP 서버의 상태와 도구를 한눈에** 볼 수 있다.

| 기능 | 설명 |
|------|------|
| 서버 목록 | 연결된 모든 MCP 서버 표시 |
| 연결 상태 | 각 서버의 연결(✅) / 오류(❌) 상태 확인 |
| 도구 목록 | 각 서버가 제공하는 도구(Tools) 확인 |
| OAuth 인증 | 로그인이 필요한 서버 인증 처리 |

### /mcp 실행 화면 예시

```
/mcp 실행 시
┌──────────────────────────────────────────┐
│ MCP Servers                              │
│                                          │
│ ✅ context7 (stdio)                      │
│    Tools: resolve-library-id, query-docs │
│                                          │
│ ✅ slack (http)                          │
│    Tools: slack_send_message,            │
│           slack_read_channel, ...        │
│                                          │
│ ❌ notion (http) - 인증 필요             │
│    → "인증" 선택하여 로그인              │
└──────────────────────────────────────────┘
```

### 명령어 역할 구분

| 하고 싶은 것 | 명령어 |
|-------------|--------|
| 서버 **설치** | `claude mcp add` (터미널에서) |
| 서버 **삭제** | `claude mcp remove` (터미널에서) |
| 서버 **현황 확인** | `/mcp` (Claude Code 안에서) |

> `/mcp` = **"지금 뭐가 연결되어 있지?"** 할 때 쓰는 명령어.

### MCP 도구의 이름 규칙

MCP 도구는 `mcp__서버이름__도구이름` 형태로 불린다:

| 서버 | 도구 | 전체 이름 |
|------|------|-----------|
| Slack | slack_read_channel | `mcp__claude_ai_Slack__slack_read_channel` |
| context7 | query-docs | `mcp__context7__query-docs` |

> 이름이 길지만 **직접 외울 필요 없다.** Claude에게 "Slack 메시지 읽어줘"라고 하면 알아서 올바른 도구를 호출한다.

### claude.ai에서 MCP 연결하기 (가장 쉬운 방법)

터미널 명령어 없이, **웹 브라우저에서 클릭 몇 번으로** MCP를 연결할 수도 있다:

```
1. https://claude.ai/settings/connectors 접속
2. 연결하고 싶은 서비스 선택 (Slack, Notion 등)
3. 로그인 후 연결 승인
4. Claude Code에서 /mcp → "claude.ai" 섹션에 자동 등록!
```

> Slack을 처음 연결한다면 이 방법이 가장 쉽다. 터미널 명령어 없이 웹에서 로그인만 하면 된다.

### OAuth 인증이 필요한 서버

Notion, GitHub, Sentry 같은 서비스는 로그인이 필요하다:

```
1. /mcp 실행
2. 인증이 필요한 서버 옆의 "인증" 선택
3. 브라우저가 열리면 로그인
4. 인증 완료 → 서버 연결됨
```

> 인증 토큰은 자동 저장되고 자동 갱신된다. 한 번만 하면 된다.

---

## 6. 인기 MCP 서버 탐색 및 설치

### MCP 서버는 어디서 찾나?

| 출처 | URL | 특징 |
|------|-----|------|
| **Claude Code 공식 문서** | https://code.claude.com/docs/ko/mcp | Claude Code와 호환 확인된 서버 |
| **MCP 서버 GitHub** | https://github.com/modelcontextprotocol/servers | 전체 목록 |
| **MCP Registry** | https://registry.modelcontextprotocol.io | 최신 서버 검색 |

### 카테고리별 추천 서버

#### 커뮤니케이션

| 서버 | 할 수 있는 것 | 추가 명령어 |
|------|--------------|-------------|
| **Slack** | 메시지 읽기/보내기, 채널 검색 | `claude mcp add --transport http slack https://api.slack.com/mcp` |

#### 생산성

| 서버 | 할 수 있는 것 | 추가 명령어 |
|------|--------------|-------------|
| **Notion** | 페이지 읽기/쓰기, DB 쿼리 | `claude mcp add --transport http notion https://mcp.notion.com/mcp` |
| **Linear** | 이슈 관리, 프로젝트 추적 | `claude mcp add --transport http linear https://mcp.linear.app/sse` |

#### 개발

| 서버 | 할 수 있는 것 | 추가 명령어 |
|------|--------------|-------------|
| **GitHub** | PR 리뷰, 이슈 관리 | `claude mcp add --transport http github https://api.githubcopilot.com/mcp/` |
| **Sentry** | 에러 모니터링, 디버깅 | `claude mcp add --transport http sentry https://mcp.sentry.dev/mcp` |
| **Context7** | 라이브러리 문서 검색 | `claude mcp add --transport stdio context7 -- npx -y @upstash/context7-mcp@latest` |

#### 데이터

| 서버 | 할 수 있는 것 | 추가 명령어 |
|------|--------------|-------------|
| **Fetch** | 웹페이지 내용 가져오기 | `claude mcp add --transport stdio fetch -- npx -y @anthropic-ai/mcp-fetch@latest` |
| **Filesystem** | 로컬 파일 읽기/쓰기 | `claude mcp add --transport stdio fs -- npx -y @modelcontextprotocol/server-filesystem /path` |
| **Memory** | 지식 그래프 기반 기억 | `claude mcp add --transport stdio memory -- npx -y @modelcontextprotocol/server-memory` |

### 서버 선택 기준

```
내가 매일 쓰는 도구가 뭐지?
  │
  ├─ Slack 많이 쓴다 → Slack MCP
  ├─ Notion에 정보 정리 → Notion MCP
  ├─ 코딩한다 → GitHub + Context7 MCP
  └─ 웹 검색/리서치 → Fetch MCP
```

> 한 번에 다 설치하지 말고, **가장 자주 쓰는 도구 1~2개**부터 시작하자.

### MCP의 한계 — 왜 Skill을 직접 만들어야 하나?

MCP 서버는 **남이 만들어둔 범용 도구**다. 편하지만 한계가 있다:

```
MCP 서버 (남이 만든 것)              나만의 Skill (직접 만든 것)
  ├─ 설치 즉시 사용 가능              ├─ 내 워크플로우에 딱 맞게 설계
  ├─ 범용적 기능 제공                 ├─ 필요한 기능만 조합
  ├─ 커스텀이 어려움 ⚠️              ├─ 자유롭게 수정 가능
  └─ 모든 기능이 다 필요한 건 아님     └─ 진짜 효율적인 자동화
```

> **MCP = 레고 블록, Skill = 레고로 만든 완성품.**
> MCP로 시작하되, 진짜 효과적인 워크플로우를 원한다면 직접 Skill로 만들자.

---

## 7. Plugin으로 MCP 확장

### Plugin이란?

**Plugin = Skill + MCP + Hook + Agent를 묶은 패키지**다.

```
Plugin 안에 MCP가 포함될 수 있다
┌─────────────────────────────────┐
│         My Plugin               │
│                                 │
│  ┌─────────┐  ┌──────────────┐  │
│  │ Skills  │  │ MCP Servers  │  │
│  └─────────┘  └──────────────┘  │
│  ┌─────────┐  ┌──────────────┐  │
│  │ Hooks   │  │ Agents       │  │
│  └─────────┘  └──────────────┘  │
└─────────────────────────────────┘
```

> Plugin을 설치하면 그 안에 포함된 MCP 서버가 **자동으로** 연결된다. 따로 `claude mcp add`를 할 필요 없다.

### Plugin이 MCP보다 좋은 점

| 항목 | MCP만 | Plugin + MCP |
|------|-------|--------------|
| 설치 | `claude mcp add ...` 직접 입력 | `/plugin install` 한 줄 |
| 팀 공유 | 각자 설정 | 플러그인 하나로 동일 환경 |
| 추가 기능 | MCP 도구만 | Skill + Hook + Agent까지 |
| 업데이트 | 수동 | 플러그인 업데이트로 자동 |

### Official vs Community Plugin

```
Official Plugins                    Community Plugins
  ├─ Anthropic이 검증              ├─ 커뮤니티 개발자가 만든 것
  ├─ /plugin에서 바로 설치          ├─ marketplace 등록 후 설치
  └─ 안정성 보장                   └─ 더 다양하고 창의적

  비유: iPhone 기본 앱              비유: App Store에서 다운로드
```

### Plugin 명령어

| 명령어 | 동작 |
|--------|------|
| `/plugin` | 플러그인 관리 메뉴 열기 |
| `/plugin marketplace add [URL]` | 커뮤니티 마켓플레이스 등록 |
| `/plugin install [이름]` | 플러그인 설치 |

### 설치 → 사용 전체 흐름

```
/plugin                                              ← 1. 메뉴 열기
/plugin marketplace add obra/superpowers-marketplace  ← 2. 마켓 등록
/plugin install superpowers@superpowers-marketplace   ← 3. 설치
/mcp                                                 ← 4. MCP 자동 연결 확인
"코드 짜줘", "버그 고쳐줘" 등                          ← 5. 그냥 평소처럼 쓰면 됨!
```

### Plugin 안의 Skill은 어떻게 작동하나?

**자동 발동** — 상황에 맞으면 Claude가 알아서 적절한 스킬을 사용한다:

```
나: "이 코드에서 에러가 나는데 고쳐줘"
     ↓
Claude 판단: "버그 상황이네? systematic-debugging 스킬 쓰자"
     ↓
자동으로 체계적 디버깅 워크플로우 시작
```

**직접 호출** — `/스킬이름`으로 강제 지정할 수도 있다:

| 스킬 | 자동 발동 조건 | 직접 호출 |
|------|---------------|----------|
| `brainstorming` | 새 기능 만들기, 설계 | `/brainstorming` |
| `systematic-debugging` | 버그, 에러 발생 | `/systematic-debugging` |
| `test-driven-development` | 기능 구현 전 | `/test-driven-development` |
| `writing-plans` | 복잡한 멀티스텝 작업 | `/writing-plans` |

> **보통은 자연어로 요청하면 된다.** "버그 고쳐줘", "새 기능 만들어줘" 같은 일상적인 말에도 Claude가 알아서 맞는 스킬을 적용한다.

---

## 8. 한 줄 요약

- **MCP** = AI와 외부 도구를 표준 규격으로 연결하는 프로토콜
- **클라우드 서비스 연결** = `--transport http` (비유: 웹사이트 접속)
- **내 컴퓨터 로컬 실행** = `--transport stdio` (비유: 앱 설치)
- **서버 추가** = `claude mcp add` (터미널에서)
- **서버 현황 확인** = `/mcp` (Claude Code 안에서)
- **서버 목록** = `claude mcp list` (터미널에서)
- **웹에서 쉽게 연결** = https://claude.ai/settings/connectors
- **Plugin** = MCP + Skill + Hook + Agent 묶음 패키지
- **Plugin 설치** = `/plugin install` 한 줄이면 MCP까지 자동 연결
- **Plugin의 Skill** = 평소처럼 쓰면 자동 발동, `/스킬이름`으로 직접 호출도 가능
- **MCP는 부품, Skill은 완성품, Plugin은 세트 상품** — 필요에 따라 조합하자
