# 마이크로 프론트엔드
## 마이크로서비스 아키텍처
마이크로서비스는 하나의 큰 애플리케이션을 **작고 독립적인 서비스 단위로 분리**한 아키텍처입니다.
각 서비스는 **독립적으로 배포/확장 가능** 하고, 일반적으로 REST API나 메시지큐로 통신합니다.
- 예: 사용자 서비스, 결제 서비스, 주문 서비스 등으로 나눔
- 특징: 독립성, 유연한 확장, 빠른 배포, 팀 단위 책임
## 마이크로 프론트엔드란?
마이크로 프론트엔드는 하나의 큰 프론트엔드 애플리케이션을 **기능 단위로 나눠서 여러팀이 독립적으로 개발하고 배포**할 수 있게 만든 아키텍처입니다. 마이크로서비스의 **프론트엔드 버전**이라고 보면 됩니다.
- 예: 헤더는 팀 A, 상품 목록은 팀 B, 결제 화면은 팀 C가 개발
- 구현 방식: iframe, 웹 컴포넌트, Module Federation (WebPack 5), SPA 통합 등
## 마이크로 서비스와 마이크로 프론트엔드의 관계
| 항목      | 마이크로서비스                        | 마이크로 프론트엔드                    |
| ------- | ------------------------------ | ----------------------------- |
| 적용 계층   | 백엔드                            | 프론트엔드                         |
| 공통 철학   | 분리된 책임, 독립 배포, 팀 자율성           |                               |
| 팀 구조    | 도메인 중심 (ex: 주문 팀, 사용자 팀)       | 각 팀이 프론트+백엔드를 함께 담당하는 경우 많음   |
| 서로의 필요성 | 마이크로서비스 구조에서는 자연스럽게 프론트도 쪼개야 함 | 백엔드가 마이크로서비스라면, 프론트도 마이크로화 필요 |
마이크로서비스를 도입하면 마이크로 프론트엔드도 필요해질 가능성이 높습니다. 백엔드가 기능별로 나뉘었는데 프론트가 하나로 뭉쳐 있다면, 개발 속도가 병목될 수 있기 때문이다.

💡 요약
- 마이크로 프론트엔드는 마이크로서비스 철학을 프론트에 적용한 것
- 마이크로서비스 아키텍처를 도입하면, 프론트도 마이크로 프론트엔드로 자연스럽게 나뉘는 경향이 있음
- 두 개념은 협력적이고 상호 보완적인 구조를 이룸

# 🔥 SPA의 장점과 한계
## ✅ SPA 장점
- 빠른 사용자 경험: 초기 로딩 후에는 페이지 전환이 빠름
- 클라이언트 렌더링 중심: 서버 부하 분산
- 모던한 개발 방식: React, Vue, Angular 등과 잘 어울림

## ❌ SPA의 한계
| 한계             | 설명                                         |
| -------------- | ------------------------------------------ |
| **번들 크기 증가**   | 앱이 커질수록 하나의 번들에 모든 코드가 들어가 로딩 시간 증가        |
| **배포 병목**      | 전체 앱을 하나로 묶어 빌드하므로, 작은 수정에도 전체 재배포가 필요     |
| **팀 협업의 충돌**   | 여러 팀이 하나의 코드베이스에 작업 → 코드 충돌, 테스트 충돌, 일정 지연 |
| **기능별 독립성 부족** | A팀이 수정한 코드가 B팀 기능에 영향을 줄 수 있음              |
| **버전 호환성 문제**  | 전역에서 React, Router 등 버전 맞추기 어려움            |
> 👉 결과적으로, 앱이 커질수록 SPA는 조직 규모와 서비스 복잡도를 감당하기 어려워집니다.

