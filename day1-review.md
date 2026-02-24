# Day 1 온보딩 복습 노트

> AI Native Camp 1기 — 강주현
> 작성일: 2026-02-21

---

## 1. 터미널에서 Claude Code 시작/관리

| 명령어 | 의미 | 참고 |
|--------|------|------|
| `claude` | 새 세션 시작 (컨텍스트 0%에서 시작) | |
| `claude --continue` | 마지막 세션 이어가기 | 컨텍스트가 이전 상태 그대로 유지됨 |
| `claude --resume` | 과거 세션 목록에서 골라서 이어가기 | |

- 새 세션 = 컨텍스트 리셋 (CLAUDE.md만 다시 읽힘)
- `--continue` = 이전 대화 내용 + 컨텍스트 사용률 그대로 유지

---

## 2. Claude Code `/` 내장 명령어

Claude에게 보내는 메시지가 아니라, Claude Code 자체의 기능.

| 명령어 | 의미 | 주의사항 |
|--------|------|----------|
| `/init` | CLAUDE.md 자동 생성 (프로젝트 분석 후 작성) | |
| `/memory` | 메모리 설정 메뉴 열기 | MEMORY.md를 생성하는 게 아니라 설정 화면을 여는 것. VS Code 통합 터미널에서 포커스 문제 발생 가능 |
| `/plugin` | 플러그인 검색/설치 메뉴 열기 | 이미 등록된 마켓플레이스의 플러그인만 검색됨 |
| `/statusline` | Status Line 설정 | |

---

## 3. `/` 스킬 명령어

Skill을 호출하는 명령어. `.claude/skills/스킬이름/SKILL.md`가 존재해야 동작함.

| 명령어 | 의미 |
|--------|------|
| `/day1-onboarding` | Day 1 온보딩 스킬 |
| `/day2-supplement-mcp` | Day 2 MCP 보충 스킬 |
| `/day2-create-context-sync-skill` | Day 2 Context Sync 스킬 |
| `/day4-wrap-and-analyze` | Day 4 세션 분석 스킬 |
| `/day5-fetch-and-digest` | Day 5 콘텐츠 소화 스킬 |
| `/day6-prd-submit` | Day 6 PRD 제출 스킬 |

- 교안(references/)에 텍스트가 있다고 스킬이 설치된 것은 아님
- 스킬 폴더(`SKILL.md`)가 실제로 존재해야 동작함

---

## 4. `!` 접두사 (직접 실행)

| 입력 방식 | 동작 |
|-----------|------|
| `이 폴더 뭐 있어?` | Claude에게 시킴 → Claude가 판단해서 실행 |
| `!ls` | Claude 안 거치고 **직접** 터미널에서 실행 |

- `!` = "Claude한테 시키지 말고 내가 직접 실행할게"
- `!` 앞에 공백이 있으면 일반 메시지로 전달되니 주의

---

## 5. 설정 파일 구분

| 파일 | 위치 | 적용 범위 |
|------|------|----------|
| `~/.claude/settings.json` | 홈 폴더 | **모든 프로젝트** 공통 (전역) |
| `.claude/settings.local.json` | 프로젝트 폴더 | **이 프로젝트만** (로컬) |
| `./CLAUDE.md` | 프로젝트 루트 | 이 프로젝트의 영구 기억 |
| `~/.claude/CLAUDE.md` | 홈 폴더 | 모든 프로젝트 공통 영구 기억 |

### 오늘 설정한 것들

| 설정 | 파일 | 범위 |
|------|------|------|
| Status Line | `~/.claude/settings.json` | 전역 |
| Agent Teams | `~/.claude/settings.json` | 전역 |
| Stop Hook (시간 출력) | `.claude/settings.local.json` | 이 프로젝트만 |

### Agent Teams 활성화 방법

Claude에게 말로 시키면 됨:
```
내 settings.json에 Agent Teams를 활성화하는 설정을 추가해줘
```
→ Claude가 `~/.claude/settings.json`에 아래 내용을 추가해줌:
```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```
> 기본적으로 꺼져 있어서 직접 활성화해야 함. 실험적(experimental) 기능

---

## 6. Hook 이벤트

"언제"는 4개 중에서 고르고, "뭘 할지"만 자유롭게 정하는 구조.

| 이벤트 이름 | 한국어 의미 | 예시 |
|------------|------------|------|
| **Stop** | 응답이 끝나면 | 현재 시간 출력 |
| **PreToolUse** | 도구 실행 직전에 | 위험한 명령 차단, 린트 체크 |
| **PostToolUse** | 도구 실행 직후에 | 자동 포맷팅 |
| **Notification** | 알림 발생 시 | 소리 울리기 |

- 이벤트 이름은 Claude Code가 미리 정해놓은 것 (내가 짓는 게 아님)
- Hook은 AI가 아니라 코드(셸 스크립트)가 실행 → 100% 확실하게 동작

### Hook 추가 방법

Claude에게 말로 시키면 됨:
```
Stop Hook을 추가해줘. 응답이 끝나면 현재 시간을 터미널에 출력하도록
```
→ Claude가 `.claude/settings.local.json`에 Hook 설정을 자동으로 추가해줌

다른 예시:
```
PreToolUse Hook을 추가해줘. Bash 실행 전에 로그를 남기도록
```

삭제하고 싶으면:
```
Stop Hook 삭제해줘
```

---

## 7. 7가지 핵심 기능 관계도

