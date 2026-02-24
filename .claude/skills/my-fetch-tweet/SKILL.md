---
name: my-fetch-tweet
description: X/Twitter URL을 받으면 트윗 원문을 가져와서 요약-인사이트-전체 번역을 제공하는 스킬. "트윗 번역", "트윗 가져와", "X 게시글" 요청에 사용.
---

# my-fetch-tweet

## Step 1: URL 파싱 및 API 연동

### 지원 URL 형식

아래 도메인의 트윗 URL을 모두 지원한다:
- `x.com`
- `twitter.com`
- `fxtwitter.com`
- `fixupx.com`

### URL 변환 규칙

사용자가 제공한 URL에서 `screen_name`과 `status_id`를 추출한 뒤, FxEmbed API URL로 변환한다.

```
입력 예시: https://x.com/garrytan/status/1234567890
                        ↓
추출: screen_name = garrytan, status_id = 1234567890
                        ↓
API URL: https://api.fxtwitter.com/garrytan/status/1234567890
```

### WebFetch로 데이터 가져오기

변환된 API URL을 WebFetch 도구로 호출하여 JSON 데이터를 가져온다.

```
WebFetch({
  url: "https://api.fxtwitter.com/{screen_name}/status/{status_id}",
  prompt: "Extract the full tweet text, author name, and engagement metrics"
})
```

### 주요 JSON 필드

| 필드 | 설명 |
|------|------|
| `tweet.text` | 트윗 본문 (전체 텍스트) |
| `tweet.author.name` | 작성자 이름 |
| `tweet.author.screen_name` | 작성자 핸들 (@xxx) |
| `tweet.likes` | 좋아요 수 |
| `tweet.retweets` | 리트윗 수 |
| `tweet.views` | 조회수 |
| `tweet.replies` | 답글 수 |
| `tweet.created_at` | 작성 시간 |
| `tweet.media` | 첨부 미디어 (이미지, 동영상) |
| `tweet.quote` | 인용 트윗 (있는 경우) |

## Step 2: 번역 파이프라인

API에서 가져온 트윗 데이터를 3단계로 처리한다. 전체 번역을 바로 보여주지 않고, 핵심부터 파악한 뒤 전체를 읽는 순서로 제공한다.

### 1단계: 요약 (3-5문장)

트윗의 핵심을 30초 만에 파악할 수 있도록 한국어로 요약한다.

포함할 내용:
- 트윗의 핵심 주장 또는 메시지
- 작성자 정보 (이름, 직함/소속이 파악되면 포함)
- 인게이지먼트 수치 (좋아요, 리트윗, 조회수)

출력 형식:
```
📋 요약
(작성자 이름) (@핸들)이 (주제)에 대해 이야기했다.
(핵심 주장 2-3문장)
반응: ❤️ (좋아요) | 🔁 (리트윗) | 👁️ (조회수)
```

### 2단계: 인사이트 (3개)

요약을 바탕으로 더 깊은 분석을 제공한다.

| 인사이트 | 질문 | 설명 |
|----------|------|------|
| 핵심 메시지 | "이 트윗이 정말 말하고 싶은 것은?" | 표면적 내용 너머의 핵심 의도 |
| 시사점 | "업계/트렌드에서 어떤 의미인가?" | 더 넓은 맥락에서의 의미 |
| 적용점 | "나(독자)에게 어떤 의미인가?" | 실제로 활용할 수 있는 포인트 |

출력 형식:
```
💡 인사이트
1. 핵심 메시지: (내용)
2. 시사점: (내용)
3. 적용점: (내용)
```

### 3단계: 전체 번역

원문 전체를 자연스러운 한국어로 번역한다.

번역 규칙:
- 직역이 아닌 자연스러운 한국어로 의역
- 전문 용어는 원문 병기: 예) "에이전트(Agent)", "파인튜닝(Fine-tuning)"
- 인용 트윗이 있으면 함께 번역
- 스레드(연결 트윗)가 있으면 순서대로 번역

출력 형식:
```
📝 전체 번역
(번역된 트윗 본문)

> 인용: (인용 트윗 번역 — 있는 경우)
```

## Step 3: WebFetch Fallback

스크립트 실행이 어렵거나 API 연동에 문제가 있을 때, WebFetch 도구로 직접 호출하는 대안이다.

### 사용 방법

```
WebFetch({
  url: "https://api.fxtwitter.com/{screen_name}/status/{status_id}",
  prompt: "Extract the full tweet text, author name, and engagement metrics"
})
```

### 언제 Fallback을 쓰는가?

- JSON 파싱이 실패했을 때
- API 응답 형식이 예상과 다를 때
- 빠르게 트윗 내용만 확인하고 싶을 때

### Fallback 후 처리

WebFetch 결과를 받으면 Step 2(번역 파이프라인)를 동일하게 적용한다:
1. 요약 (3-5문장)
2. 인사이트 (3개)
3. 전체 번역
