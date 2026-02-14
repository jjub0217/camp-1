# Block 3-7: Plugin

> **Phase A 시작 시 반드시 아래 형태로 출력한다:**
> ```
> 📖 공식 문서: https://code.claude.com/docs/ko/plugins
> 📖 마켓플레이스: https://code.claude.com/docs/ko/discover-plugins
> ```

## EXPLAIN

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

## EXECUTE

Claude에게 물어보라고 안내한다:

```
Plugin은 Skill이랑 뭐가 달라? Plugin이 왜 필요한지 설명해줘
```

## QUIZ

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
