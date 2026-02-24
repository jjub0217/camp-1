# Vite + React SPA에서 Lighthouse 65점 → 93점까지: 성능 최적화 여정과 한계

> 프론트엔드 취업 준비생이 실제 프로젝트(Cuddle Market)에서 겪은 성능 최적화 과정을 기록합니다.
> Babel이 뭔지도 몰랐던 상태에서 시작해, Lighthouse 성능 점수를 65점에서 93점으로 끌어올리기까지의 과정과, 그래도 해결할 수 없었던 SPA의 근본적 한계를 솔직하게 공유합니다.

---

## 1. 시작: Lighthouse 65점, 무엇이 문제였나

Cuddle Market은 React + Vite + TypeScript 기반의 중고거래 플랫폼입니다. Vercel에 배포한 뒤 Chrome Lighthouse로 성능을 측정해보니 **65점**이었습니다.

Lighthouse가 지적한 주요 문제는 크게 세 가지였습니다:

| 문제 영역 | 예상 절감 효과 |
|----------|-------------|
| 레거시 JavaScript | ~10 KiB |
| 이미지 전송 최적화 | ~30,173 KiB |
| CLS (Cumulative Layout Shift) | 레이아웃 흔들림 |

특히 **이미지 전송 예상 절감이 약 30MB**라는 수치를 보고 놀랐습니다. 이미지 최적화가 가장 임팩트가 클 것이라는 판단이 들었지만, 가장 빠르게 적용할 수 있는 레거시 JavaScript부터 시작했습니다.

---

## 2. 첫 번째 최적화: 레거시 JavaScript 제거

### 문제 발견

Lighthouse는 `@babel/plugin-transform-spread`가 불필요하게 ES6+ 문법을 ES5로 변환하고 있다고 지적했습니다.

솔직히 고백하면, **Babel이 뭔지도 몰랐습니다.** "React 언어를 JavaScript로 변환하는 건가?" 수준이었습니다.

알아보니 Babel은 **JavaScript → JavaScript 변환 도구(트랜스파일러)**였습니다:

```
React (라이브러리) + JSX (문법)
        ↓
   Babel 또는 SWC (변환 도구)
        ↓
   순수 JavaScript (브라우저 실행)
```

### 해결: Vite build.target 설정

문제의 원인은 Vite의 빌드 타겟이 오래된 브라우저까지 지원하도록 설정되어 있어서, **최신 브라우저에서는 필요 없는 변환 코드가 포함**되고 있었던 것입니다.

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    target: 'esnext', // 또는 'es2020' 등 최신 타겟으로 변경
  },
})
```

**결과**: 약 10KiB 절감. 수치는 작지만, 빌드 타겟 설정 하나로 불필요한 폴리필을 제거할 수 있다는 것을 배웠습니다.

---

## 3. 두 번째 최적화: 이미지 전송량 30MB 절감

### 문제 발견

Lighthouse가 보여준 이미지 전송 개선 예상 절감 용량은 **30,173 KiB(약 30MB)**였습니다. 중고거래 특성상 사용자들이 올리는 상품 이미지가 핸드폰 카메라 원본 그대로 업로드되고 있었습니다.

### 해결: 클라이언트 측 이미지 압축

서버에서 처리하는 대신, **업로드 시점에 클라이언트에서 이미지를 압축**하는 방식을 선택했습니다.

`browser-image-compression` 라이브러리를 사용하여:
- 최대 파일 크기 제한
- 최대 가로/세로 크기 제한
- 이미지 품질 조정

사용자가 이미지를 업로드할 때 자동으로 압축이 적용되어, 원본 대비 크게 용량이 줄어들었습니다.

### placeholder 이미지 WebP 전환

코드 내에서 사용하던 placeholder 이미지도 PNG에서 WebP로 전환했습니다.

- 브랜치: `refactor/596--placeholder-webp-home-loading`
- 8개 파일에서 `placeholder.png` → `placeholder.webp` 변경
- WebP는 PNG 대비 약 25~35% 더 작은 용량

---

## 4. 세 번째 최적화: CLS 개선 (레이아웃 흔들림)

### 문제 발견

CLS(Cumulative Layout Shift)는 페이지가 로드되면서 요소들이 갑자기 밀려나는 현상입니다. 사용자가 버튼을 누르려는데 갑자기 레이아웃이 바뀌어 다른 곳을 누르게 되는 문제죠.

### 해결: 스켈레톤 UI → 로딩 스피너 전략 변경

처음에는 스켈레톤 UI를 사용했는데, 오히려 스켈레톤과 실제 콘텐츠 사이의 크기 차이가 CLS를 발생시키고 있었습니다.

Home 페이지에서 스켈레톤 UI를 제거하고 로딩 스피너로 대체하여, 콘텐츠가 한 번에 렌더링되도록 변경했습니다.

### LCP 이미지 최적화

LCP(Largest Contentful Paint) 이미지에 `fetchPriority`와 `loading` 속성을 적용했습니다:

```tsx
<img
  src={mainImage}
  fetchPriority="high"    // 브라우저에게 "이 이미지 먼저 로드해" 알림
  loading="eager"          // 지연 로딩 하지 않음