## 🏗️ 모놀리식 프론트엔드 vs 마이크로 프론트엔드 비교
| 항목          | 모놀리식 프론트엔드     | 마이크로 프론트엔드               |
| ----------- | -------------- | ------------------------ |
| **구성**      | 하나의 코드베이스      | 여러 개의 독립 앱               |
| **팀 협업**    | 하나의 레포지토리에서 협업 | 각 팀이 독립적으로 개발            |
| **배포**      | 전체 빌드/배포       | 각 앱 개별 배포 가능             |
| **기술 스택**   | 공통된 기술로 통일     | 팀별로 React, Vue 등 선택 가능   |
| **디자인 시스템** | 전역 통일          | 통일 어렵지만 웹 컴포넌트 등으로 해결 가능 |
| **장점**      | 통일된 설계, 관리 편함  | 확장성과 팀 자율성 확보            |
| **단점**      | 병목, 충돌, 속도 저하  | 초기 설정 복잡, UX 통일 어려움      |

### 👥 조직 구조 관점에서의 문제
**모놀리식 프론트엔드에서는:**
- 모든 팀이 하나의 코드베이스를 공유
- PR, 코드 리뷰, 배포 등에서 병목 발생
- 한 팀의 일정 지연이 전체 프로젝트 일정에 영향
**마이크로 프론트엔드에서는:**
- 팀 단위로 프론트+백엔드+DB까지 책임지는 조직 (cross-functional team) 구성
- 각 팀이 자신의 도메인 앱을 독립 개발 및 배포
- Spotify, Amazon, Zalando 등에서 채택한 구조

### 🎯 요약
- SPA는 초기엔 좋지만, 규모가 커질수록 병목, 충돌, 배포 문제 발생
- 모놀리식 프론트엔드는 통일성은 있지만, 협업과 확장성에서 한계
- 마이크로 프론트엔드는 SPA 구조의 확장판이자 대안으로, 조직 구조와 기술 구조의 분리를 가능하게 함
- Module Federation 같은 기술을 통해 런타임에서 앱을 조합하는 방식으로 SPA 한계를 해결 가능

# 마이크로 프론트엔드 설계 전략
## Build-time vs Run-time
마이크로 프론트엔드는 어떻게 통합되는가에 따라 크게 두 가지 방식으로 나눌 수 있습니다

### 🏗️ Build-time 통합 (Static Integration)
- 통합 시점: 빌드 단계
- 모든 마이크로 프론트엔드 앱이 하나의 빌드 결과물로 묶임
- 사용 예: Module Federation (Webpack) - Static Remote
- 장점:
  - 퍼포먼스가 좋음 (로딩 최적화 가능)
  - 복잡성 낮음
- 단점:
  - 배포가 느림 (하나의 변경이 전체에 영향)
  - 독립적인 배포 불가능

### 🚀 Run-time 통합
런타임에서 각각의 앱을 불러와 통합하는 방식이며, 세 가지로 세분화할 수 있습니다:

1) Client-side integration
- 브라우저에서 JS로 동적으로 앱들을 로딩
- 사용 예: Module Federation (Dynamic Remote), single-spa, import-maps
- 장점:
  - 배포 독립성 보장
  - 유연한 통합
- 단점:
  - 초기 로딩 시간 증가
  - 복잡한 상태 공유 및 이벤트 관리
2) Server-side composition
- 서버에서 각 마이크로 프론트엔드의 HTML 조각(fragment)을 모아 렌더링
- 사용 예: Edge Side Includes(ESI), SSR 조합, Backend for Frontend(BFF)
- 장점:
  - 초기 렌더링 속도 향상 (SSR)
  - SEO에 유리
- 단점:
  - 서버 부담 증가
  - 클라이언트 간 상태 공유가 어려움
3) Edge-side 또는 CDN integration
- CDN이나 엣지 서버에서 조합
- 장점:
  - 빠른 응답 속도
- 단점:
  - 구현 난이도 높음

