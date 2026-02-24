# MacBook 환경 설정 가이드

Mac Mini에서 작업한 camp-1 프로젝트를 MacBook에서 이어서 사용하기 위한 설정 가이드.

---

## 1. 사전 준비 (필수 도구 설치)

```bash
# Homebrew (없으면 설치)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Node.js (v22 권장)
brew install node

# GitHub CLI
brew install gh

# GitHub 로그인
gh auth login
```

---

## 2. 프로젝트 클론

```bash
cd ~/Desktop
git clone https://github.com/jjub0217/camp-1.git
cd camp-1
```

---

## 3. Claude Code 설치

```bash
npm install -g @anthropic-ai/claude-code
```

---

## 4. Claude Code 플러그인 설치

Claude Code 실행 후 아래 명령어로 플러그인을 설치한다.

```
/install-plugin frontend-design@claude-plugins-official
/install-plugin superpowers@claude-plugins-official
/install-plugin code-simplifier@claude-plugins-official
```

---

## 5. Claude Code 전역 설정

`~/.claude/settings.json` 파일을 아래 내용으로 생성한다.

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "enabledPlugins": {
    "frontend-design@claude-plugins-official": true,
    "superpowers@claude-plugins-official": true,
    "code-simplifier@claude-plugins-official": true
  }
}
```

---

## 6. 프로젝트 로컬 설정

`.claude/settings.local.json`은 이미 레포에 포함되어 있으므로 clone하면 자동 적용된다.

현재 설정 내용:
- **outputStyle**: Explanatory (교육적 설명 모드)
- **hooks**: Stop 이벤트 시 완료 시간 출력

---

## 7. MCP 서버 설정 (필요 시)

캠프에서 사용한 MCP 서버가 있으면 Claude Code에서 다시 연결한다.

```bash
# Notion MCP (예시)
claude mcp add notion -- npx -y @notionhq/notion-mcp-server

# Context7 MCP (예시)
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

> MCP 서버 설정은 `~/.claude/` 디렉토리에 저장되며 Git에 포함되지 않는다.
> 각 MCP 서버에 필요한 API 키/토큰은 별도로 발급받아야 한다.

---

## 8. Git 리모트 확인

```bash
cd ~/Desktop/camp-1
git remote -v
# origin  https://github.com/jjub0217/camp-1.git (fetch/push)

# 원본 캠프 레포도 연결하고 싶으면:
git remote add upstream https://github.com/ai-native-camp/camp-1.git
```

---

## 9. 확인 체크리스트

- [ ] `claude` 명령어 실행 가능
- [ ] `/day1-onboarding` 등 스킬 호출 가능
- [ ] `gh auth status`로 GitHub 로그인 확인
- [ ] MCP 서버 연결 확인 (Notion, Context7 등)

---

## 환경 정보 (Mac Mini 기준)

| 항목 | 버전 |
|------|------|
| Claude Code | 2.1.51 |
| Node.js | v22.22.0 |
| Python | 3.9.6 |
| GitHub CLI | 2.83.1 |
| OS | macOS (Darwin 25.2.0) |
