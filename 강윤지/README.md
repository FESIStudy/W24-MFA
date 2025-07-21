### 1. 개념 및 등장 배경

마이크로 프론트엔드는 **마이크로서비스 아키텍처(MSA)**의 프론트엔드 버전이라 할 수 있다. 백엔드에서 MSA가 팀 단위의 독립 개발과 배포를 가능하게 했듯, 마이크로 프론트는 **대규모 프론트엔드 애플리케이션을 독립적인 모듈(앱)로 분할**해 각각 개별적으로 개발하고 배포할 수 있도록 한다.

SPA(Single Page Application)는 성능, 유지보수, 배포, 협업 면에서 한계가 있다. 특히 대규모 조직에서는 한 코드베이스에서 많은 팀이 충돌하는 일이 빈번하고, 도메인 분리가 어렵다. 이런 문제를 해결하기 위한 조직 구조적 요구와 함께 마이크로 프론트엔드가 등장하게 되었다.

- **모놀리식 프론트엔드**: 한 저장소, 한 빌드, 한 배포 → 통합은 쉽지만 유지보수/스케일링 어려움
- **마이크로 프론트엔드**: 도메인 기반 분할, 독립된 빌드와 배포, 팀 자율성 보장

---

### 2. 아키텍처 패턴 분류 및 설계 전략

### 📦 Build-time 통합 vs ⏱ Run-time 통합

- **Static integration (Build-time)**
    - 공통 저장소에서 모든 마이크로앱을 통합 빌드
    - 단점: 각 앱이 강하게 결합되어 있음
- **Client-side integration (Run-time)**
    - 각 앱은 독립적으로 빌드/배포되고, Shell 앱에서 동적 로딩
    - Module Federation, Single-SPA 방식
- **Server-side composition**
    - 서버가 HTML 조각(fragment)을 조합하여 렌더링
    - SSR 환경에 적합 (e.g., Next.js 기반 구성)

### 설계 전략

- **Self-contained Systems (SCS)**: 각 앱이 프론트 + 백엔드를 포함한 완전한 시스템
- **Vertical Slice Design**: 기능 단위가 아닌 도메인(예: 결제, 장바구니) 기준으로 분리
- **분할 기준**
    - **도메인**: 사용자/상품/결제 등
    - **팀**: 팀 간 충돌 최소화
    - **기술 스택**: React, Vue 혼합 등도 가능

---

### 3. 구현 방식 및 주요 기술 스택

- **Web Components**: 프레임워크 독립적인 방식. Shadow DOM으로 스타일 격리도 가능
- **iframe**: 완전한 격리 제공. 보안과 스타일 충돌 방지. 하지만 UX/성능 이슈 존재
- **Module Federation (Webpack 5+)**: 런타임에 외부 앱 로딩, shared 모듈 공유 가능
- **프레임워크 비교**
    - **Single-SPA**: 루트 앱에서 다양한 프레임워크 통합
    - **qiankun**: Alibaba에서 만든 Single-SPA 기반
    - **PirateShip**: 실험적인 Webpack-based 런타임
- **CDN 활용**: Remote Entry 파일을 CDN에 배포하고 Shell 앱에서 import

---

### 4. 상태 관리 및 데이터 공유 전략

- **전역 상태 공유**: Redux, Zustand 등을 Shell 앱에서 관리
- **이벤트 기반 통신**: `CustomEvent`, Pub/Sub, RxJS
- **로컬 상태 유지**: 각 앱이 독립적으로 상태 관리
- **멀티 프레임워크 환경**
    - 공통 인터페이스 추상화
    - Web Component 내부에서 context API 흉내

---

### 5. 라우팅 전략과 URL 구조

- **통합 라우터**: Shell 앱이 모든 경로를 컨트롤
- **개별 앱 라우터**: 각 마이크로앱이 자신만의 라우팅 구조 가짐
- **Nested Routing**: Shell → Micro App 내부로 라우팅 위임
- **History 모드와 Sync**: `window.history` 동기화 필요
- **SEO/SSR 고려사항**
    - SSR 기반에서는 `Server-side Composition`이 필요
    - Next.js와 Module Federation 조합 가능

---

### 6. 스타일 격리 및 충돌 방지

- **CSS 스코핑**
    - BEM, CSS Modules, Shadow DOM(Web Components)
- **충돌 방지 전략**
    - 네임스페이스 활용
    - CSS-in-JS 사용 시 중복 방지
