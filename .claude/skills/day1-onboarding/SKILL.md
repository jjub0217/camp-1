---
name: day1-onboarding
description: AI Native Camp Day 1 온보딩. Claude와 대화하면서 Claude Code를 익힌다. "1일차", "Day 1", "온보딩" 요청에 사용.
---

# Day 1: Onboarding

이 스킬이 호출되면 아래 규칙에 따라 블록 단위로 진행한다.

**규칙:**
- 한 번에 한 블록씩 진행한다
- "다음", "skip", 블록 번호/이름으로 이동한다
- 퀴즈는 반드시 AskUserQuestion으로 출제한다
- 블록이 끝나면 AskUserQuestion으로 다음 진행 여부를 확인한다
- Claude Code 관련 질문이 오면 claude-code-guide 에이전트로 답변한다

스킬 시작 시 아래 테이블을 보여주고 어디서 시작할지 물어본다.

| Block | 주제 | 내용 |
|-------|------|------|
| 0 | Setup | Claude Code 설치 + 첫 대화 + CLAUDE.md |
| 1 | Experience | Working Backward 데모 3가지 |
| 2 | Why | 왜 CLI? 왜 터미널? (퀴즈 2개) |
| 3 | What | 7개 기능 소개 |
| 4 | Basics | CLI + git + GitHub (퀴즈 3개) |

```json
AskUserQuestion({
  "questions": [{
    "question": "어디서부터 시작할까요?",
    "header": "시작 블록",
    "options": [
      {"label": "Block 0: Setup", "description": "Claude Code 설치 + 첫 대화 + CLAUDE.md"},
      {"label": "Block 1: Experience", "description": "Working Backward 데모 3가지"},
      {"label": "Block 2: Why", "description": "왜 CLI? 왜 터미널?"},
      {"label": "Block 3: What", "description": "7개 기능 소개"}
    ],
    "multiSelect": false
  }]
})
```

---

## Block 0: Setup

> 공식 문서: https://code.claude.com/docs/ko/setup | 빠른 시작: https://code.claude.com/docs/ko/quickstart

### 설치

| OS | 명령어 |
|----|--------|
| Mac / Linux | `curl -fsSL https://claude.ai/install.sh \| bash` |
| Windows (PowerShell) | `irm https://claude.ai/install.ps1 \| iex` |

Node.js 불필요. 한 줄이면 끝. 자동 업데이트 내장.

| 문제 | 해결 |
|------|------|
| 권한 에러 (Mac) | 터미널을 관리자 권한으로 재실행 |
| 설치 후 claude 명령 안 됨 | 터미널을 껐다 다시 열기 |
| Windows에서 실행 정책 에러 | `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` 실행 후 재시도 |

### 첫 실행

터미널에 `claude` 한 글자를 입력하라고 안내한다. Anthropic 계정 로그인 + Claude 구독(Pro, Max, Teams, Enterprise) 연결을 확인시킨다.

### 첫 대화

참가자에게 순서대로 시도하라고 안내한다:
1. "안녕, 나는 [이름]이고 [직업]이야. 나한테 인사해줘"
2. "내 업무에서 매일 반복하는 일 3가지를 말해줄 테니 어떤 걸 자동화할 수 있을지 알려줘"
3. 5분 자유 대화

### CLAUDE.md 만들기

참가자에게 아래처럼 Claude에게 요청하라고 안내한다:

```
이 폴더에 CLAUDE.md를 만들어줘.
내 이름은 [이름]이고, [직업]이야.
나는 항상 [선호사항]으로 해줘.
```

선호사항 예시: "존댓말로", "짧게 답해줘", "항상 표로 정리해줘"

블록 끝:

```json
AskUserQuestion({
  "questions": [{
    "question": "설치와 첫 대화가 완료되었나요?",
    "header": "Setup 확인",
    "options": [
      {"label": "완료, 다음으로", "description": "Block 1로 이동"},
      {"label": "아직 진행 중", "description": "더 시간이 필요함"},
      {"label": "트러블슈팅 필요", "description": "설치나 실행에 문제 발생"}
    ],
    "multiSelect": false
  }]
})
```

---

## Block 1: Experience

> 7일 후 모습을 먼저 체험한다. 외우지 않는다. 느끼기만 한다.

먼저 아래 Before/After 테이블을 보여준다:

