## Angular ㅡ Testing Guide

https://medium.com/google-developer-experts/angular-2-testing-guide-a485b6cb1ef0

TestBed, fixtures, async 와 fakeAsync/tick을 이용한 따라하기 쉬운 9가지 예제.

이 포스팅에서는 Angular Application에서는 가장 많이 테스트하는 Components, Services, Http & Pipes 등과 함께 조금 덜 알려진 영역인 Directives, Router 또는 Observable의 유닛 테스트도 같이 알아본다. 테스트를 작성할 때 **테스트 체크리스트**가 많은 도움이 될 것이다.

테스트를 위해서는 다음 API들을 알아야 한다.

- 간단한 Jasmine: suites, specs, expectations, matchers
- Angular 테스트 : setup, dependency injection, testing checklist
- 테스트 유틸리티: TestBed, inject, fixtures, async, fakeAsync/tick, jasmine.done
- 테스트 예제들: Components, Services, Http & MockBackend, Directives, Pipes, Routes, Observable & EventEmitter

## Jasmine 소개

Jasmine은 behaviour-driven 노테이션을 사용해 유연하고 향상된 테스트를 제공하는 오픈 소스 프레임워크다.

### Jasmine 주요 개념

- Suites - ***describe(string, function) 함수***. 제목과 하나 이상의 Spec을 정의하는 함수를 가짐
- Specs - ***it(string, function) 함수***. 제목과 하나 이상의 Expectation을 정의하는 함수를 가짐
- Expectations - true 나 false로 평가되는 assertion. 기본 문법은 ***expect(actual).toBe(expected)***
- Matchers - 공통 assertion들을 위해 미리 정의된 함수들. ex) ***toBe(expected)***, ***toEqual(expected)***

> **`.toEqual()`**은 deep comparision을 하고, .**`toBe()`**는 reference 값만 비교한다.

### 시작과 마무리를 위한 함수

Jasmine은 테스트의 시작과 마무리를 위한 4개의 핸들러를 제공한다.

beforeEach, afterEach 함수는 각 Spec마다 실행이 되고,

beforeAll, afterAll 함수는 Suite 당 한 번 실행된다.

> 각 Spec 의 전후에 작업할 일이 있다면 beforeEach 와 afterEach를 사용한다.

## Angular 테스트

### Testing 설정

환경을 설정하는 방법은 여러 개가 있다. Jasmine의 SpecRunner.html을 사용할 수도 있고, Karma의 test runner로 통합할 수도 있다.

(뭔 말인지 잘 모르겠음)

### Dependency Injection (DI)

TestBed는 @NgModule과 비슷하게 테스트에 의존성을 주입해주는 역할을 한다. TestBed.configureTestingModule에 의존성에 대한 정보를 넘기면서 호출하면 된다.

```typescript
@NgModule({
  declarations: [ ComponentToTest ] 
  providers: [ MyService ]
}) 
class AppModule { }
TestBed.configureTestingModule({
  declarations: [ ComponentToTest ],
  providers: [ MyService ]  
});
//get instance from TestBed (root injector)
let service = TestBed.inject(MyService); // get은 타입 문제가 있어서 이제는 inject를 사용해 서비스 인스턴스를 받아와야 한다.
```

Component를 인스턴스화하는 예제도 살펴보자. 첫 번째는 Component의 인스턴스를 포함하는 fixture를 동기적으로 생성하는 예제다.

```typescript
// synchronous
beforeEach(() => {
  fixture = TestBed.createComponent(MyTestComponent);
});
```

두 번째는 외부에 존재하는 템플릿과 CSS를 이용해 XHR을 호출하는 비동기 예제다.

```typescript
// asynchronous 
beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [ MyTestComponent ],
  }).compileComponents(); // compile external templates and css
}));
```

의존성이 비동기로 처리되는 경우 async를 사용할 수 있다. 빌드 설정에 따라 모든 템플릿과 CSS가 내부에 존재하는 경우 안전하게 동기적으로 접근하면 된다.

### Testing Checklist

- **테스트의 종류는?** Isolated, shallow, integration Test

  > #### Isolated Tests
  >
  > 복잡한 컴포넌트를 렌더링하지 않고 테스트하기. 로직만 비슷한 fake action, router 등등으로
  >
  > #### Shallow Testing
  >
  > 컴포넌트 템플릿을 렌더링해야지만 할 수 있는 테스트도 있다. 이 때 컴포넌트의 children은 렌더링하지 않고 템플릿만 렌더링하는 방법.
  >
  > #### Integration Testing
  >
  > 전체 애플리케이션을 실행하는 테스트. Shallow 테스트와 Integration 둘 다 컴포넌트를 렌더링하지만, Shallow 테스트에는 의존성을 mock해서 컴포넌트에 전달한다. Integration 테스트는 컴포넌트가 제대로 동작하는지를 테스트하고 Shallow 테스트는 컴포넌트의 디자인을 이끌어낼 때 사용될 수 있다.

- **Mock, Stub 또는 Spy를 사용할 수 있는가?** 의존성은 테스트 자체에서 다룰 수 있어야 한다. 이들을 사용하면 효율적으로 테스트할 수 있다.

- **Sync or Async?** 테스트에서 비동기 호출을 사용하는가? Promise, Observable, XHR 등을 사용하는지, 컴포넌트가 TemplateUrl 이나 styleUrls을 사용하는지 아니면 inline인지에 따라서 적절한 API를 사용해야 한다.

  