- **전역 스타일 주입 방지**
    - styled-components나 Emotion 사용 시 주의
- **디자인 시스템 공유**
    - 공용 라이브러리로 배포하거나 각 앱에서 로컬 사용

---

### 7. 빌드 및 배포 전략

- **개별 빌드**: 각 앱을 독립적으로 빌드 & 배포 (권장)
- **통합 빌드**: 변경된 앱만 새로 빌드 (monorepo 환경)
- **CI/CD 설계**
    - GitHub Actions, Turborepo, Nx 활용
    - Micro App → CDN 배포 → Shell 앱이 consume
- **버전 관리/캐시 전략**
    - remoteEntry.[hash].js
    - `cache-control` 정책 설정

---

### 8. 성능 이슈 및 최적화 전략

- 초기 렌더링 지연 → **Lazy loading**, **Skeleton UI** 필요
- **중복 로딩 방지**
    - Module Federation의 `shared` 설정으로 라이브러리 공유
- **코드 분할**
    - dynamic import, Webpack chunk 전략
- **prefetch/preload**로 사용자 행동 예측

---

### 9. 보안 이슈 및 해결 방안

- **CORS, iframe sandbox**로 cross-origin 보안 확보
- **토큰 공유 주의**
    - 앱마다 인증 처리 권장
    - 공용 스토리지(XSS 위험)
- **공통 취약점 대응**
    - XSS: Content Security Policy 설정
    - CSRF: SameSite 설정, 토큰 검증
- **SSO, OAuth2, JWT** 활용

---

### 10. 테스트 및 모니터링

- **마이크로앱 단위 테스트**
    - Unit, Integration, Component test
- **통합 테스트**
    - Cypress, Playwright 등으로 E2E 시나리오 구성
- **로깅/모니터링**
    - Sentry, Datadog, Grafana 등
- **헬스 체크**
    - 각 앱에 `/health` 엔드포인트 제공

---

### 11. 조직 협업 및 운영 전략

- **도메인 기반 팀 구성**
    - 각 팀이 앱을 소유하고 책임
- **디자인 시스템/컴포넌트 관리**
    - Storybook + npm 패키지
- **버전 관리**
    - Semver, Git Tag 활용
- **협업 도구**
    - Nx, Turborepo, Changeset, Bit.dev

---

### 12. 실전 사례 및 리스크 관리

- **실제 사례**
    - Zalando: 최초 도입 사례
    - Spotify: 각 팀 독립 배포 구조
    - DAZN, IKEA 등도 점진적 도입
- **문제점**
    - 공통 코드 중복, 퍼포먼스 저하
    - 디버깅/모니터링 복잡성
- **실패 사례**
    - 앱 간 결합도 높거나 팀 문화가 분리되지 않은 경우
- **Trade-off**
    - 유연성과 독립성 확보 ↔ 초기 도입 비용, 러닝 커브

---

### 13. 예상 면접 질문

- **마이크로 프론트엔드를 도입할 때 가장 큰 리스크는 무엇인가요?**

> 마이크로 프론트엔드의 가장 큰 리스크는 복잡성 증가입니다.
> 
> 
> 팀별로 독립된 앱을 운영하면서 생기는 **상태 공유의 어려움**, **라우팅 충돌**, **공통 라이브러리 중복 로딩** 문제가 대표적입니다.
> 
> 특히 협업 체계가 확립되지 않으면, 각 앱이 서로 다른 스타일 가이드, 디자인 시스템, 상태 관리 방식 등을 채택해 **일관성을 잃을 수 있습니다**.
> 
> 이를 해결하기 위해 저는 다음과 같은 전략을 사용합니다:
> 
> - 공통 디자인 시스템 및 컴포넌트 라이브러리 도입
> - Module Federation에서 `shared` 설정으로 라이브러리 중복 방지
> - 명확한 도메인 경계 설정과 CI/CD 분리

---

- **Module Federation의 장점과 한계는 무엇인가요?**

> 장점은 런타임에서 모듈을 로딩할 수 있기 때문에, 각 팀이 독립적으로 앱을 배포하고, Shell 앱이 이를 동적으로 가져올 수 있다는 점입니다.
> 
> 
> `shared` 설정을 통해 공통 라이브러리(React 등)를 중복 없이 로딩할 수 있어 **퍼포먼스 측면에서도 효율적**입니다.
> 
> 하지만 **한계**는 다음과 같습니다:
> 
> - 복잡한 설정: webpack 설정이 까다롭고 디버깅이 어렵습니다.
> - shared 버전 충돌: 각 앱이 서로 다른 React 버전을 참조하면 앱이 깨질 수 있습니다.
> - SSR 적용이 어렵고, Next.js 같은 프레임워크와 완벽히 통합하려면 추가 설정이 필요합니다.

