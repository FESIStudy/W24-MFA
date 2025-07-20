# 마이크로서비스 아키텍처(MSA)

마이크로 서비스 아키텍처의 정의부터 살펴보자.
하나의 거대한 애플리케이션을 여러 개의 작고 독립적인 서비스로 분리하여 개발하고 배포하는 아키텍처.
여기서 키워드는 `작고`,`독립적인`,`서비스`
떠오르는건 모노레포가 떠오른다. 그럼 둘의 관계를 어떻게 볼 수 있을까

# 모노레포와 MSA와의 관계?

각각의 정의는 다음과 같다.

## 마이크로서비스 아키텍처 (MSA)

MSA는 하나의 거대한 애플리케이션을 여러 개의 작고 독립적인 서비스로 분리하여 개발하고 배포하는 아키텍처입니다. 각 서비스는 독립적으로 개발, 배포, 확장될 수 있으며, 특정 기능에 집중합니다.

## 모노레포 (Monorepo)

모노레포는 여러 개의 독립적인 프로젝트 또는 패키지의 코드를 하나의 단일 저장소(Repository)에서 관리하는 방식입니다.

내가 착각했던 프로젝트 https://github.com/chainapsis/keplr-app-registry

앱 내부에서 쓰이는 코드들이 쪼개져 있으면 MSA인 줄 알았으나
각 쪼개진 것들이 독립적인 `서비스`가 아님. 내부 비즈니스로직에 쓰이는 코드들을 패키지형태로 쪼개놓은것.
각각이 따로 어떤 서비스를 제공하지는 않고 유틸함수 정도의 분리였음.
위 프로젝트는 모노레포에만 해당한다고 보면 된다.

처음 궁금한건 찾아봤으니.이제 msa에 대해서 좀 더 알아보자

그럼 MSA는 기존 아키텍처랑 뭐가 다르냐.
내가 아는 기존 방식은 모놀리식(Monolithic) 아키텍처라고 부르는거같은데
예로 쇼핑몰 사이트를 하나 만들었다 치자. 이 쇼핑몰 사이트의 모든 기능들
예로 뭐 회원가입, 장바구니등등의 기능들을 하나의 코드안에 다 있는, 그니까 하나의 파일(코드스플리팅 안했다 하자.)로 배포하는걸 MSA라고 하는거같다.
여기서 말하는 기능들은 api라고 이해를 해보자.

?:api 라우트만 다르게만 짜면 msa냐?

당연히 이건 아니고 서로의 api가 다른 배포주기를 가지고
독립적으로 배포가능해야함. 이러면 각각이 api가 죽어도 전체가 죽지는 않으니까.

아하 여기까지 백엔드의 MSA에 대한 설명이었네요. 어쩐지 이상하더라고.

설명을 다시 써보자.

- 독립적인 SPA/애플리케이션: 각 기능(대시보드, 주문, 결제, 마이페이지)이 자체적인 SPA 형태로 존재하며, 각각 독립적으로 개발, 빌드, 배포됩니다.

- 느슨한 결합: 각 마이크로 프론트엔트는 다른 마이크로 프론트엔드에 대한 강한 의존성을 갖지 않습니다.

- 기술 스택 유연성: 각 마이크로 프론트엔드마다 다른 기술 스택(예: 대시보드는 React, 주문은 Vue, 결제는 Angular)을 사용할 수도 있습니다 (권장되지는 않지만 가능).

- 통합 방식: 이러한 독립적인 SPA들을 최종 사용자에게 하나의 응집된 사용자 경험으로 제공하기 위한 "컨테이너 애플리케이션" 또는 "셸 애플리케이션"이 필요합니다. 이는 주로 웹 서버 수준(Nginx, Caddy), 클라이언트 사이드에서 JavaScript를 통해 통합하거나(Single-SPA, Module Federation), 빌드 타임에 통합하는 방식이 사용됩니다.

오호라. 라우트별로 서로 다로 배포할 수도 있는거였다.
그럼 이런것들을 하나로 뭉치긴 해야할텐데. 그러면 엔진엑스에서 라우팅을 따로 처리해서 매번 새 페이지로 라우팅을 해주는건가??

통합 방식에 대해 알아보자

iframe도 있고 내가 말한거처럼 웹서버로 통합처리할 수 있는데 이건 장점이 그닥 없고 잘 안쓰는거같다.

우리가 알아볼만한 이거인거같다

### Single-SPA (프레임워크 agnostic):

원리: Single-SPA는 각 마이크로 프론트엔드를 "애플리케이션"으로 등록하고, 특정 URL 라우트나 조건에 따라 해당 애플리케이션을 활성화(mount)하거나 비활성화(unmount)합니다.