/>
```

---

## 5. 네 번째 최적화: FCP 병목 분석 (DebugBear)

### DebugBear Waterfall 차트 활용

Lighthouse만으로는 구체적인 네트워크 병목을 파악하기 어려워, **DebugBear**라는 도구로 waterfall 차트를 분석했습니다.

FCP(First Contentful Paint)가 느린 원인을 찾기 위해 waterfall 차트를 캡처하고 분석한 결과, 리소스 로딩 순서와 크기에서 개선점을 발견할 수 있었습니다.

---

## 6. 결과: 65점 → 93점

모든 최적화를 적용한 후, Lighthouse 성능 점수는 **93점**까지 올라갔습니다.

### 최적화 타임라인 (커밋 기록 기반)

| 날짜 | 작업 | 관련 기술 |
|------|------|----------|
| 1/26 | Lighthouse 65점 → 레거시 JS 개선 시작 | Vite build.target |
| 1/26 | 이미지 전송 최적화 (클라이언트 압축) | browser-image-compression |
| 1/26 | LCP 이미지 최적화 | fetchPriority, loading 속성 |
| 1/26 | Home 페이지 스켈레톤 UI → CLS 개선 | 스켈레톤 UI 제거 |
| 1/26 | placeholder 이미지 WebP 변환 | PNG → WebP |
| 1/27 | FCP waterfall 분석 | DebugBear |
| 1/27 | 최종 Lighthouse **93점** 달성 | - |

놀라운 점은, **이 모든 작업이 단 이틀** 만에 이루어졌다는 것입니다.

---

## 7. 한계: SPA에서 할 수 없는 것들

93점까지 올렸지만, **Vite + React SPA 구조에서는 근본적으로 해결할 수 없는 문제들**이 있었습니다.

### SEO의 근본적 한계

SPA(Single Page Application)는 빈 HTML을 보내고, JavaScript가 실행된 후에야 콘텐츠가 렌더링됩니다.

```html
<!-- SPA가 보내는 HTML -->
<body>
  <div id="root"></div>
  <script src="/assets/index.js"></script>
</body>
```

검색엔진 크롤러가 이 페이지를 방문하면 **빈 페이지**를 봅니다. 콘텐츠가 없으니 검색 결과에 제대로 노출되지 않습니다.

### FCP/LCP의 구조적 한계

SPA는 반드시 이 순서를 거칩니다:

```
HTML 다운로드 → JS 번들 다운로드 → JS 실행 → API 호출 → 렌더링
```

아무리 JS 번들을 줄이고 이미지를 최적화해도, **"JS가 실행되어야 콘텐츠가 보인다"**는 구조 자체를 바꿀 수 없습니다. SSR(Server-Side Rendering)이라면:

```
HTML 다운로드 → 콘텐츠 바로 표시 (JS 없이도)
```

### 결론: Next.js 마이그레이션

결국 **SPA에서 할 수 있는 최적화는 모두 했지만, 근본적인 SEO와 초기 로딩 속도는 SSR/SSG 없이 해결할 수 없다**는 결론에 도달했습니다.

이를 위해 **Cuddle Market을 Next.js로 마이그레이션**하는 계획을 세웠습니다:

- SSR로 검색엔진에 완전한 HTML 제공
- `next/image`로 자동 이미지 최적화
- 페이지별 자동 코드 스플리팅
- 빌트인 폰트 최적화

---

## 8. 배운 것들

### 기술적으로 배운 것
- **Lighthouse 점수는 이미지 최적화가 가장 임팩트가 크다** (30MB → 수 MB)
- **Vite build.target** 하나로 불필요한 폴리필 제거 가능
- **CLS는 스켈레톤 UI가 오히려 원인**이 될 수 있다
- **fetchPriority="high"**로 LCP 이미지 우선 로딩
- **DebugBear waterfall**로 네트워크 병목 시각적 분석

### 과정에서 배운 것
- Babel이 뭔지도 몰랐지만, **문제를 하나씩 해결하면서 자연스럽게 학습**할 수 있었다
- 완벽한 이해 없이도 **Lighthouse가 알려주는 힌트를 따라가면** 상당한 개선이 가능하다
- 하지만 **근본적인 아키텍처 한계**는 결국 직면하게 된다 — 이것을 아는 것도 중요한 배움이다

---

*이 글은 AI Native Camp에서 Claude Code의 History Insight 기능을 활용하여, 과거 작업 세션을 분석하고 작성했습니다.*
