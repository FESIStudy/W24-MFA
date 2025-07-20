msa설계와 관련해서 초기 설계를 어떻게 하는지 이해가 쉬워서 원문 그대로 공유해드려용

Single-SPA를 이용한 마이크로 프론트엔드 구현은 크게 **Root Config (컨테이너/셸 역할)** 와 **등록된 마이크로 프론트엔드 애플리케이션들**로 나눌 수 있습니다.

여기서는 가장 기본적인 구조와 구현 흐름을 설명해 드릴게요. 실제로 프로덕션 레벨에서는 Webpack 설정, 빌드 도구, 배포 전략 등이 더 복잡해질 수 있습니다.

---

### **Single-SPA 구현의 핵심 단계**

1.  **루트 설정(Root Config) 애플리케이션 생성:**

    - 이것이 바로 "컨테이너 애플리케이션" 또는 "셸 애플리케이션"의 역할을 합니다.
    - 주요 역할:
      - Single-SPA 라이브러리 초기화.
      - 다른 마이크로 프론트엔드 애플리케이션들을 등록하고, 어떤 URL 경로에서 어떤 애플리케이션을 활성화할지 정의.
      - 공통 UI (헤더, 푸터 등) 또는 전역적인 로직 (인증 등) 처리.

2.  **마이크로 프론트엔드 애플리케이션 생성:**

    - 각각의 독립적인 기능 단위(예: 대시보드, 주문, 마이페이지)가 됩니다.
    - 각 애플리케이션은 Single-SPA의 라이프사이클 훅(bootstrap, mount, unmount)을 구현해야 합니다.
    - 각자의 프레임워크(React, Vue, Angular 등)를 사용하여 개발됩니다.

3.  **애플리케이션 간 통신 (선택 사항):**

    - 필요하다면, Custom Event, Pub/Sub 라이브러리, 전역 상태 관리 등을 통해 독립적인 애플리케이션들 간의 통신 방법을 마련합니다.

---

### **구현 예시 (간소화된 형태)**

여기서는 React를 사용하는 마이크로 프론트엔드와 Single-SPA 루트 설정을 예시로 보여드리겠습니다.

**프로젝트 구조 예시:**

```
microfrontend-project/
├── root-config/                <-- 컨테이너/셸 애플리케이션
│   ├── src/
│   │   ├── index.js          <-- Single-SPA 초기 설정 및 앱 등록
│   │   └── index.html        <-- 메인 HTML 파일
│   ├── package.json
│   └── webpack.config.js
│
├── mf-dashboard/               <-- 대시보드 마이크로 프론트엔드
│   ├── src/
│   │   ├── main-app.js       <-- Single-SPA 라이프사이클 훅 구현
│   │   └── components/
│   │       └── Dashboard.js
│   ├── package.json
│   └── webpack.config.js
│
└── mf-orders/                  <-- 주문 마이크로 프론트엔드
    ├── src/
    │   ├── main-app.js       <-- Single-SPA 라이프사이클 훅 구현
    │   └── components/
    │       └── Orders.js
    ├── package.json
    └── webpack.config.js
```

---

#### **1. `root-config` (컨테이너/셸) 구현**

`root-config/src/index.html` (메인 HTML 파일)

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Microfrontend Shell</title>
  </head>
  <body>
    <div id="root-container">
      <nav>
        <a href="/dashboard">대시보드</a>
        <a href="/orders">주문</a>
        <a href="/mypage">마이페이지</a>
      </nav>
      <main id="single-spa-application"></main>
    </div>
    <script src="/single-spa-root-config.js"></script>
  </body>
</html>
```

`root-config/src/index.js` (Single-SPA 초기 설정 및 앱 등록)

```javascript
import { registerApplication, start } from 'single-spa';
import { constructApplications, constructRoutes, constructLayoutEngine } from 'single-spa-layout';

// Micro-frontends의 진입점 URL을 정의합니다. (Webpack Dev Server 또는 실제 배포 URL)
const routes = constructRoutes(`
  <single-spa-router>
    <route path="dashboard">
      <application name="@my-org/mf-dashboard"></application>
    </route>
    <route path="orders">
      <application name="@my-org/mf-orders"></application>
    </route>
    <route default>
      <application name="@my-org/mf-dashboard"></application>
    </route>
  </single-spa-router>
`);

const applications = constructApplications({
  routes,
  loadApp({ name }) {
    // 여기서 각 마이크로 프론트엔드의 번들을 동적으로 로드합니다.
    // Webpack의 SystemJS Import를 사용하거나, 직접 <script> 태그를 동적으로 생성할 수도 있습니다.
    // 일반적으로 SystemJS 방식을 많이 사용하며, Webpack 설정을 통해 자동화됩니다.
    if (name === '@my-org/mf-dashboard') {
      return System.import('@my-org/mf-dashboard');
    }
    if (name === '@my-org/mf-orders') {
      return System.import('@my-org/mf-orders');
    }
    // ... 다른 앱들
  },
});

const layoutEngine = constructLayoutEngine({ routes, applications });

applications.forEach(registerApplication);