## Self-contained Systems (SCS)
- 시스템을 완전히 독립된 단위로 나누는 설계 전략
- 각 시스템은 프론트엔드, 백엔드, 데이터베이스를 모두 포함하며 완전 자립적
- 예: 쇼핑몰의 "상품", "결제", "장바구니" 등
- 장점:
  - 팀 단위 소유권 확실
  - 배포·스케일링 독립적
- 단점:
  - 중복 코드 발생 가능
  - UX 일관성 유지 어려움

## Vertical Slice Design (도메인 기반 분할)
- 기능/도메인 단위로 프론트엔드를 분할
- 예: '상품 조회', '결제', '리뷰' 기능을 각각 별도의 앱으로 구성
- 장점:
  - 기능 중심의 팀 조직 가능 (도메인 주도 설계)
  - 복잡성 분산
- 단점:
  - 공통 UI나 상태 공유 어려움
  - UX 일관성 확보 필요

## 마이크로 프론트엔드 모듈 분할 기준
다양한 기준으로 모듈을 나눌 수 있으며, 일반적으로 다음 기준들을 복합적으로 고려합니다:

### 📦 1) 도메인 기준 (Domain-driven)
- 비즈니스 기능 단위로 나눔
- 예: 쇼핑몰의 '회원 관리', '상품 관리', '주문 처리'

### 👥 2) 팀 기준 (Team-aligned)
- 각 팀이 하나의 모듈을 소유
- 팀이 독립적으로 개발·배포 가능
- 팀 책임과 소유권 명확히 분리

### 🛠️ 3) 기술 스택 기준
- 각 모듈이 서로 다른 프레임워크/라이브러리를 사용할 수 있음
- 예: 일부는 React, 일부는 Vue로 개발
- 장점: 팀의 자율성 ↑
- 단점: UX 일관성 유지 및 번들 최적화 어려움


## ✅ 설계 전략 요약
| 요소    | 내용                                           |
| ----- | -------------------------------------------- |
| 통합 방식 | Build-time / Client-side / Server-side / CDN |
| 구조 패턴 | Self-contained Systems / Vertical Slices     |
| 분할 기준 | 도메인, 팀, 기술 스택                                |
| 주의점   | UX 일관성, 공통 컴포넌트 관리, 상태 공유 전략 필요              |


## 🔧 기술 예시
| 기술                    | 설명                             |
| --------------------- | ------------------------------ |
| **Module Federation** | Webpack 5 이상에서 런타임 동적 모듈 로딩 가능 |
| **single-spa**        | 다양한 프레임워크 조합 지원                |
| **import-maps**       | 브라우저에서 모듈 간 맵핑 처리              |
| **Nx / Turborepo**    | 모노레포 기반에서 마이크로 프론트엔드 분리 관리     |
| **SSR 도구**            | Next.js, Remix 등과 서버사이드 통합 가능  |

# 🔧 구현 방식 및 주요 기술 스택
## 📦 Module Federation (Webpack 5+) 기반 동적 로딩
### 개요
- Webpack 5의 Module Federation Plugin을 활용하여 런타임에 원격 모듈 로딩
- 각각의 앱이 Remote Entry를 통해 모듈을 노출하고 소비
### 장점
- 런타임 통합 가능 (개별 배포)
- 번들 크기 최적화
- 앱 간 코드 공유 가능 (공통 라이브러리 등)
### 단점
- Webpack 의존성, 설정 복잡도 ↑
- 프레임워크별 세부 설정 필요

