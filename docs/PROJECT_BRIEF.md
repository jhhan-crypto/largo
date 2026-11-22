# 갤러리 웹사이트 개발 브리프 (SEO · GEO · AEO 최적화)

> 이 문서는 다른 Claude Code 세션이 **추가 질문 없이** 개발을 시작할 수 있도록 정리한 요구사항 문서입니다.
> 아직 코드는 작성하지 않았습니다. 결정된 사항(✅)과 미정 사항(❓)을 구분해 두었습니다.

## 1. 프로젝트 개요

- **업종**: 갤러리(미술 전시 공간) 업체 공식 사이트
- **목적**: 전시·작가·작품을 소개하고, 예술계 소식과 인사이트를 연재한다.
- **핵심 목표**: 검색엔진(SEO), 생성형 AI 엔진(GEO), 답변 엔진(AEO)에서 모두 잘 노출·인용되는 사이트
- **레포**: `jhhan-crypto/largo` (현재 커밋 없음, 빈 레포)
- **갤러리 이름**: ✅ **AVIO Gallery** (영문 표기 그대로 사용, 변형 문자·특수 문자 사용 금지). 한글 표기는 ✅ **아비오 갤러리**. `src/config/site.ts` 한 곳에서 정의하고 `<title>`, `alt`, JSON-LD `name`, 푸터, 도메인·SNS·플레이스 등록명 모두 `AVIO Gallery`(한국어 페이지 본문은 `아비오 갤러리`)로 통일한다.

## 2. 확정된 결정 사항

| 항목 | 결정 |
|---|---|
| 프레임워크 | ✅ **Astro** (정적 생성, SSG) |
| 콘텐츠 관리 | ✅ **개발자가 직접 관리** → CMS 없이 Astro Content Collections + 마크다운/MDX 파일 |
| 언어 | ✅ **한국어(기본) + 영어** 2개 언어 |
| 작품 판매 | ✅ **구매 문의만** (온라인 결제 없음). 작품마다 문의 버튼/폼 |
| 브랜드 | ✅ 아래 3절 참고 |

## 3. 브랜드 가이드

- **로고**: `docs/assets/logo-original.png` (595×842 PNG, 흰 배경)
  - 보라색 기하학적 "A" 형태의 심볼 마크. 텍스트(워드마크)는 없음.
  - ⚠️ 래스터(PNG)이므로 개발 시 **SVG로 벡터화**해 `public/logo.svg`로 사용한다(단색 도형이라 벡터화가 쉬움). 흰 배경은 투명 처리하고 여백을 잘라낸 정사각형 버전을 함께 만든다.
  - 파비콘(`favicon.svg`, `favicon.ico`, `apple-touch-icon.png` 180px), OG 이미지(1200×630)도 이 로고로 생성한다.
- **브랜드 컬러**: 로고에서 추출한 보라색 `#775DA0` (RGB 119, 93, 160)
  - 이 색을 기준으로 CSS 변수(`--color-brand`)와 밝기 단계(50~900) 팔레트를 만든다.
  - 흰 배경 위 텍스트 대비(WCAG AA 4.5:1)를 확인한다. `#775DA0`은 흰 배경에서 약 5.2:1이므로 링크·버튼 배경에 사용 가능하나, 작은 텍스트에는 더 어두운 단계를 쓴다.
- **디자인 방향**: **갤러리다운 미니멀 화이트 큐브 스타일** + 레퍼런스(3-1절)의 몰입감 있는 전환·갤러리 연출. 흰 배경, 작품 이미지가 주인공, 넉넉한 여백, 보라색은 포인트로만 사용, 타이포 중심 레이아웃.
- **폰트**: 한글은 Pretendard(또는 Noto Sans KR), 영문 제목은 세리프 계열을 고려. 폰트는 셀프 호스팅한다(성능, 개인정보).

### 3-1. 디자인 레퍼런스 (Awwwards)