| Before (지금) | After (7일 후) |
|---------------|----------------|
| 슬랙/캘린더/노션 하나씩 열어서 30분 | 한 문장으로 자동 발송 |
| "앱 만들어줘" → 뭘 만들지 모름 | 모호한 요청 → Claude가 질문으로 명확화 |
| 기능 외우려고 문서 뒤지기 | 모르면 Claude에게 물어보기 |

### 데모 1: 한 문장 요청

참가자에게 Claude에 간단한 요청을 직접 해보라고 안내한다. 예시:

```
오늘 내가 할 일 3가지를 정리해서 알려줘
```

핵심 포인트: 한 문장으로 요청하면 Claude가 알아서 판단하고 실행한다는 것을 체험시킨다.

### 데모 2: 모호한 요청 정리

참가자에게 일부러 모호하게 요청해보라고 안내한다:

```
우리 팀 업무를 개선해줘. 모호한 부분을 단계별로 추론해서 질문해. 질문할 때에는 AskUserQuestion을 사용해
```

Claude가 "어떤 팀인가요?", "몇 명인가요?" 등 질문하는 과정을 체험시킨다.

### 데모 3: Claude에게 물어보기

참가자에게 Claude Code 자체에 대해 질문해보라고 안내한다:

```
Claude Code에서 MCP가 뭐야?
```

핵심 메시지: 기능을 다 외울 필요 없다. 모르면 Claude에게 물어보면 된다.

```
체험 정리:
1. 한 문장 요청 → Claude가 실행
2. 모호한 요청 → Claude가 질문으로 명확화
3. 모르는 것 → Claude에게 물어보기
```

---

## Block 2: Why

### Quiz 1: 왜 CLI인가?

```json
AskUserQuestion({
  "questions": [{
    "question": "왜 굳이 터미널을 깔아서 Claude Code를 사용해야 할까요?",
    "header": "Quiz 1",
    "options": [
      {"label": "CLI는 컴퓨터의 모든 일을 할 수 있어서", "description": "GUI는 버튼만 누를 수 있지만 CLI는 제한 없음"},
      {"label": "터미널이 더 예뻐서", "description": "예쁜 건 GUI가 더 예쁨"},
      {"label": "프로그래머처럼 보이려고", "description": "멋은 부산물일 뿐"},
      {"label": "Anthropic이 시켜서", "description": "아무도 안 시킴"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번. 해설로 아래 도식을 보여준다:

```
GUI:  사용자 → [버튼] [버튼] [버튼] → 제한된 기능
CLI:  사용자 → 자유로운 명령어 ────→ 컴퓨터의 모든 기능
      AI 에이전트 → CLI ────────────→ 소프트웨어 직접 제어