장점: React, Vue, Angular 등 다양한 프레임워크로 만들어진 마이크로 프론트엔드를 하나의 페이지에 통합할 수 있습니다.

예시:

index.html에서 Single-SPA 프레임워크를 로드합니다.

Single-SPA 설정에서 /dashboard 경로에는 대시보드 애플리케이션을, /orders 경로에는 주문 애플리케이션을 매핑합니다.

사용자가 /orders로 이동하면, Single-SPA가 주문 애플리케이션의 JavaScript 번들을 동적으로 다운로드하여 DOM에 마운트합니다.

전혀 이해를 못하겠다.
일단 폴더구조부터 예시를 찾아보자

```txt
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

보면 각자 패지키 제이슨도 있고 웹팩설정 코드들도 있는데. 다 따로 배포가 가능한 형태이다.

근데 이런 통합 분류 말고도 또 있다.
그럼 또 뭐가 있냐
Build-time 통합 - Run-time 통합

이렇게 또 큰 분류가 있다고 한다.
Build-time 통합은

```shell
# 빌드 시점에 모든 마이크로앱을 하나로 합침
npm run build  # → 하나의 큰 번들 생성
```

음 전혀 의미없어보이네요.

```js
// 런타임에 다른 앱을 동적 로딩
const PaymentApp = lazy(() => import('payment-app/PaymentComponent'));
```

진짜 런타임에 로딩하는 그런거다.

가만보니 각각의 페이지에서 다 따로 배포가 가능한 구조에서
상태값 공유는 어떻게 하는걸까

각각 주스탠드 같은걸 쓴다고 했을때 하나 상태값 변하면 이벤트를 디스패치해서 그렇게 상태공유를 하는거같다.

```js
// 사용자 서비스
class UserService {
  async registerUser(userData) {
    const user = await this.userRepository.create(userData);

    // 이벤트 발행
    await this.eventBus.publish('domain.user', 'user.registered', {
      userId: user.id,
      email: user.email,
      name: user.name,
    });

    return user;
  }
}

// 주문 서비스 - 사용자 정보 캐싱
class OrderService {
  constructor() {
    // 사용자 이벤트 구독
    this.eventBus.subscribe('domain.user', 'user.registered', this.handleUserRegistered.bind(this));
    this.eventBus.subscribe('domain.user', 'user.updated', this.handleUserUpdated.bind(this));
  }

  async handleUserRegistered(event) {
    // 로컬 사용자 캐시에 저장
    await this.userCache.set(event.userId, {
      email: event.email,
      name: event.name,
      createdAt: new Date(),
    });
  }

  async createOrder(userId, products) {
    // 로컬 캐시에서 사용자 정보 조회
    let user = await this.userCache.get(userId);

    if (!user) {
      // 캐시 미스 시 사용자 서비스 호출 (Circuit Breaker 적용)
      user = await this.userServiceClient.getUser(userId);
      await this.userCache.set(userId, user);
    }

    const order = await this.orderRepository.create({
      userId,
      userEmail: user.email, // 비정규화
      products,
    });

    // 주문 생성 이벤트 발행
    await this.eventBus.publish('domain.order', 'order.created', {
      orderId: order.id,
      userId: order.userId,
      amount: order.totalAmount,
    });

    return order;
  }
}

// 알림 서비스 - 여러 도메인 이벤트 구독
class NotificationService {
  constructor() {
    this.eventBus.subscribe('domain.user', 'user.registered', this.sendWelcomeEmail.bind(this));
    this.eventBus.subscribe('domain.order', 'order.created', this.sendOrderConfirmation.bind(this));
  }

  async sendWelcomeEmail(event) {
    await this.emailService.send({
      to: event.email,
      template: 'welcome',
      data: { name: event.name },
    });
  }
}
```

그러면 우리는 어떻게 MSA를 만들 수 있죠?
요즘 핫한건 웹팩의 모듈 페더레이션이거같은데.
솔직히 잘 이해안되서 다른 분들이 더 잘 설명해주실거라 믿고 저는 single-spa이 라이브러리로 갑니다.

# single-spa

single-spa는 여러 개의 독립적인 프론트엔드 애플리케이션을 하나의 페이지에서 함께 실행할 수 있게 해주는 메타 프레임워크 라고 한다

이걸 쓰면 각 팀(뭐 페이지별로 팀마다 나눠서 개발한다 가정)마다 따로 배포도 가능하고 어디는 리액트 어디는 스벨트 자기들 맘대로 쓸 수 있다는 장점이 있다.
