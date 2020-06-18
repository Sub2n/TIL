# Angular Universal

https://blog.angular-university.io/angular-2-universal-meet-the-internet-of-the-future-seo-friendly-single-page-web-apps/

- Single Page Application의 장점
- 그러나 모든 곳에서 SPA를 이용하지 않는 이유
- SPA의 SEO(Search Engine Optimization)의 의미 이해
- Angular Universal이란?
- SSR(Server-side Rendering)은 실제로 서버에서만 렌더링을 하는 건 아니다.
- Server-side rendered SPA 데모
- 빌드 타임에 Pre-rendering - Amazon S3에 pre-renderd app 업로드하기
- Angular Universal의 핵심 기능: Dependency Injection
- 정리

해당 포스트는 Angular Universal을 소개하고 있다. Angular Universal에 대한 더 자세한 가이드는 다음 포스트에서 확인할 수 있다.

https://blog.angular-university.io/angular-universal/

## Single Page Application의 장점

Single page app들은 오랫동안 주목되어왔으며 Angular, React와 같이 유명한 자바스크립트 라이브러리들이 SPA 개발 툴이 있다.

### SPA의 장점은 이거 하나다:

- 사용자가 페이지를 이동할 때 페이지의 일부분만 변경되어 더 부드러운 사용자 경험을 제공한다.
- 첫 페이지가 로딩된 후에는 사용자가 앱을 탐색할 때 데이터만 전송한다. JSON이 브라우저에 전달되고 브라우저의 HTML 템플릿에 바로 적용된다.
- 이로 인해 성능이 향상되고 백엔드 서비스가 데이터를 반환하기 때문에 다른 작업을 할 수 있는 가능성이 생긴다.

즉, SPA의 장점은 **더 나은 사용자 경험을 제공**할 수 있다는 것이다.

페이지 전환이 더딜수록 사용자가 이탈하고 사업에 타격을 주기 때문에 UX는 굉장히 중요하다.

## 그러면 왜 SPA를 모든 곳에서 쓰진 않을까?

사용자 경험을 향상시켜주는 SPA를 왜 모두가 사용하진 않는 걸까. 

## SPA에서 SEO의 의미

SPA가 모든 곳에서 사용되지 않는 가장 큰 이유는 검색 엔진에서 동작하지 않기 때문이다. 이유는 다음과 같다.

### 1. SPA의 경우 검색 엔진이 페이지가 완성된 모습을 추측해야 한다.

Single page가 검색되면 검색 엔진은 최소한의 HTML만 볼 수 있다. MVC 프레임워크가 시작되고 페이지가 실제로 렌더링 되어야 서버에서 받은 데이터로 화면을 구성하기 때문이다. 이때 검색 엔진이 자바스크립트 프레임워크가 페이지 렌더링을 마친 모습을 추측하기 때문에 불완전한 컨텐츠를 수집할 위험이 있다.

### 2. SPA deep link는 검색 엔진이 수집하기 힘들다.

브라우저에서 지원하는 HTML5 History가 없기 때문에 SPA는 URL의 HTML 북마크 앵커 #를 기반으로 앱을 탐색한다. 그런데 이런 방식은 검색 엔진이 별도의 페이지로 판단하기 힘들다. 

따라서 SEO가 잘 되어있지 않은 경우 SPA는 검색 엔진이 올바르게 수집할 수 업다.

다행인 점은 구글이 검색 엔진을 개선해서 SPA를 더 잘 분석할 수 있게 되었다는 거지만 여전히 다른 검색 서비스들에서는 SEO가 필요하다. 또, SEO가 해결되었다고 SPA가 완벽해지는 건 아니다. SPA는 초기 로딩 시 시간이 많이 걸린다는 성능 이슈가 있다. HTML 기반 방식보다 성능이 떨어진다.

SEO 친화적이고, navigation도 되면서 모바일에서도 높은 성능을 내는 웹 애플리케이션을 만들 방법으로 Angular Universal이 있다.

## Angular Universal이란?

간략히 말하면, Angular Universal은 SPA의 장점인 부드러운 사용자 경험과 정적 페이지의 SEO, 성능을 다 잡은 애플리케이션을 개발할 수 있게 해준다.

## SSR(Server-side Rendering)은 실제로 서버에서만 렌더링을 하는 건 아니다. (클라이언트에서도 한다!)

Angular Universal은 서버에서 HTML을 렌더링하기 위한 솔루션을 제공하는 것 외에도 여러가지 기능을 가지고 있다. Server-side Rendering이라는 용어에서 Angular Universal이 Jade와 같은 서버 측 템플릿 언어와 비슷하다고 생각할 수 있지만 더 많은 기능이 있다.

Angular Universal을 사용하면 서버에서 렌더링된 초기 HTML 페이로드를 받는다. 그때 클라이언트에서 Angular도 함께 실행되어 페이지를 SPA로 만들어 클라이언트 측에서 모든 HTML을 생성한다.

따라서 결과적으로는 실행 중인 SPA와 동일하지만 서버에서 초기 HTML 페이로드를 받았기 때문에 초기 실행 속도가 빨라지고 SEO 인덱싱이 가능한 앱을 만들 수 있다.

## 빌드 타임에 Pre-rendering  

Angular Universal은 빌드 타임에 컨텐츠의 변화가 별로 없는 페이지를 pre-rendering 하는 방법도 제공한다. Pre-rendering은 웹팩 플러그인이나 다른 라이브러리를 사용할 수도 있다.

### 사용자가 페이지를 즉시 사용하면?

plain HTML이 렌더링돼서 사용자에게 제공되는 시점과 Angular가 클라이언트 측에서 시작하여 SPA로 넘기는 시점에는 초기에 지연이 있는데, 이 기간동안 사용자가 무언가를 클릭하거나 검색창에 입력을 할 수가 있다. Angular Universal은 사전 부팅(preboot) 기능을 통해 이 시점에서의 사용자 이벤트를 기록하고, Angular가 시작되면 실행하도록 한다.

이렇게 하면 Angular는 이 시점에서 발생한 이벤트에 대응할 수 있다. 서버에서는 이를 어떻게 구현하는지 알아보자

### 서버에서 HTML을 Angular로 렌더링하는 방법

컨텐츠는 express Angular Universal 렌더링 엔진인 expressEngine을 사용해 서버에서 렌더링된다.

```typescript
import {createEngine} from 'angular2-express-engine';
import { UniversalModule } from 'angular2-universal';

app.engine('.html', createEngine({}));
app.set('views', __dirname);
app.set('view engine', 'html');

@NgModule({
    bootstrap: [ App ],
    declarations: [ App, ... ],
    imports: [
        UniversalModule, // NodeModule, NodeHttpModule, and NodeJsonpModule are included
        ...
    ],
    providers: [...],

})
export class MainModule {

}

function ngApp(req, res) {
    let baseUrl = '/';
    let url = req.originalUrl || '/';

    let config = {
        req,
        res,
        requestUrl: url,
        baseUrl,
        ngModule: MainModule,
        preboot: false,
        originUrl: ROOT_URL
    };

    console.log("rendering ...");

    res.render('index', config, (err, html) => {
        res.status(200).send(html);
    });

}

```