## Testing 예제

### Component 테스트

```typescript
@Component({
  selector: 'greeter',
  template: `<h1>Hello {{name}}!</h1>`
})
export class Greeter { 
  @Input() name;
}
```

이 컴포넌트를 테스트하기 위해서 TestBed를 이용해서 공용으로 사용되는 설정을 한다.

테스트 중에 사용할 수 있도록 TestBed를 사용해 해당 종속성을 로드한다. beforeEach를 사용하면 의존성 주입 등 테스트를 위한 사전 설정 코드가 중복되는 것을 방지할 수 있다.

```typescript
escribe('Component: Greeter', () => {
  let fixture, greeter, element, de;
  
  //setup
  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [ Greeter ]
    });

    fixture = TestBed.createComponent(Greeter);
    greeter = fixture.componentInstance;  // to access properties and methods
    element = fixture.nativeElement;      // to access DOM element
    de = fixture.debugElement;            // test helper
  });
  
  //specs
  it('should render `Hello World!`', async(() => {
    greeter.name = 'World';
    //trigger change detection
    fixture.detectChanges();
    fixture.whenStable().then(() => { 
      expect(element.querySelector('h1').innerText).toBe('Hello World!');
      expect(de.query(By.css('h1')).nativeElement.innerText).toBe('Hello World!');
    });
  }));
}) 
```

TestBed.createComponent 를 사용해서 Greeter 컴포넌트의 인스턴스를 생성했다. 이 컴포넌트 인스턴스는 fixture 내부에서 접근이 가능하다.

```pseudocode
abstract class ComponentFixture {
	debugElement;				// test helper
	componentInstance;	// 프로퍼티와 메소드에 접근하기 위해 사용
	nativeElement;			// DOM 요소에 접근하기 위해 사용
	detectChanges();		// Component의 변화 감지 작동
}
```

detectChanges로 변화 감지를 시작한 후, whenStable을 사용해 비동기 호출이 끝난 후 예상 값과 결과를 비교했다. 

렌더링된 텍스트에 접근하기 위해서 2개의 API를 사용했다.

- element.querySelector('h1').innerText
- de.query(By.css('h1')).nativeElement.innerText

debugElement에서 사용할 수 있는 다른 쿼리로는 `query(By.all())`과 `query(By.directive(MyDirective))`가 있다.

### Service 테스트

아래와 같은 *LanguageService*가 있다고 하자.

```typescript
export class LanguagesService {
  get() {
    return ['en', 'es', 'fr'];
  }
}
```

이전 예제와 비슷하게 beforeEach를 이용해서 서비스 인스턴스를 생성한다. 

```typescript
describe('Service: LanguagesService', () => {
  let service;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [ LanguagesService ]
    });
    service = TestBed.inject(LanguagesService);
  });

  it('should return available languages', () => {
    let languages = service.get();
    expect(languages).toContain('en');
    expect(languages).toContain('es');
    expect(languages).toContain('fr');
    expect(languages.length).toEqual(3);
  });
});A
```



### Http를 사용하는 테스트

```typescript
export class LanguagesServiceHttp {
  constructor(private http:Http) { }
  
  get(){
    return this.http.get('api/languages.json')
      .map(response => response.json());
  }
}
```

위 예시에서 json 파일을 읽어오기 위해 `http.get()` 메소드를 사용한다. 그 후에 `Observable.map()` 을 통해 response를 json으로 변환한다.





```typescript
describe('Service: LanguagesServiceHttp', () => {
  let service;
  
  //setup
  beforeEach(() => {
    TestBed.configureTestingModule({
       imports: [ HttpModule ],
   		 providers: [ LanguagesServiceHttp ]
    });
    service = TestBed.inject(LanguagesService);
  });
  
  //specs
  it('should return available languages', async(() => {
    service.get().subscribe(x => { 
      expect(x).toContain('en');
      expect(x).toContain('es');
      expect(x).toContain('fr');
      expect(x.length).toEqual(3);
    });
  }));
}) 
```

비동기 처리에 따른 `async`를 사용한 것만 빼면 테스트 코드는 위와 흡사하다.

> XHR 호출이 포함되어있을 경우 fakeAsync의 사용이 불가능하다.

### MockBackend를 사용하는 테스트

더 정교한 방법은 HTTP 호출을 MockBackend로 대체하는 것이다. 이를 위해서는 `provide`를 이용할 수 있다. MockBackend를 이용하면 실제 backend를 건드리지 않고 응답을 받을 수 있어 테스트에 적합하다.

```typescript
describe('MockBackend: LanguagesServiceHttp', () => {
  let mockbackend, service;

  //setup
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [ HttpModule ],
      providers: [
        LanguagesServiceHttp,
        { provide: XHRBackend, useClass: MockBackend }
      ]
    });
    service = TestBed.inject(LanguageServiceHttp);
    mockbackend = TestBed.inject(XHRBackend);
  });
    
  //specs
  it('should return mocked response (sync)', () => {
    let response = ["ru", "es"];
    mockbackend.connections.subscribe(connection => {
      connection.mockRespond(new Response(new ResponseOptions({
        body: JSON.stringify(response)
      }));
    });
    service.get().subscribe(languages => {
      expect(languages).toContain('ru');
      expect(languages).toContain('es');
      expect(languages.length).toBe(2);
    });
  });  
})
```



### Directive 테스트

아너무많아



