| # | 사이트 | 등급 | 핵심 특징 | AVIO에 가져올 점 |
|---|---|---|---|---|
| 1 | [Woodwrights](https://www.awwwards.com/sites/woodwrights) (원 사이트 woodwrights.co.nz) | Honorable Mention | 클린·미니멀, 큰 배경 사진, 사진 중심, 스크롤 기반 갤러리. 팔레트 `#fbfbf9` / `#262525`. Material Library 같은 갤러리형 요소 | **기본 톤의 기준.** 따뜻한 오프화이트 배경 + 거의 검정에 가까운 텍스트, 큰 작품 사진, 절제된 UI. 순백 대신 오프화이트 사용 |
| 2 | [A24](https://www.awwwards.com/sites/a24) (원 사이트 a24.raviklaassens.com) | Site of the Day (7.65) | 흑백(`#000` / `#F2F2F2`), 3D 디스크 기반 탐색, 페이지 전환(Barba.js), 프리로더→히어로 전환, 개성 있는 404. 기술: GSAP, Three.js | **전환 연출.** 페이지 간 부드러운 전환, 프리로더→히어로, 전시 목록→상세 전환, 개성 있는 404 페이지 |
| 3 | [365days PROJECT in 2024](https://www.awwwards.com/sites/365days-project-in-2024) (원 사이트 kamimae.com/365days) | Honorable Mention | 366점의 작품을 몰입형 가상 갤러리로 제시. 흑백(`#fff` / `#000`), 인트로, 탑다운 뷰, **포커스 모드**(작품 하나에 집중), 사운드. 기술: Three.js, TypeScript, Vite | **작품 감상 경험.** 작품 상세의 "포커스 모드"(전체 화면 확대 뷰어), 전체 작품을 한눈에 보는 격자/탑다운 뷰 |

> 위 정리는 Awwwards 소개 페이지(요약·팔레트·기술 태그·요소 목록)를 기준으로 한 것이다. 개발 세션에서 **원 사이트를 직접 열어** 간격, 타이포, 모션 속도를 추가로 확인하면 더 정확하다.

**적용 원칙 (중요: 레퍼런스의 "느낌"은 가져오되 SEO·접근성은 해치지 않는다)**

- 레퍼런스 2·3번은 WebGL/3D 중심의 실험적 사이트다. Awwwards 개발 평가에서도 A24는 접근성 6.8/10, 마크업·메타데이터 7.0/10으로 상대적으로 낮다. 이런 구조를 그대로 쓰면 이 프로젝트의 최우선 목표인 SEO·GEO·AEO와 충돌한다.
- 따라서 **모든 콘텐츠(작가·작품·전시 정보, 텍스트, 이미지 alt)는 일반 HTML로 먼저 제공**하고, 3D·전환 효과는 **점진적 향상(progressive enhancement)** 으로만 얹는다. JS가 꺼져 있어도 크롤러와 AI가 모든 정보를 읽을 수 있어야 한다.
- 전환 효과는 Astro 내장 View Transitions(`<ClientRouter />`)를 우선 사용하고, 무거운 라이브러리(Three.js 등)는 **홈 히어로나 포커스 모드 같은 선택 구간에만** 지연 로딩한다. 기본은 순수 CSS·가벼운 JS로 구현한다.
- `prefers-reduced-motion` 설정을 존중해 모션을 줄이고, 키보드 조작·포커스 표시·색 대비를 유지한다. 모션 때문에 Lighthouse 점수(95+)와 LCP가 떨어지지 않아야 한다.
- 색상은 흑백 레퍼런스를 따르되 **보라색 `#775DA0`을 포인트 컬러**로 사용한다. 다크 모드는 후순위(선택).

## 4. 사이트 구조와 라우팅

한국어가 기본 언어(접두어 없음), 영어는 `/en/` 접두어. Astro 내장 i18n 라우팅을 사용한다.

| 페이지 | 한국어 경로 | 영어 경로 |
|---|---|---|
| 홈 | `/` | `/en/` |
| 전시 목록 | `/exhibitions/` | `/en/exhibitions/` |
| 전시 상세 | `/exhibitions/[slug]/` | `/en/exhibitions/[slug]/` |
| 작가 목록 | `/artists/` | `/en/artists/` |
| 작가 상세 | `/artists/[slug]/` | `/en/artists/[slug]/` |
| 작품 상세 | `/works/[slug]/` | `/en/works/[slug]/` |
| 저널 목록 | `/journal/` | `/en/journal/` |
| 저널 글 | `/journal/[slug]/` | `/en/journal/[slug]/` |
| About | `/about/` | `/en/about/` |
| 콘텐츠 목록 | `/content/` | `/en/content/` |
| 콘텐츠 상세 | `/content/[slug]/` | `/en/content/[slug]/` |
| 구매 문의 | `/inquiry/` | `/en/inquiry/` |
| 404 | `/404` | `/en/404` |

- **'오시는 길' 메뉴·페이지는 두지 않는다.** 주소·운영시간·연락처·지도 링크는 **푸터와 About 페이지**에 표기하고, 로컬 SEO용 구조화 데이터(`ArtGallery`)는 그대로 유지한다.
- 상단 메뉴 순서(제안): 전시 · 작가 · 작품 · 저널 · **콘텐츠** · About · 구매 문의(버튼) · KO/EN 전환
- 모든 페이지에 `hreflang`(ko, en, x-default) 상호 참조를 넣는다.
- 슬러그는 한국어·영어 공통으로 영문 소문자 kebab-case를 사용한다(예: `/artists/kim-minjae/`).
- 전시는 종료 후에도 URL과 페이지를 **영구 유지**한다(아카이브). 상태(진행 중/예정/종료)는 날짜로 자동 계산한다.

### 4-1. '콘텐츠' 메뉴 (유튜브 영상 · 인스타그램 게시물)

갤러리가 운영하는 **유튜브 영상**과 **인스타그램 게시물**을 사이트 안에서 볼 수 있는 메뉴. 개발자가 관리하므로 별도 API 연동 없이 **콘텐츠 파일에 URL을 등록**하는 방식으로 한다.

- **목록 `/content/`**: 카드 그리드. `전체 / 영상 / 인스타그램` 필터 탭(클라이언트 JS 없이 쿼리스트링 또는 별도 정적 경로로 구현해도 됨), 최신순 정렬
- **상세 `/content/[slug]/`**: 영상 또는 게시물 + **직접 작성한 텍스트 요약·설명**(필수) + 관련 작가·전시 링크 + 원본 링크(유튜브/인스타그램으로 이동)
- **유튜브**: `youtube-nocookie.com` 임베드를 **클릭 시 로드(facade)** 방식으로 사용. 처음에는 로컬에 저장한 썸네일과 재생 버튼만 보여 주고, 클릭하면 iframe을 불러온다(성능·개인정보 보호, Lighthouse 점수 유지). 영상 길이·게시일은 콘텐츠 파일에 기록한다.
- **인스타그램**: 공식 임베드 스크립트는 무겁고 외부 추적이 있으므로 **카드(자체 저장 이미지 + 캡션 일부 + "인스타그램에서 보기" 링크)** 를 기본으로 한다. 필요하면 클릭 시에만 공식 임베드를 불러오는 옵션을 둔다. 로그인 필요·삭제된 게시물 때문에 **본문 이미지와 캡션은 사이트 안에 직접 저장**해 둔다(링크가 깨져도 페이지가 유지됨).
- **SEO·GEO·AEO**: 외부 임베드만 있는 페이지는 검색·AI가 읽을 텍스트가 없다. 그래서 상세 페이지마다 제목, 100~300자 요약, (영상은) 핵심 내용 목차 또는 스크립트 발췌를 **HTML 텍스트로 반드시 포함**한다. 영상은 `VideoObject` JSON-LD, 인스타그램 카드는 `SocialMediaPosting` 또는 `CreativeWork` JSON-LD를 출력한다.
- 갤러리의 유튜브 채널·인스타그램 계정 URL은 `site.ts`의 `sameAs`에 넣어 Organization 스키마와 연결한다. 채널·계정 URL은 ❓ 미정(14절).

## 5. 콘텐츠 모델 (Astro Content Collections, zod 스키마)

콘텐츠는 `src/content/` 아래에 두고, 언어별 필드는 `ko`/`en` 객체 또는 언어별 파일로 분리한다. 스키마는 빌드 시 검증한다.

- **exhibitions**: `slug`, `title{ko,en}`, `startDate`, `endDate`, `artists[]`(artist slug 참조), `works[]`, `summary{ko,en}`, `description`(본문 MDX), `coverImage`, `gallery[]`, `openingReception?`, `admission`
- **artists**: `slug`, `name{ko,en}`, `birthYear`, `nationality`, `bio{ko,en}`, `cv`(학력·개인전·단체전), `portrait`, `website?`, `sameAs[]`(외부 프로필 링크)
- **works**: `slug`, `title{ko,en}`, `artist`(참조), `year`, `medium{ko,en}`, `dimensions`, `edition?`, `image`, `exhibition?`(참조), `availability`(`available`|`sold`|`nfs`), `description?`
- **journal**: `slug`, `title{ko,en}`, `date`, `updated?`, `author`, `category`(소식/인사이트/인터뷰 등), `summary{ko,en}`(AEO용 40~60자 직답 요약), `faq[]?`(질문·답변), `coverImage`, 본문 MDX
- **contents** (콘텐츠 메뉴): `slug`, `type`(`youtube`|`instagram`), `title{ko,en}`, `date`, `url`(원본 링크), `youtubeId?`(유튜브일 때), `duration?`, `thumbnail`(로컬 이미지 필수), `images[]?`(인스타그램 게시물 이미지), `caption{ko,en}?`, `summary{ko,en}`(필수, 100~300자), `transcriptExcerpt?`, `relatedArtists[]?`, `relatedExhibitions[]?`, `tags[]?`
- **site 설정** (`src/config/site.ts`): 갤러리 이름(ko/en), 주소, 전화, 이메일, 운영시간, 좌표, SNS 링크(유튜브 채널·인스타그램 계정 포함), 설립 연도, 소개 문구

이미지는 `astro:assets`로 처리해 자동으로 WebP/AVIF, 반응형 `srcset`, 지연 로딩을 적용한다. 모든 이미지에 **의미 있는 alt 텍스트**(작가명·작품명·재료)를 필수 필드로 둔다.

## 6. SEO 요구사항

- 페이지별 고유 `<title>`, `meta description`, `canonical`, Open Graph, Twitter Card (공통 `SEO.astro` 컴포넌트)
- `@astrojs/sitemap`으로 `sitemap-index.xml` 생성(i18n·hreflang 포함), 이미지 sitemap은 작품·전시 이미지 포함 여부를 검토
- `robots.txt`: 전체 허용 + sitemap 위치 명시
- 시맨틱 HTML(`header/nav/main/article/section/footer`), 제목 계층(`h1`은 페이지당 1개), 브레드크럼(+ `BreadcrumbList` 스키마)
- RSS 피드: `/journal/rss.xml`, `/en/journal/rss.xml`
- 성능 목표: Lighthouse Performance·SEO·Accessibility·Best Practices **모두 95+**, LCP < 2.5s, CLS < 0.1. 불필요한 클라이언트 JS는 사용하지 않는다(Astro 기본 0 JS 유지).
- 모바일 우선 반응형, 접근성(키보드 탐색, 포커스 표시, 색 대비) 준수
- 한국 검색 대응: 네이버 서치어드바이저·구글 서치콘솔 소유권 인증용 메타 태그 자리(값은 환경변수 `PUBLIC_NAVER_SITE_VERIFICATION`, `PUBLIC_GOOGLE_SITE_VERIFICATION`)

## 7. GEO 요구사항 (생성형 AI 엔진 최적화)

- **JSON-LD 구조화 데이터**를 페이지 유형별로 출력(공통 `JsonLd.astro`):
  - 사이트 전역: `Organization` + `ArtGallery`(`LocalBusiness`; 주소, 영업시간, 좌표, `sameAs` SNS)
  - 전시: `ExhibitionEvent`(또는 `Event`) + `Place`, `startDate`/`endDate`, `performer`/`about`로 작가 연결
  - 작가: `Person`(`sameAs` 포함)
  - 작품: `VisualArtwork`(`creator`, `artMedium`, `dateCreated`, `width/height`, `offers`는 가격 비공개이므로 생략 또는 `availability`만)
  - 저널: `Article`/`BlogPosting`(`author` Person, `datePublished`, `dateModified`, `inLanguage`)
  - About: `AboutPage`
  - 콘텐츠: 유튜브 영상은 `VideoObject`(`name`, `description`, `thumbnailUrl`, `uploadDate`, `duration`, `embedUrl`), 인스타그램 게시물은 `SocialMediaPosting`
- `/llms.txt` 및 `/llms-full.txt`: 갤러리 소개, 주요 섹션 링크, 작가·전시 목록을 마크다운으로 정리(빌드 시 콘텐츠에서 자동 생성)
- `robots.txt`에서 AI 크롤러(GPTBot, ChatGPT-User, OAI-SearchBot, ClaudeBot, anthropic-ai, PerplexityBot, Google-Extended, Applebot-Extended 등) **허용**. 허용 여부 정책은 `site.ts`의 설정으로 한 곳에서 바꿀 수 있게 한다.
- **엔티티 일관성**: 갤러리 이름·주소·전화·소개 문구를 모든 곳에서 동일하게 사용하고, 작가·전시·작품 간 **내부 링크**를 양방향으로 촘촘히 연결한다(작가 페이지 ↔ 전시 ↔ 작품).
- 모든 글에 작성자, 게시일, 수정일, 근거·출처를 명시한다(E-E-A-T).

## 8. AEO 요구사항 (답변 엔진 최적화)

- 저널 글: 제목은 가급적 질문형 또는 명확한 주제문, **본문 첫 문단에 40~60자 내외 직답 요약**, 요약 박스(`summary` 필드)
- FAQ 섹션 컴포넌트(`Faq.astro`) + `FAQPage` JSON-LD. 대상: About·구매 문의 페이지(관람료, 운영시간, 주차, 작품 구매 방법, 대관 문의 등), 전시 상세(관람 정보), 저널 글
- 표·목록 형태의 요약 정보(전시 개요: 기간, 장소, 작가, 관람료)를 `dl`/`table` 시맨틱 마크업으로 제공
- 운영시간·위치 같은 로컬 질문에 바로 답할 수 있는 구조화된 정보를 푸터와 About에 노출
- 용어 설명(예: 에디션, 판화 기법)이 필요한 글에는 정의형 문장을 포함

## 9. 작품 구매 문의 기능 (결제 없음)

- 작품 상세와 전시 상세에 **"구매 문의"** 버튼 → 문의 폼 페이지 또는 `mailto:` 링크(작품명·작가명 자동 입력)
- ❓ 폼 전송 방식 미정. 추천: 정적 사이트이므로 외부 폼 서비스(Formspree, Web3Forms 등) 또는 이메일 `mailto:` 우선. 스팸 방지(honeypot) 포함. 개인정보 수집 안내 문구 포함.
- 가격은 공개하지 않고 "문의" 표기가 기본. 작품별로 `availability` 상태(판매 가능/판매 완료/비매품)를 표시한다.

## 10. 다국어 처리

- Astro 내장 i18n 라우팅(`defaultLocale: 'ko'`, `locales: ['ko','en']`, `prefixDefaultLocale: false`)
- UI 문자열은 `src/i18n/ko.json`, `en.json`으로 분리, 번역 헬퍼 `t()` 제공
- 언어 전환 버튼은 같은 콘텐츠의 다른 언어 페이지로 이동(없으면 해당 언어 홈)
- 번역이 없는 콘텐츠는 한국어 원문으로 대체하되 `hreflang`을 출력하지 않는다. 영어 번역 초안은 작성 후 담당자 검수를 거친다.
- `<html lang>`, `og:locale`, `inLanguage` 값 정확히 설정

## 11. 기술 스택과 폴더 구조 (제안)

- Astro 최신 안정 버전, TypeScript(strict), 순수 CSS(CSS 변수) 또는 Tailwind(둘 중 가볍게 한쪽 선택, 기본은 순수 CSS 권장)
- 통합: `@astrojs/sitemap`, `@astrojs/mdx`, `@astrojs/rss`
- Node 22, 패키지 매니저 npm
- 배포 대상: ❓ 미정. 정적 호스팅(Cloudflare Pages, Vercel, Netlify 등)이면 모두 가능. 도메인 `largo.co.kr` 계열 사용 여부 확인 필요. `astro.config.mjs`의 `site` 값은 환경변수로 받는다.

```
largo/
├─ docs/                     # 이 브리프, 로고 원본
├─ public/                   # favicon, logo.svg, robots.txt, og 기본 이미지
├─ src/
│  ├─ components/            # SEO.astro, JsonLd.astro, Faq.astro, Header, Footer ...
│  ├─ config/site.ts         # 갤러리 정보 단일 출처
│  ├─ content/               # exhibitions, artists, works, journal
│  ├─ i18n/                  # ko.json, en.json, utils
│  ├─ layouts/
│  ├─ pages/                 # ko 기본 + en/ 하위
│  └─ styles/
└─ astro.config.mjs
```

## 12. 개발 단계와 완료 기준

1. **기반**: Astro 프로젝트 생성, i18n·sitemap·MDX 설정, 디자인 토큰(브랜드 컬러), 공통 레이아웃, 로고 벡터화·파비콘·OG 이미지
2. **SEO/GEO/AEO 공통 모듈**: `SEO.astro`, `JsonLd.astro`, `Faq.astro`, `robots.txt`, `llms.txt` 자동 생성, RSS
3. **콘텐츠 컬렉션과 페이지**: 스키마 정의 → 샘플 데이터(작가 3, 전시 3, 작품 6, 저널 3, 콘텐츠 4(유튜브 2 + 인스타그램 2) / 한·영) → 각 페이지 구현
4. **콘텐츠 메뉴, 구매 문의, About**: 콘텐츠 목록·상세(유튜브 facade, 인스타그램 카드), 구매 문의 기능, 푸터의 주소·운영시간, FAQ
5. **검증**: 아래 완료 기준 확인

**완료 기준 (Definition of Done)**
- `npm run build` 성공, `astro check` 오류 없음
- Lighthouse(모바일) 4개 항목 95+
- 모든 JSON-LD가 schema.org 검증기/구글 리치 결과 테스트 형식에 맞음(빌드 후 스크립트로 JSON 파싱 검증 포함)
- `sitemap`, `robots.txt`, `llms.txt`, RSS, `hreflang` 출력 확인
- 모든 이미지에 alt, 모든 페이지에 단일 `h1`, 깨진 내부 링크 없음
- 한국어·영어 두 언어 모두 전 페이지 동작
- 콘텐츠 메뉴: 유튜브 iframe은 클릭 전에는 로드되지 않고(네트워크 요청 없음), 모든 상세 페이지에 텍스트 요약과 JSON-LD가 있음
- 디자인 레퍼런스의 전환·포커스 모드 연출이 적용되되 JS를 꺼도 모든 정보가 읽히고, `prefers-reduced-motion`에서 모션이 줄어듦
- `README.md`에 콘텐츠 추가 방법(작가·전시·작품·글 추가 절차), 실행·배포 방법 기재

## 13. 사람이 직접 해야 하는 일 (개발 범위 밖)

- 도메인 연결, 호스팅 계정 생성과 배포
- 구글 서치콘솔, 네이버 서치어드바이저 등록과 소유권 인증, sitemap 제출
- 구글 비즈니스 프로필, 네이버 플레이스 등록(사이트의 이름·주소·전화와 **동일하게** 입력)
- 실제 갤러리 정보, 작가·작품 이미지와 저작권 확인, 영어 번역 검수
- 개인정보처리방침 문구 확정

## 14. 개발 시작 전 확인이 필요한 미정 사항 (❓ 요약)

1. 주소, 전화, 이메일, 운영시간, 관람료
2. 배포 호스팅과 도메인
3. 구매 문의 폼 방식(`mailto:` vs 외부 폼 서비스)
4. 갤러리의 유튜브 채널·인스타그램 계정 URL (미정이면 샘플 콘텐츠로 개발)
5. 실제 콘텐츠 제공 시점(그 전까지는 샘플 데이터로 개발)

## 15. 다른 세션에 요청할 때 쓸 프롬프트 예시

```
docs/PROJECT_BRIEF.md 를 읽고 12절의 1~3단계를 구현해 줘.
미정(❓) 항목은 문서의 제안값으로 진행하고, 갤러리 정보는 샘플 데이터로 채워 줘.
각 단계가 끝날 때마다 완료 기준을 직접 검증하고 결과를 보고해 줘.
```