---

- **여러 기술 스택(Vue/React)이 섞인 프로젝트에서 상태 관리는 어떻게 설계할 것인가요?**

> 기술 스택이 혼합된 경우, 전역 상태 공유보다는 이벤트 기반 통신이 적합합니다.
> 
> 
> 대표적인 방식은:
> 
> - **CustomEvent / EventBus**로 앱 간 통신
> - **Window 객체를 통한 메시징** (예: `window.dispatchEvent`)
> - 상태가 필요한 경우, 공통 상태 저장소(예: Redux)를 외부 패키지로 분리해 각 앱에서 의존하게 만들 수 있습니다.
> 
> 단, React의 Context나 Vuex처럼 프레임워크 종속적인 방식은 공유가 어렵기 때문에, 프레임워크에 독립적인 방식이 우선입니다.
> 

---

- **마이크로앱이 서로 다른 릴리즈 사이클을 가질 때 CI/CD 전략은?**

> 앱이 서로 다른 릴리즈 사이클을 갖는 경우, 개별 앱 단위의 빌드 및 배포 파이프라인을 구성해야 합니다.
> 
> 
> 이를 위해 각 앱은 독립적인 Git 저장소 또는 Monorepo 구조에서 **Turborepo / Nx** 같은 툴을 사용해 분리된 빌드 환경을 가집니다.
> 
> - 배포는 각 앱의 remoteEntry 파일을 **CDN**에 배포하고,
> - Shell 앱은 해당 앱의 버전을 동적으로 로딩합니다 (예: remoteEntry.[hash].js).
> 
> 이를 통해 전체 앱의 릴리즈 없이도, 일부 마이크로앱만 **버전 관리 및 롤백**이 가능하도록 합니다.
> 

---

- **퍼포먼스 관점에서 마이크로 프론트엔드와 SPA의 결정적인 차이는 무엇인가요?**

> SPA는 단일 번들에서 모든 코드를 로딩하는 구조이기 때문에 초기 렌더링 속도와 번들 크기가 문제됩니다.
> 
> 
> 반면 마이크로 프론트엔드는 **앱 단위로 분리된 번들을 Lazy Load**할 수 있어 **필요한 부분만 로딩 가능**합니다.
> 
> 그러나, 마이크로 프론트는 오히려 **초기 모듈 해석 비용이 증가**할 수 있고, **공통 라이브러리 중복 로딩** 위험도 존재합니다.
> 
> 이 때문에 Webpack의 `shared` 설정이나 prefetch 전략 등으로 최적화해야 하며, 실제로는 SPA보다 더 복잡한 성능 튜닝이 요구될 수 있습니다.
> 

---

- **iframe 기반 마이크로 프론트 구조에서의 보안 설계 전략은?**

> iframe을 사용하는 경우, 완벽한 격리가 가능하다는 장점이 있지만, 보안 설정이 중요합니다.
> 
> 
> 대표적인 전략은 다음과 같습니다:
> 
> - `sandbox` 속성 설정: `allow-scripts`, `allow-same-origin` 최소화
> - `Content-Security-Policy(CSP)` 설정으로 script, iframe-src 제한
> - CORS 정책을 통한 리소스 보호
> - OAuth2 또는 JWT를 사용할 경우, **토큰은 URL이 아닌 쿠키 기반 저장 + SameSite 설정**
> 
> 특히 민감한 데이터나 인증 정보를 다룰 경우, iframe 내에서는 최소한의 권한만 허용하는 것이 원칙입니다.

---

### 참고
https://www.lgcns.com/blog/cns-tech/cloud/50598/
https://toss.im/slash-21/sessions/2-5
https://f-lab.kr/blog/everything-about-the-microfrontend?gad_source=1&gad_campaignid=22368870602&gbraid=0AAAAACGgUFfslwd1fOQz4d1s3FJQNPza0&gclid=Cj0KCQjwyvfDBhDYARIsAItzbZExF6qIjNMH1JAJWYn7bt7h6F77fwgDKsY-s8eLv45DhbyjJiHEx0oaAvD-EALw_wcB
