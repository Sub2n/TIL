## ViewChild

view query를 설정하는 프로퍼티 데코레이터. Change detector는 뷰 DOM의 selector와 매칭되는 첫번째 엘리먼트나 디렉티브를 찾는다. 만약 DOM이 변화하면 새로운 child가 매치되고 프로퍼티가 업데이트된다.

### Description

뷰 쿼리들은 `ngAfterViewInit`이 호출되기 이전에 세팅된다.

#### Metadata Properties:

- selector - 쿼리를 할 때 사용되는 디렉티브의 타입이나 이름
- read - 쿼리된 요소에서 토큰을 읽을 때 사용됨
- static - **변화 감지가 실행되기 전에 쿼리된 결과를 매칭하면 True**, 변화 감지 이후에 매칭하면 false. 디폴트는 false. ViewChild로 선택할 요소가 부모로부터 전달받는 요소가 아니라면 static을 true로 설정하면 된다.

아래와 같은 selector들을 지원한다.

- `@Component` 나 `@Directive` 데코레이터의 클래스
- string으로 된 템플릿 참조 변수 `<my-component #comp></my-conpmnent>` 는 `@ViewChild('comp')` 로 쿼리가능
- 현재 컴포넌트의 child 컴포넌트 트리에 정의된 모든 프로바이더 `@ViewChild(SomeService) someService: SomeService`
- string 토큰으로 정의된 모든 프로바이더 `@ViewChild('someToken') someTokenVal: any`
- 모든 TemplateRef `<ng-template></ng-template>` 은 `@ViewChild(TemplateRef) template;`



## PostCSS

PostCSS 플러그인은 CSS를 파싱하고 브라우저 vendor 별로 사용되는 CSS prefix를 붙여준다. 

Angular CLI는 Autoprefixer를 사용하여 다른 브라우저 및 브라우저 버전과의 호환성을 보장한다. 특정 브라우저를 대상으로하거나 빌드에서 특정 브라우저의 버전을 제외할 수 있다.

Autoprefixer는 내부적으로 Browserslist 라이브러리를 사용해 prefix를 지원할 브라우저를 찾는다. Browserslist는 `package.json` 파일의 `browserslist` 속성 또는 `.browserslistrc` 라는 구성 파일에서 옵션을 찾는다.

따라서 Angular에서 CSS를 작성할 때 `package.json` 에 browserslist 정보를 등록한다면 따로 `-moz-`, `-ms-` 등의 prefix를 별도로 사용할 필요가 없다. 



## CSS selector로 id 쓰지 않기

id 속성의 경우 동일한 id가 페이지 내에 한 개만 존재해야 valid한 HTML 문서가 될 수 있다.

Angular Component의 경우 페이지 내에서 여러 컴포넌트를 쓸 수도 있기 때문에 정말 특별한 이유가 아니면 id 대신 class로만 스타일링 하도록 한다. 