### 예시
```js
// Host app webpack.config.js
remotes: {
  cartApp: "cartApp@https://cdn.com/cart/remoteEntry.js"
}
```
## ⚙️ 프레임워크 비교
| 프레임워크                 | 설명                             | 장점                                   | 단점                 |
| --------------------- | ------------------------------ | ------------------------------------ | ------------------ |
| **Single-SPA**        | SPA 애플리케이션을 모듈 단위로 통합          | 다양한 프레임워크 혼합 가능, 커뮤니티 활발             | 러닝 커브 존재, 초기 설정 복잡 |
| **qiankun**           | Single-SPA 기반의 중국 Ant Group 개발 | 자동 자원 격리 (CSS, JS), iframe 없이 독립성 유지 | 내부 구조가 블랙박스에 가까움   |
| **Module Federation** | Webpack 5 기능, 런타임 모듈 공유        | 동적 로딩 및 공유 최적화                       | Webpack 종속성 강함     |
| **PirateShip**        | Spotify가 만든 MFE 플랫폼, React 기반  | 상태 공유/스타일/라우팅 등 프레임워크 제공             | 공개 정보 부족, 내부 사용 위주 |


### ✅ 선택 팁:
- 단순 조합 중심이면 Module Federation
- 다양한 프레임워크 통합이면 Single-SPA 또는 qiankun
- 엔터프라이즈급 MFE 플랫폼 필요 시 PirateShip

## 🌐 CDN을 이용한 Remote Entry 배포 전략
### 개요
- 각 마이크로 프론트엔드 앱의 번들을 빌드 후 CDN에 업로드
- Host 앱에서 remoteEntry.js를 런타임에 동적으로 로딩
### 장점
- 빠른 전파, 글로벌 캐싱
- 마이크로 앱의 독립 배포 가능
- 배포 파이프라인 단순화 가능 (CI/CD와 결합)
### 설계 전략
- 각 앱은 remoteEntry.js를 생성
- CDN에 업로드 (예: AWS S3 + CloudFront, Netlify, Vercel 등)
- Host 앱에서는 remoteEntry URL을 런타임에 지정
### 예시
```js
// Dynamic remote loading
System.import('https://cdn.myapp.com/checkout/remoteEntry.js')
  .then((checkout) => checkout.render())
```

### 보안 주의사항
- 신뢰되지 않은 remoteEntry 차단 (CSP, 서명 등)
- remoteEntry의 버전 관리를 명시적으로 하거나 해시 적용

# 🧠 상태 관리 및 데이터 공유 전략
## 글로벌 스토어 사용 여부
- Redux / Zustand / Recoil (React), Pinia / Vuex (Vue) 등
- 모듈 간 공유 상태를 위해 Global Store를 중앙 집중식으로 사용하는 방식
- 하지만 앱 간 coupling 증가 위험 → 가급적 로컬 상태 우선, 글로벌은 최소화

## Pub/Sub, CustomEvent 방식
- 앱 간 느슨한 연결을 위해 이벤트 기반 통신
- 예: `window.dispatchEvent(new CustomEvent('user:login', {...}))`
- 장점: 프레임워크 독립, 낮은 결합도
- 단점: 디버깅 어려움, 순서 보장 안 됨

## Cross-app state sharing
- 전역 상태: 인증 정보, 테마, 다크모드
- 국소 상태: 폼 입력값, 모달 상태 등 → 앱 내부에서만 유지
- 전략: "Lift state only when truly shared"

## 프레임워크 혼합 환경 (React, Vue, Angular)
- 글로벌 상태를 JS 객체 또는 메시지 기반으로 통신 (`postMessage`, EventBus)
- 상태 동기화를 위한 중간 브릿지 계층 필요
- 예: `zustand` 같은 JS 기반 상태 저장소를 공통 모듈로 제공

# 🚦 라우팅 전략 및 URL 구조
## 통합 라우터 vs 개별 앱 라우터
- 통합 라우터: Shell/Host 앱이 전체 URL 관리 (추천)
- 개별 라우터: 각 앱이 자신만의 라우터 관리
- 권장: 통합 Shell이 경로 결정, 하위 앱에 props로 전달

## Nested Routing 구성
- `/shop/product/123` → `shop` 앱이 `product` 세부 경로 처리
- MFE 라우팅 설계 시 `basePath` 설정 중요

## History 모드와 URL Sync
- history.pushState() 기반의 SPA 라우팅 사용
- single-spa는 각 앱에 history 전달
- hash 모드는 SEO에 불리하므로 지양