start(); // Single-SPA 시작
```

`root-config/webpack.config.js` (간략화된 Webpack 설정)

```javascript
const { merge } = require('webpack-merge');
const singleSpaDefaults = require('webpack-config-single-spa');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = (webpackConfigEnv, argv) => {
  const orgName = 'my-org';
  const defaultConfig = singleSpaDefaults({
    orgName,
    projectName: 'root-config',
    webpackConfigEnv,
    argv,
    disableHtmlGeneration: true, // index.html을 수동으로 관리
  });

  return merge(defaultConfig, {
    entry: './src/index.js',
    output: {
      filename: 'single-spa-root-config.js',
      // systemjs로 다른 앱들을 로드할 수 있도록 libraryTarget 설정
      libraryTarget: 'system',
    },
    plugins: [
      new HtmlWebpackPlugin({
        template: './src/index.html',
        inject: false, // 스크립트 주입을 직접 관리
      }),
    ],
    externals: ['single-spa', '@my-org/mf-dashboard', '@my-org/mf-orders'], // 다른 마이크로 프론트엔드를 외부 모듈로 선언
  });
};
```

**참고:** `System.import`는 SystemJS 모듈 로더와 함께 사용됩니다. Single-SPA는 Webpack 설정에서 `libraryTarget: 'system'`을 통해 SystemJS 모듈 형식으로 번들을 내보내도록 설정하는 경우가 많습니다.

---

#### **2. `mf-dashboard` (마이크로 프론트엔드) 구현**

`mf-dashboard/src/main-app.js` (Single-SPA 라이프사이클 훅 구현)

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import singleSpaReact from 'single-spa-react';
import Dashboard from './components/Dashboard';

const lifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent: Dashboard,
  domElementGetter: () => document.getElementById('single-spa-application'), // root-config의 렌더링 영역
});

export const { bootstrap, mount, unmount } = lifecycles;
```

`mf-dashboard/src/components/Dashboard.js` (실제 대시보드 UI 컴포넌트)

```javascript
import React from 'react';

const Dashboard = () => {
  return (
    <div>
      <h1>대시보드 페이지</h1>
      <p>환영합니다! 최신 활동과 정보를 확인하세요.</p>
      <ul>
        <li>새로운 메시지: 5개</li>
        <li>오늘의 매출: 1,234,567원</li>
      </ul>
    </div>
  );
};

export default Dashboard;
```

`mf-dashboard/webpack.config.js` (간략화된 Webpack 설정)

```javascript
const { merge } = require('webpack-merge');
const singleSpaDefaults = require('webpack-config-single-spa-react'); // React용 헬퍼 함수

module.exports = (webpackConfigEnv, argv) => {
  const orgName = 'my-org';
  const defaultConfig = singleSpaDefaults({
    orgName,
    projectName: 'mf-dashboard',
    webpackConfigEnv,
    argv,
  });

  return merge(defaultConfig, {
    output: {
      libraryTarget: 'system', // SystemJS 모듈로 내보내기
      publicPath: '/', // 개발 서버 또는 CDN 경로
    },
    // 공유할 라이브러리 (React, ReactDOM 등) 설정
    externals: ['react', 'react-dom'],
  });
};
```

**참고:** `mf-orders`도 `mf-dashboard`와 유사한 방식으로 구현됩니다. 단, `mf-orders/src/main-app.js`에서는 `single-spa-react` 대신 `single-spa-vue` 또는 `single-spa-angular`와 같은 해당 프레임워크 헬퍼 라이브러리를 사용하고, `rootComponent`를 `Orders` 컴포넌트로 변경하면 됩니다.

---

### **동작 방식 요약:**

1.  사용자가 웹사이트(예: `http://localhost:9000`)에 접속하면, `root-config`의 `index.html`이 로드됩니다.
2.  `index.html`은 `single-spa-root-config.js` (루트 설정 번들)를 로드합니다.
3.  `single-spa-root-config.js`는 `single-spa` 라이브러리를 초기화하고, `registerApplication`을 통해 `mf-dashboard`, `mf-orders` 등의 마이크로 프론트엔드를 등록합니다. 이때, 각 앱을 어떤 URL 경로에서 활성화할지 정의합니다 (`/dashboard`는 `mf-dashboard`, `/orders`는 `mf-orders`).
4.  사용자가 `/dashboard` 경로로 이동하면, Single-SPA는 등록된 정보에 따라 `mf-dashboard` 애플리케이션의 JavaScript 번들을 **동적으로 다운로드**합니다.
5.  다운로드된 `mf-dashboard` 번들은 `bootstrap` (초기화) -\> `mount` (DOM에 렌더링) 라이프사이클 훅을 실행하여 `Dashboard` 컴포넌트를 `single-spa-application` DIV 내부에 렌더링합니다.
6.  사용자가 `/orders` 경로로 이동하면, Single-SPA는 `mf-dashboard`를 `unmount` (DOM에서 제거)하고, `mf-orders` 번들을 다운로드하여 `bootstrap` -\> `mount` 과정을 거쳐 `Orders` 컴포넌트를 렌더링합니다.

이러한 방식으로 Single-SPA는 각 마이크로 프론트엔드를 독립적으로 관리하고, 필요할 때만 로드하여 마치 하나의 애플리케이션처럼 부드러운 사용자 경험을 제공합니다. 개발 시에는 각 마이크로 프론트엔드가 자체 개발 서버에서 실행될 수 있으며, 배포 시에는 각 앱의 번들이 CDN 등에 독립적으로 배포됩니다.
