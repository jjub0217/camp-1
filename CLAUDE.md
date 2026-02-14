# AI Native Camp - 1기 커리큘럼

> 비개발자를 위한 Claude Code 활용법 7일 집중 캠프

## 사용 가능한 스킬

| 스킬 | 트리거 | 용도 |
|------|--------|------|
| `/day1-onboarding` | "1일차", "Day 1", "온보딩" | Day 1 강사용 교안 (상세) |
| `/day2-sync` | "2일차", "Day 2", "sync" | Day 2 강사용 교안 |
| `/day3-clarify` | "3일차", "Day 3", "clarify" | Day 3 강사용 교안 |
| `/day4-wrap` | "4일차", "Day 4", "wrap" | Day 4 강사용 교안 |

## 커리큘럼 구조

| Day | 요일 | 주제 | 핵심 스킬 |
|-----|------|------|----------|
| 1 | 토(2/14) | Onboarding | Working Backward + CC 설치 + CLI/git |
| 2 | 월(2/17) | Communication | `/sync` — 모든 소스에서 정보 수집 |
| 3 | 화(2/18) | Requirements | `/clarify` — 모호함을 명확하게 |
| 4 | 수(2/19) | Documentation | `/wrap` — 마무리 + 자동화 + subagent |
| 5 | 목(2/20) | Information | Contents Hub MCP |
| 6 | 금(2/21) | Use Case | ACE 초대, 실전 Use Case |
| 7 | 토(2/22) | Graduation | 퇴소식 |

## 대상

- 참가자 45명 + 엠버서더 10명
- Claude Code Max 구독 비개발자
- CLI 경험 거의 없음

## 운영진

| 이름 | 역할 |
|------|------|
| 구봉 | 기술 파트 (핸즈온 실습) |
| Zoon | 철학 파트 (Why 세션 + 과제) |
| Sung | 참가자 서포트 |

## Claude 사용 규칙

### Claude Code 질문 대응
- Claude Code 관련 질문은 `claude-code-guide` 에이전트로 답변한다
- 답변 후 사용자가 직접 따라할 수 있게 단계별로 안내한다
- 내장 에이전트 답변이 의심스러우면 공식 문서를 직접 탐색한다:
  1. `curl`로 해당 문서 페이지를 파일로 저장
  2. Read 툴로 꼼꼼히 읽고 정확한 정보 확인
  3. WebFetch는 사용하지 않는다 (요약/손실 위험)

### 사용자 인터랙션
- 사용자에게 질문하거나 답변을 받아야 할 때는 항상 `AskUserQuestion`을 사용한다