```
┌─ CLAUDE.md ─── 항상 읽히는 영구 기억 (회사 규칙서)
│
├─ Skill ─────── 필요할 때 꺼내는 레시피 (업무 매뉴얼)
│   └─ MCP ───── 외부 도구 연결 표준 (USB 플러그)
│
├─ Subagent ──── 독립 공간에서 전담 처리 (부하 직원)
│   └─ Agent Teams ── 여러 에이전트가 소통하며 협업 (프로젝트 팀)
│
├─ Hook ──────── 이벤트 발생 시 자동 실행 (자동 체크리스트)
│
└─ Plugin ────── 위의 모든 것을 묶어서 공유 (앱스토어)
```

| # | 기능 | 한 마디 | 로딩 시점 |
|---|------|---------|----------|
| 1 | CLAUDE.md | 항상 읽히는 영구 기억 | 매 세션 시작 시 자동 |
| 2 | Skill | 필요할 때 꺼내는 레시피 | `/명령어` 호출 시 |
| 3 | MCP | 외부 도구 연결 표준 | 설정 후 항상 |
| 4 | Subagent | 독립 공간에서 전담 처리 | 요청 시 |
| 5 | Agent Teams | 여러 에이전트가 소통하며 협업 | 요청 시 |
| 6 | Hook | 이벤트 발생 시 자동 실행 | 이벤트 발생 시 |
| 7 | Plugin | 모든 것을 묶어서 공유 | 설치 시 |

---

## 8. 컨텍스트 윈도우 핵심 개념

- **컨텍스트 윈도우** = Claude가 한 번에 볼 수 있는 최대 크기
- 대화가 길어지면 → 컨텍스트가 차면 → 오래된 대화 자동 압축(요약) → 세부사항 유실 가능
- **100%가 되어도 세션이 끊기지 않음** → 자동 압축 후 계속 대화 가능
- Status Line으로 실시간 확인 가능: `[Opus] ▓▓░░░░░░░░ 25%`
- **토큰 사용량 ≠ 비용**. Claude Max는 비용 무제한이지만 컨텍스트 창 크기는 유한

### Status Line 설정 방법 (컨텍스트 사용률 실시간 확인)

이미 설정 완료된 상태. 만약 다시 설정해야 할 경우:

1. Claude Code 세션에서 아래 입력:
   ```
   /statusline 모델 이름과 컨텍스트 사용률을 프로그레스 바로 보여줘
   ```
2. Claude가 알아서 스크립트 생성 + `~/.claude/settings.json`에 등록해줌
3. Claude Code 재시작 후 화면 하단에 표시됨

또는 수동으로 설정하려면:
1. `~/.claude/settings.json`에 아래 추가:
   ```json
   {
     "statusLine": {
       "type": "command",
       "command": "bash ~/.claude/statusline-command.sh"
     }
   }
   ```
2. `~/.claude/statusline-command.sh` 스크립트 생성 (Claude에게 시키면 됨)

> 퍼센트가 높아지면 "새 세션을 열어야겠다"는 판단 기준이 됨

### 실무 팁

- 주제가 바뀌면 새 세션(`claude`) 시작
- 중요한 규칙은 CLAUDE.md에 적어두기 (세션이 바뀌어도 유지)
- Subagent를 활용하면 메인 컨텍스트 절약 가능 (작업 과정이 아닌 결과 요약만 전달)

---

## 9. git / GitHub

| 명령어 | 비유 |
|--------|------|
| `git clone` | Google Drive에서 폴더 다운로드 |
| `git commit` | 게임 세이브 |
| `git branch` | A안, B안 동시 작업 |
| `git push` | Google Drive에 업로드 |
| **Pull Request (PR)** | "이렇게 바꿨는데 괜찮아?" 리뷰 요청 |
| **Merge** | "오케이, 합치자" 리뷰 통과 후 반영 |

---

## 10. CLI 기본 명령어

| 명령어 | 비유 |
|--------|------|
| `cd 폴더명` | 파인더에서 폴더 더블클릭 |
| `ls` | 폴더 열어서 뭐 있나 보기 |
| `pwd` | 주소창 보기 (현재 위치) |
| `cat 파일명` | 메모장으로 파일 열기 |
| `open 파일명` | 파인더에서 더블클릭 (Mac) |

> 외울 필요 없음. Claude에게 말로 시키면 됨

---

## 11. 오늘 설치한 플러그인

| 플러그인 | 역할 |
|---------|------|
| frontend-design | 프론트엔드 UI 디자인 품질 향상 |
| superpowers | 개발 워크플로우 강화 (TDD, 디버깅, 코드리뷰 등) |
| code-simplifier | 코드 간결화 |

### 플러그인 설치 방법

1. Claude Code 세션에서 `/plugin` 입력
2. 검색창이 뜨면 원하는 플러그인 이름 검색
3. 선택하면 설치됨
4. **Claude Code 재시작 후 적용됨** (`claude` 다시 실행)

> 커뮤니티 마켓플레이스의 플러그인은 기본 검색창에 안 뜸. 마켓플레이스를 먼저 등록해야 검색 가능

---

## 12. 핵심 요약

```
/  = Claude Code 내장 명령어 또는 스킬 호출
!  = 직접 터미널 실행
아무것도 안 붙임 = Claude에게 말로 시키기 ← 가장 많이 쓰는 방식
```

모르면 Claude에게 물어보면 된다!
