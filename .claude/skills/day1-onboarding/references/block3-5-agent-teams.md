# Block 3-5: Agent Teams

> **Phase A 시작 시 반드시 아래 형태로 출력한다:**
> ```
> 📖 공식 문서: https://code.claude.com/docs/ko/agent-teams
> ```

## EXPLAIN

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

### 라이브 데모

> 강사가 iTerm2로 전환하여 `tmux -CC`를 실행한다.
> 여러 터미널 패널에서 Agent들이 동시에 동작하는 모습을 실시간으로 보여준다.
> "지금 보시는 것처럼 각 Agent가 독립된 터미널에서 동시에 작업하고, 서로 메시지를 주고받습니다."

## EXECUTE

Claude에게 물어보라고 안내한다:

```
Agent Teams는 Subagent와 뭐가 달라? 실제 사용 예시를 보여줘
```

## QUIZ

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