```

### Quiz 2: 왜 Cowork가 아니라 터미널인가?

```json
AskUserQuestion({
  "questions": [{
    "question": "왜 CoWork 같은 비개발자용 툴이 아니라 터미널에서 Claude Code를 써야 할까요?",
    "header": "Quiz 2",
    "options": [
      {"label": "모두가 코딩으로 새로운 차원의 일을 하게 될 것이라서", "description": "코딩 = 개발자 전유물이 아니라 모든 지식노동의 도구가 된다"},
      {"label": "Claude CoWork 같은 비개발자용 도구가 더 나아서", "description": "CoWork도 좋지만 터미널이 자유도의 끝판왕"},
      {"label": "터미널이 무료라서", "description": "둘 다 유료"},
      {"label": "개발자만 쓸 수 있어서", "description": "비개발자도 씀. 지금 여기서."}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번. 해설:

```
Cursor, CoWork 같은 도구 → 정해진 UI 안에서의 생산성
터미널 Claude Code   → 코딩이라는 새로운 차원의 일

결과적으로 토큰을 많이 쓸 수 있다 = 엄청나게 많은 노동력을 가진 사람이 된다
```

오늘만 지나면 출발선에 서게 된다. 내일부터는 더 근본적인 원리를 알게 되는 날들이다.

### 영상 시청

아래 영상을 시청하라고 안내한다:

https://youtu.be/JI8S9T7r2SQ?si=RXSzHCGe9H6eniF9&t=29

> "AI 분야가 지식 노동보다 더 큰 규모가 될 수 있는 '소프트웨어 공장 카테고리'라고 보고 있다. 그럴듯한게, 이 그래프들을 보면 말도 안됨. 기존 코딩 생산성 툴의 시장을 그냥 키워버림"

---

## Block 3: What

> 7개 기능을 하나씩 체험한다. 설명 → 실습 → 퀴즈 순서로 진행한다.

먼저 전체 목차를 보여준다:

| # | 기능 | 한 줄 |
|---|------|-------|
| 1 | CLAUDE.md | Claude가 매번 읽는 규칙 |
| 2 | Skill | 반복 업무를 레시피로 저장 |
| 3 | MCP | 외부 도구 연결 (Slack, Calendar 등) |
| 4 | Subagent | 하나의 작업을 백그라운드에서 처리 |
| 5 | Agent Teams | 여러 Subagent가 동시에 작업 |
| 6 | Hook | 특정 이벤트 발생 시 자동 실행 |
| 7 | Plugin | 위의 모든 것을 묶어서 팀에 공유 |

---

### 3-1. CLAUDE.md

> 공식 문서: https://code.claude.com/docs/ko/memory

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **시스템 프롬프트** — AI가 대화를 시작할 때 가장 먼저 읽는 지시문. 매 세션마다 규칙을 주입하여 AI의 휘발성 기억을 영구 기억으로 만든다 |
| 비유 | 팀 위키 — Claude가 매 대화 시작할 때 읽는 규칙서 |
| 예시 | "항상 존댓말로", "표로 정리해줘", "내 이름은 OOO" |

```
세션 시작
  │
  ▼
┌─────────────┐     ┌─────────────┐
│ CLAUDE.md   │────▶│ 시스템       │────▶ Claude가 규칙을 아는 상태로 대화 시작
│ (영구 기억)  │     │ 프롬프트     │
└─────────────┘     └─────────────┘
  항상 읽힘            매번 자동 주입
```

**② 실습**: Block 0에서 이미 만들었다. 아래를 입력해서 확인하라고 안내한다:

```
CLAUDE.md를 읽어서 내용을 보여줘
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-1**:

```json
AskUserQuestion({
  "questions": [{
    "question": "CLAUDE.md는 언제 읽히나요?",
    "header": "Quiz 3-1",
    "options": [
      {"label": "매 대화 시작할 때 자동으로", "description": "Claude가 세션 시작 시 항상 읽음"},
      {"label": "내가 '읽어'라고 할 때만", "description": "수동으로 읽히는 게 아님"},
      {"label": "하루에 한 번", "description": "대화마다 읽음"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### 3-2. Skill

> 공식 문서: https://code.claude.com/docs/ko/skills

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **점진적 로딩(Progressive Disclosure)** — CLAUDE.md는 항상 전부 읽히지만, Skill은 필요할 때만 꺼내 읽힌다. 컨텍스트 윈도우는 유한하므로, 필요한 순간에 필요한 지식만 로딩한다 |
| 비유 | 업무 레시피 — 반복하는 업무를 한 번 저장하면 다음부터 한 줄로 실행 |
| 예시 | "매주 월요일 캠페인 리포트", "일일 브리핑" |

```
CLAUDE.md ━━━━━━━━━━━━━━━━━━━━━━━━━ 항상 로딩 (매 세션)
Skill A   ─ ─ ─ ─ ─ ─ ┐
Skill B   ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
Skill C   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐  필요할 때만 로딩
                       ▼         ▼         ▼
                  "/sync" 호출  자동 매칭  "/wrap" 호출
```

**② 실습**: 지금 이 온보딩 자체가 Skill이다. 아래를 입력해보라고 안내한다:

```
/day1-onboarding
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-2**:

```json
AskUserQuestion({
  "questions": [{
    "question": "지금 이 온보딩 수업은 어떤 기능으로 동작하고 있나요?",
    "header": "Quiz 3-2",
    "options": [
      {"label": "Skill", "description": "미리 저장된 레시피를 실행 중"},
      {"label": "MCP", "description": "외부 도구를 연결한 것"},
      {"label": "Plugin", "description": "패키지를 설치한 것"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번. 지금 이 대화 자체가 Skill의 동작 예시.

---

### 3-3. MCP

> 공식 문서: https://code.claude.com/docs/ko/mcp

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **툴 콜링(Tool Calling)** — AI가 텍스트만 생성하는 게 아니라, "이 함수를 이 파라미터로 호출해"라고 구조화된 요청을 보낸다. MCP는 이 툴 콜링을 외부 서비스까지 확장하는 오픈 표준 |
| 비유 | 외부 도구 플러그 — Slack, Calendar, Notion 등을 Claude에 연결하는 오픈 표준 프로토콜 |
| 예시 | "Slack 메시지 읽어줘", "캘린더 일정 확인해줘" |

```
Claude ──── "Slack에서 메시지 읽어줘"
  │
  ▼ 툴 콜링
┌──────────┐    MCP 프로토콜    ┌──────────┐
│ Claude   │ ◀═══════════════▶ │ Slack    │
│ Code     │    표준 규격       │ Server   │
└──────────┘                   └──────────┘
  내 컴퓨터                      외부 서비스
```

**② 실습**: Claude에게 MCP에 대해 물어보라고 안내한다:

```
MCP가 뭔지 쉽게 설명해줘. 내가 쓸 수 있는 MCP 서버 예시 3개도 알려줘
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-3**:

```json
AskUserQuestion({
  "questions": [{
    "question": "MCP를 한 마디로 말하면?",
    "header": "Quiz 3-3",
    "options": [
      {"label": "Claude와 외부 도구를 연결하는 표준 프로토콜", "description": "Slack, Calendar 등을 플러그처럼 꽂는 것"},
      {"label": "Claude의 내장 기능", "description": "MCP는 외부 연결이지 내장이 아님"},
      {"label": "프로그래밍 언어", "description": "도구 연결 프로토콜임"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### 3-4. Subagent

> 공식 문서: https://code.claude.com/docs/ko/sub-agents

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **프로세스 격리 + Blank Slate** — Subagent는 새로운 Claude Code 프로세스를 띄운다. 메인 대화의 컨텍스트를 물려받지 않고 빈 상태에서 시작해서, 작업만 수행하고 요약만 돌려준다 |
| 비유 | 부하 직원 — 독립된 공간에서 특정 작업을 전담 처리 |
| 예시 | "100페이지 PDF 읽고 핵심만 정리해줘" |

```
┌─ 메인 Claude ──────────────────────────┐
│  대화 컨텍스트: [A, B, C, D, E...]     │
│                                        │
│  "PDF 분석해줘" ──┐                     │
│                   ▼                    │
│  ┌─ Subagent ──────────────┐           │
│  │ 컨텍스트: [blank slate]  │           │
│  │ 작업: PDF 분석            │           │
│  │ 결과: 요약 3줄 ──────────┼──▶ 전달   │
│  └─────────────────────────┘           │
└────────────────────────────────────────┘
```

**② 실습**: Subagent가 실제로 동작하는 걸 체험하라고 안내한다:

```
이 폴더의 파일 구조를 탐색해서 정리해줘. Explore 에이전트를 사용해
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-4**:

```json
AskUserQuestion({
  "questions": [{
    "question": "Subagent는 왜 필요한가요?",
    "header": "Quiz 3-4",
    "options": [
      {"label": "독립된 공간에서 전담 처리시키려고", "description": "메인 대화 컨텍스트를 소비하지 않고 별도 처리"},
      {"label": "Claude가 혼자 못해서", "description": "Claude가 못하는 건 아니고 효율의 문제"},
      {"label": "필수 기능이라서", "description": "없어도 되지만 있으면 강력함"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### 3-5. Agent Teams

> 공식 문서: https://code.claude.com/docs/ko/agent-teams

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **멀티 에이전트** — Subagent가 1:1 위임이라면, Agent Teams는 여러 독립 에이전트가 공유 태스크 리스트와 메시지로 소통하는 구조. 각자 컨텍스트를 갖고 서로 대화하며 협업한다 |
| 비유 | 프로젝트 팀 — 각자 독립 인스턴스로 서로 소통하며 협업 |
| 예시 | "리서치 팀 3명이 각자 조사하고 결과를 공유" |

```
┌─ 리더 ─────────────────────────────────────┐
│                                            │
│  ┌─ Agent A ─┐  ┌─ Agent B ─┐  ┌─ Agent C ─┐
│  │ 시장조사   │  │ 보고서    │  │ 발표자료   │
│  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
│        │              │              │
│        └──────── 메시지 ─────────────┘
│                    +
│            공유 태스크 리스트
└────────────────────────────────────────────┘
  Subagent: 리더에게만 보고
  Teams:    팀원끼리 직접 소통
```

**② 실습**: Claude에게 물어보라고 안내한다:

```
Agent Teams는 Subagent와 뭐가 달라? 실제 사용 예시를 보여줘
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-5**:

```json
AskUserQuestion({
  "questions": [{
    "question": "Agent Teams와 Subagent의 차이는?",
    "header": "Quiz 3-5",
    "options": [
      {"label": "각자 독립 인스턴스에서 서로 소통하며 협업", "description": "Subagent는 보고만, Teams는 팀원끼리 대화"},
      {"label": "Subagent를 여러 개 돌리는 것", "description": "단순 병렬이 아니라 소통 + 공유 태스크 리스트"},
      {"label": "이름만 다름", "description": "구조 자체가 다름"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### 3-6. Hook

> 공식 문서: https://code.claude.com/docs/ko/hooks-guide

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **결정론적 프로그래밍** — AI는 확률적이라 "대부분" 맞지만 100%는 아니다. Hook은 AI가 아니라 코드(셸 스크립트)가 실행되므로 100% 확실하게 동작한다 |
| 비유 | 자동 체크리스트 — 특정 이벤트가 발생하면 자동으로 실행 |
| 예시 | "Claude 응답 완료 시 자동 요약", "도구 실행 전 자동 검증" |

```
AI (확률적)                    Hook (결정론적)
  "파일 저장 후                   파일 저장 이벤트
   포맷팅 해줘"                      │
      │                            ▼
      ▼                     ┌──────────────┐
  대부분 함                  │ prettier     │
  가끔 까먹음                │ --write .    │ ← 100% 실행
                            └──────────────┘
```

**② 실습**: Claude에게 물어보라고 안내한다:

```
Hook이 뭔지 예시와 함께 설명해줘. 내 업무에서 쓸 수 있는 Hook 아이디어 3개도 제안해줘
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-6**:

```json
AskUserQuestion({
  "questions": [{
    "question": "Hook은 언제 실행되나요?",
    "header": "Quiz 3-6",
    "options": [
      {"label": "특정 이벤트가 발생했을 때 자동으로", "description": "응답 완료, 도구 실행 등 이벤트 기반"},
      {"label": "내가 직접 실행할 때", "description": "자동 실행이 핵심"},
      {"label": "매일 정해진 시간에", "description": "스케줄러와는 다름"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### 3-7. Plugin

> 공식 문서: https://code.claude.com/docs/ko/plugins | 마켓플레이스: https://code.claude.com/docs/ko/discover-plugins

**① 설명**

| 항목 | 내용 |
|------|------|
| 근본 원리 | **패키징과 배포** — 위의 기능들은 모두 개별 파일이다. Plugin은 이것들을 하나의 설치 단위로 묶어서, 한 줄로 팀 전체가 동일한 환경을 갖추게 한다 |
| 비유 | 종합 패키지 — 위의 모든 기능을 묶어서 팀에 공유 |
| 예시 | "마케팅 팀용 플러그인" 하나로 Skill + MCP + Hook + Agent 설치 |

```
개별 설치 (Plugin 없이)          Plugin (한 번에)
┌─────────┐                    ┌─────────────────┐
│ Skill A │ ← 수동 복사         │ marketing-plugin│
│ Skill B │ ← 수동 복사         │ ┌─ Skill A,B   │
│ MCP 설정 │ ← 수동 설정   vs   │ ├─ MCP 설정    │
│ Hook 설정│ ← 수동 설정         │ ├─ Hook 설정   │
│ Agent   │ ← 수동 설정         │ └─ Agent       │
└─────────┘                    └────────┬────────┘
  팀원 각자 반복                         │
                              claude plugin add
                                한 줄이면 끝
```

**② 실습**: Claude에게 물어보라고 안내한다:

```
Plugin은 Skill이랑 뭐가 달라? Plugin이 왜 필요한지 설명해줘
```

```json
AskUserQuestion({
  "questions": [{
    "question": "실행해봤나요?",
    "header": "실습 확인",
    "options": [
      {"label": "실행 완료", "description": "다음 퀴즈로 이동"},
      {"label": "건너뛰기", "description": "퀴즈로 바로 이동"}
    ],
    "multiSelect": false
  }]
})
```

**③ Quiz 3-7**:

```json
AskUserQuestion({
  "questions": [{
    "question": "Plugin은 뭘 묶은 것인가요?",
    "header": "Quiz 3-7",
    "options": [
      {"label": "Skill + MCP + Hook + Agent 등을 하나의 패키지로", "description": "여러 기능을 팀 단위로 배포"},
      {"label": "Skill만 묶은 것", "description": "Skill 외에도 MCP, Hook, Agent 포함"},
      {"label": "외부 도구만 묶은 것", "description": "내부 기능도 포함"}
    ],
    "multiSelect": false
  }]
})
```

정답: 1번.

---

### Block 3 마무리

7개 기능의 관계를 정리해서 보여준다:

```
┌─ CLAUDE.md ─── Claude가 매번 읽는 규칙
│
├─ Skill ─────── 반복 업무를 레시피로 저장
│   └─ MCP ───── Slack, Calendar 등 외부 도구 연결
│
├─ Subagent ──── 하나의 작업을 백그라운드에서 처리
│   └─ Agent Teams ── 여러 Subagent가 동시에 작업
│
├─ Hook ──────── 특정 이벤트 발생 시 자동 실행
│
└─ Plugin ────── 위의 모든 것을 묶어서 팀에 공유
```

전부 외울 필요 없다. 모르면 Claude에게 물어보면 된다.

---

## Block 4: Basics

### CLI 기초

| 명령어 | 의미 | 비유 |
|--------|------|------|
| cd 폴더명 | 폴더 이동 | 파인더에서 폴더 더블클릭 |
| ls | 목록 보기 | 폴더 열어서 뭐 있나 보기 |
| pwd | 현재 위치 | 주소창 보기 |

이 3개면 충분하다. Claude Code가 나머지는 다 해준다.

#### Quiz 3

```json
AskUserQuestion({
  "questions": [{
    "question": "내가 지금 어느 폴더에 있는지 확인하는 명령어는?",
    "header": "Quiz 3",
    "options": [
      {"label": "pwd", "description": "Print Working Directory"},
      {"label": "ls", "description": "목록 보기"},
      {"label": "cd", "description": "폴더 이동"},
      {"label": "where", "description": "이런 명령어는 없음"}
    ],
    "multiSelect": false
  }]
})
```

정답: pwd (Print Working Directory)

### git 기초

"보고서_최종.docx → 보고서_최종_진짜.docx → 보고서_최종_찐최종.docx" — git을 쓰면 이런 일이 없다.

| 동작 | 비유 |
|------|------|
| git clone | Google Drive에서 폴더 다운로드 |
| git commit | 게임 세이브 |
| git branch | A안, B안 동시 작업 |
| git push | Google Drive에 업로드 |

git은 실행 취소의 끝판왕이다. 뭘 해도 돌아갈 수 있다.

#### Quiz 4

```json
AskUserQuestion({
  "questions": [{
    "question": "git에서 게임 세이브에 해당하는 동작은?",
    "header": "Quiz 4",
    "options": [
      {"label": "git commit", "description": "현재 상태를 저장"},
      {"label": "git clone", "description": "폴더 다운로드"},
      {"label": "git push", "description": "업로드"},
      {"label": "git save", "description": "이런 명령어는 없음"}
    ],
    "multiSelect": false
  }]
})
```

정답: git commit

### GitHub 기초

| 개념 | 비유 |
|------|------|
| Pull Request (PR) | "이렇게 바꿨는데 괜찮아?" 리뷰 요청 |
| Merge | "오케이, 합치자" 리뷰 통과 후 반영 |

#### Quiz 5

```json
AskUserQuestion({
  "questions": [{
    "question": "GitHub에서 변경사항 리뷰를 요청하는 것은?",
    "header": "Quiz 5",
    "options": [
      {"label": "Pull Request (PR)", "description": "이렇게 바꿨는데 괜찮아? 리뷰 요청"},
      {"label": "Merge", "description": "리뷰 통과 후 합치기"},
      {"label": "git push", "description": "업로드"},
      {"label": "Issue", "description": "버그/기능 요청 등록"}
    ],
    "multiSelect": false
  }]
})
```

정답: Pull Request (PR)

### 에디터 비교

| 에디터 | 특징 | 추천 대상 |
|--------|------|-----------|
| Cursor | AI 내장, CC 시너지 | 코드 편집도 할 분 |
| VSCode | 범용적, 확장 풍부 | 이미 쓰고 계신 분 |
| Antigravity | CC 전용, 가장 간단 | 처음 시작하는 분 |

"이 캠프에서는 터미널에서 claude만 쳐도 충분합니다."
