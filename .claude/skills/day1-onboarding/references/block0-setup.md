# Block 0: Setup

## EXPLAIN

> **Phase A 시작 시 반드시 아래 형태로 출력한다:**
> ```
> 📖 공식 문서: https://code.claude.com/docs/ko/setup
> 📖 빠른 시작: https://code.claude.com/docs/ko/quickstart
> ```

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

### Output Style 설정

로그인이 끝나면 아래 순서로 Output Style을 설정하라고 안내한다:

1. Claude Code 대화창에 `/output-style` 입력
2. 선택지에서 **Explanatory** 선택

```
/output-style
→ Explanatory 선택
```

> Explanatory 모드로 설정하면 Claude가 코드를 작성할 때 왜 그렇게 하는지 설명을 함께 해준다. 배우는 단계에서 가장 적합한 모드다.

## EXECUTE

참가자에게 순서대로 실행하라고 안내한다:

1. **설치**: 위 명령어로 Claude Code 설치
2. **첫 실행**: 터미널에서 `claude` 입력
3. **첫 대화**: "안녕, 나는 [이름]이고 [직업]이야. 나한테 인사해줘"
4. **자유 대화**: 5분간 자유롭게 대화

## QUIZ

```json
AskUserQuestion({
  "questions": [{
    "question": "설치와 첫 대화까지 완료했나요?",
    "header": "Setup 확인",
    "options": [
      {"label": "모두 완료!", "description": "Block 1로 이동"},
      {"label": "아직 진행 중", "description": "더 시간이 필요함"},
      {"label": "트러블슈팅 필요", "description": "설치나 실행에 문제 발생"}
    ],
    "multiSelect": false
  }]
})
```

> Block 0은 퀴즈 대신 완료 확인만 한다.