## SEO & SSR 고려사항 (Next.js 기반 구성)
- SSR 구성은 Next.js 기반 shell에서 하위 MFE를 통합하거나 Proxy 처리
- dynamic import + SSR 고려 → next/dynamic + SSR false 옵션 활용
- getServerSideProps() 내에서 micro-app SSR 결과 포함 가능

# 🎨 스타일 격리 및 충돌 방지
## CSS 스코핑
- CSS Modules, BEM, Shadow DOM 사용하여 범위 제한
- Web Components + Shadow DOM은 격리 수준 가장 강력

## 스타일 충돌 해결
- 글로벌 CSS class name 충돌 방지
- `scoped`, `:local`, `prefix` 기반 명명 규칙 적용

## 전역 스타일 주입 방지
- 스타일드 컴포넌트(styled-components, Emotion) 사용 시:
- createGlobalStyle 최소 사용
- ThemeProvider 범위 격리

## 디자인 시스템 통일 전략
- 공통 디자인 시스템은 별도 패키지화 (@org/ui-library)
- 버전 고정 + 의존성 관리 필수
- Figma + Storybook 기반으로 디자인-개발 협업 구조 권장

# ⚙️ 빌드 및 배포 전략
## 개별 빌드 vs 통합 빌드
- 개별 빌드: MFE의 기본 전략
- 통합 빌드는 초기 단계에서 임시로 활용할 수 있음 (예: 개발용)
## CI/CD 설계
- 팀 단위로 개별 앱을 Git repo + CI 파이프라인으로 배포
- Host 앱이 remote URL만 업데이트
## CDN 기반 버전 관리 & 캐시
- remoteEntry.js에 버전 포함 (/app1/v1.2.3/remoteEntry.js)
- CDN 캐싱 설정 (immutable + version path)

## Dynamic & Lazy Loading
- React.lazy, System.import, dynamic import() 활용
- 초기 로딩 최소화 → 필요 시 로드

# 🚀 성능 이슈 및 최적화 전략
## 초기 렌더링 성능 저하
- 코드 분할, Lazy loading
- shell 앱에서 필요한 앱만 동적 로딩
## 공통 라이브러리 중복 방지
- Module Federation의 shared 옵션 설정:
```js
shared: {
  react: { singleton: true, eager: true, requiredVersion: '^18.0.0' }
}
```
## bundle splitting / prefetch
- webpack splitChunks, React.lazy, import(/* webpackPrefetch */)
- loading="lazy" for images, rel="preload" for fonts

# 🔐 보안 이슈 및 해결 방안
## cross-origin 대응
- CORS, postMessage, iframe sandbox
- SameSite Cookie 정책 고려
## 토큰 / 인증 헤더 공유
- OAuth2 + PKCE, JWT via cookie + HttpOnly
- 앱 간 인증 컨텍스트 공유 시: 브라우저 저장소보다 서버 중심 전략 권장
## XSS / CSRF 방지
- CSP 설정, HTML escaping
- 상태 변경 요청은 CSRF Token 또는 SameSite=Strict 쿠키 사용
## SSO 구현
- OpenID Connect 기반의 단일 로그인 적용
- MFE 간 리디렉션 관리 필요

# 🧪 테스트 및 모니터링 전략
## 마이크로앱 단위 테스트
- 각 앱에 자체 Unit + Integration 테스트
- Jest, Vitest, React Testing Library, Cypress 등

## 통합 테스트
- E2E: Playwright / Cypress
- 시나리오 기반 테스트 (페이지 전환, 인증 흐름 등)

## 모니터링
- Sentry, Datadog, Prometheus 등
- 앱별로 error boundary 구성 + 공통 로깅 API 사용

## Health Check
- 각 앱의 /health, /status API 응답
- 배포 후 CDN / Nginx / API Gateway에서 상태 확인
