## Testing

### Service Tests

서비스는 Angular 구성요소 중에서 유닛 테스트 하기 가장 쉬운 구성요소다. 우선 Angular 테스트 유틸리티를 사용하지 않고도 동기적으로, 비동기적으로 서비스를 테스트하는 방법을 알아보자.

```typescript
// Straight Jasmine testing without Angular's testing support
describe('ValueService', () => {
  let service: ValueService;
  beforeEach(() => { service = new ValueService(); });

  it('#getValue should return real value', () => {
    expect(service.getValue()).toBe('real value');
  });

  it('#getObservableValue should return value from observable',
    (done: DoneFn) => {
    service.getObservableValue().subscribe(value => {
      expect(value).toBe('observable value');
      done();
    });
  });

  it('#getPromiseValue should return value from a promise',
    (done: DoneFn) => {
    service.getPromiseValue().then(value => {
      expect(value).toBe('promise value');
      done();
    });
  });
});
```

#### 의존성 있는 Service

Angular가 constructor에 주입해주는 다른 서비스를 사용하는 서비스들도 많다. 서비스의 constructor를 호출할 때 이런 종속성 객체를 만들어 주입하는 것은 어렵지 않다. 아래와 같은 서비스가 있다고 해보자.

```typescript
@Injectable()
export class MasterService {
  constructor(private valueService: ValueService) { }
  getValue() { return this.valueService.getValue(); }
}
```

 MasterService는 valueService를 의존성 주입 받아서 리턴하는 메소드를 가지고 있다. 이 경우 아래와 같은 다양한 테스트가 가능하다.

```typescript
describe('MasterService without Angular testing support', () => {
  let masterService: MasterService;
  
  it('#getValue should return real value from the real service', () => {
    masterService = new MasterService(new ValueService()); //	직접 구현해서 넣어줌
    expect(masterService.getValue()).toBe('real value');
  });
  
  it('#getValue should return fake value from a fakeService', () => {
    masterService = new MasterService(new FakeService());
    expect(masterService.getValue()).toBe('fake service value');
  });
  
  it('#getValue should return faked value from a fake object', () => {
    const fake = { getValue: () => 'fake value' };
    masterService = new MasterService(fake as ValueService);
    expect(masterService.getValue()).toBe('fake value');
  });
  
  it('#getValue should return stubbed value from a spy', () => {
    // getValue 메소드가 정의된 스파이 객체 생성
    const valueServiceSpy = jasmin.createSpyObj('ValueService', ['getValue']);
    
    // getValue 스파이가 호출되면 리턴할 값 지정
    const stubValue = 'stub value';
    valueServiceSpy.getValue.and.returnValue(stubValue);
    
    masterService = new MasterService(valueServiceSpy);
    
    expect(masterService.getValue())
    	.toBe(stubValue, 'service returned stub value');
    expect(valueServiceSpy.getValue.calls.count())
    	.toBe(1, 'spy method was called once');
    expect(valueServiceSpy.getValue.calls.mostRecent().returnValue)
    	.toBe(stubValue);
  });
});
```

첫번째 테스트에서는 new 키워드를 사용해서 ValueService를 직접 생성하고 MasterService의 생성자로 전달한다. 그러나 실제 서비스 인스턴스를 의존성으로 주입하면, 그 서비스가 실제로 어떤 동작을 할지 제어하기 힘들기 때문에 테스트가 제대로 동작하지 않을 가능성이 높다.

따라서 실제 서비스 대신 더미 객체나 스파이(spy)를 활용하는 것이 더 간편하다.

> 서비스를 mocking하는 방법 중 간단한 방법은 spy를 활용하는 것으로, 적극 활용이 장려된다.
>
> stub은 기존 코드를 흉내내거나 아직 개발되지 않은 코드를 임시로 대치하는 역할을 한다.

spy를 활용해 작성하면 테스트하는 서비스만 따로 분리할 수 있기 때문에 테스트가 편하다. 하지만 의존성 관계가 복잡하게 엮여 있거나 실제 사용하는 패턴으로 테스트를 실행해야한다면 Angular 테스트 기능을 활용해야 한다.

#### TestBed로 Service 테스트

Service 를 사용할 때 의존성 주입을 Angular에 맡기듯이, Service를 테스트할 때도 TestBed 테스트 유틸리티를 사용해서 Service를 제공하고 생성할 때 Angular DI가 Service를 생성하고 의존성을 주입하게 할 수 있다.

#### Angular TestBed

TestBed는 Angular 테스트 유틸리티 중 가장 중요한 모델이다. TestBed는 Angular의 @NgModule 역할을 하는 테스트 모듈을 동적으로 생성한다.

`TestBed.configureTestingModule()` 메소드는 메타데이터 객체를 인자로 받는데, 이 메타데이터는 @NgModule에서 사용하는 프로퍼티를 거의 다 지원한다. 따라서 NgModule에 providers 메타데이터 프로퍼티를 지정했던 것처럼 `TestBed.configureTestingModule()` 메소드에도 providers 프로퍼티를 지정할 수 있다.

```typescript
let service: ValueService;

beforeEach(() => {
  TestBed.configureTestingModule({ providers: [ValueService] });
});
```

providers 메타데이터를 지정한 후 TestBed.inject() 에 서비스 클래스를 전달하며 호출하면 서비스 인스턴스를 참조할 수 있다.

```typescript
it('should use ValueService', () => {
  service = TestBed.inject(ValueService);
  expect(service.getValue()).toBe('real value');
});
```

아니면 공통되는 서비스 주입 로직을 `beforeEach()` 내부로 빼서 변수에 할당할 수도 있다.

```typescript
beforeEach(() => {
  TestBed.configureTestingModule({ providers: [ValueService] });
  service = TestBed.inject(ValueService);
});
```

서비스에 의존성으로 주입되는 객체가 있을 경우, 이 객체의 mock 역할을 하는 객체도 providers 배열에 지정할 수 있다. 아래 예제에서 Jasmine 스파이 객체가 mock으로 사용됐다.

```typescript
let masterService: MasterService;
let valueService: ValueService;

beforeEach(() => {
  const spy = jasmine.createSpyObj('ValueService', ['getValue']);
  
  TestBed.configureTestingModule({
    // 테스트할 서비스와 의존성으로 사용될 mock 객체를 등록
    providers: [
      MasterService,
      { provide: ValueService, useValue: spy }
    ]
  });
  // 서비스의 인스턴스와 mock 객체의 인스턴스를 가져옴
  masterService = TestBed.inject(MasterService);
  valueService = TestBed.inject(ValueService);
});
```

너무어렵다!

https://codecraft.tv/courses/angular/unit-testing/mocks-and-spies/

참고할것













































