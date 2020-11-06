https://blog.angular-university.io/rxjs-higher-order-mapping/

RxJS의 switchMap, concatMap, exhaustMap, mergeMap의 특성을 살펴보기위해 이 글을 정리해본다.

- The RxJs Map Operator
- What is higher-order Observable Mapping
- Observable Concatenation
- The RxJs concatMap Operator
- Observable Merging
- The RxJs mergeMap Operator
- Observable Switching
- The RxJs switchMap Operator
- The Exhaust strategy
- The RxJs exhaustMap Operator
- How to choose the right mapping Operator?
- Running GitHub repo (with code samples)
- Conclusions

## RxJS Map Operator

Mapping operator가 무슨 역할을 하는지부터 짚고 넘어가자.

Operator들의 이름이 명시하듯이 이들이 매핑 한다는 걸 알 수 있지만 정확히 무엇을 매핑한다는 걸까? 우선 아래 RxJS의 Map operator의 마블 다이어그램을 살펴보자.

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/01-rxjs-map.png" alt="RxJs Map Operator marble diagram" style="zoom:67%;" />

## 기본 Map Operator는 어떻게 동작하는가

우리는 map operator를 통해서 input 스트림으로부터 원하는 output 스트림을 생성할 수 있다. 다이어그램 아래의 output 스트림 값들은 input 스트림 값들에 함수를 적용하여 얻어낸 값이다.

즉, map operator는 input 옵저버블의 값을 매핑하는 것이다. 아래에 HTTP request를 처리하며 map operator를 사용하는 예시가 있다.

```typescript
const http$ : Observable<Course[]> = this.http.get('/api/courses');

http$
    .pipe(
        tap(() => console.log('HTTP request executed')),
        map(res => Object.values(res['payload']))
    )
    .subscribe(
        courses => console.log("courses", courses)
    );
```

위 예제에서 우리는 HTTP 옵저버블을 생성하고 subscribe를 함으로써 get 요청을 한다. 곧 옵저버블은 JSON 객체로 된 HTTP response 값을 방출한다.

이때 HTTP response는 데이터를 payload 프로퍼티로 래핑하므로 데이터를 얻기 위해서 RxJS의 map operator를 사용한다. 그러면 mapping 함수는 JSON response payload를 payload 프로퍼티의 값들로 추출해 매핑할 것이다.

기본 mapping이 어떻게 이루어지는지 알아보았으므로 이제 higher-order mapping에 대해서 알아보자.

## Higher-Order Observable Mapping이란?

Higher-order mapping은 일반적인 값을 다른 값으로 매핑하는 것이 아니라 값을 옵저버블로 매핑한다. 매핑의 결과는 higher-order 옵저버블이다. Higher-order 옵저버블은 다른 옵저버블과 마찬가지로 옵저버블이지만, 값 자체도 옵저버블이므로 별도로 구독할 수 있다.

어렵게 들리지만 사실 이런 유형의 매핑은 언제나 발생한다. 예를 들어 옵저버블을 통해 valid한 form value를 방출하는 Angular Reactive Form이 있다고 해보자.

```typescript
@Component({
    selector: 'course-dialog',
    templateUrl: './course-dialog.component.html'
})
export class CourseDialogComponent implements AfterViewInit {

    form: FormGroup;
    course:Course;

    @ViewChild('saveButton') saveButton: ElementRef;

    constructor(
        private fb: FormBuilder,
        private dialogRef: MatDialogRef<CourseDialogComponent>,
        @Inject(MAT_DIALOG_DATA) course:Course) {

        this.course = course;

        this.form = fb.group({
            description: [course.description, 
                          Validators.required],
            category: [course.category, Validators.required],
            releasedAt: [moment(), Validators.required],
            longDescription: [course.longDescription,
                              Validators.required]
        });
    }
}
```

Reactive Form은 `this.form.valueChanges`라는 옵저버블을 제공하는데, 이 옵저버블은 사용자 입력에 따른 가장 최신 값을 방출한다. 이게 우리의 source 옵저버블이 될 것이다.

우리가 하고 싶은 건 자동 저장 기능을 구현하기 위해서 시간이 지남에 따라 방출되는 값들 중 일부를 저장하는 것이다. 이렇게 하면 사용자가 form을 채울 때 데이터가 저장되므로 실수로 새로고침을 했을 때 데이터가 손실되지 않는다.

## 왜 고차 옵저버블(higher-order Observable)인가?



자동 저장 기능을 구현하기 위해서 우리는 form value를 가져오고, 서버에 값을 저장할 HTTP 옵저버블을 생성하고 구독해야 한다.

아래처럼 구현할 수 있지만, 이렇게 하면 안 좋은 중첩 subscribe 패턴에 빠지게 된다.

```typescript
this.form.valueChanges
    .subscribe(
       formValue => {
      
           const httpPost$ = 
                 this.http.put(`/api/course/${courseId}`, formValue);

           httpPost$.subscribe(
               res => ... handle successful save ...
               err => ... handle save error ...
           );

       }        
    );
```

이렇게 subscribe가 중첩되면 중첩되는 콜백 패턴 같아지기 때문에 지양해야 한다.

## subscription 중첩 피하기

위의 중첩 패턴을 피하기 위한 방법으로 값으로부터 값을 저장하는 옵저버블로 매핑하는 방법이 있다. 이는 각 값의 저장 요청에 따라 효율적으로 higher-order 옵저버블을 생성한다.

그런 다음 각 network 옵저버블들을 구독하고 네트워크 응답을 한 번에 수신함으로써 중첩을 피하려고 한다.

이런 작업들을 RxJS의 higher order mapping operator를 이용해서 할 수 있다. 그렇다면 왜 operator가 4가지나 존재하는지 알아보자.

 `valueChanges` 옵저버블에 의해 여러 form value 들이 연속적으로 값을 방출하고, 저장 작업이 완료될 때까지 시간이 걸리면 어떻게 될지 생각해보자. 아래와 같은 방법들로 접근할 수 있다.

- 하나의 저장 요청이 완료되기 전에 다른 요청이 들어올 경우 이전 작업이 완료될 때까지 기다린다.
- 저장 작업을 병렬적으로 처리한다.
- 하나의 저장 요청이 완료되기 전에 다른 요청이 들어올 경우 이전 저장 요청을 취소하고 새로 들어온 요청을 수행한다.
- 이전 저장 요청이 진행되는 도중 새로운 요청이 들어올 경우 무시한다.

우선 이전 저장 요청이 완료된 후에만 다음 요청을 수행하는 방식을 생각해보자.

## Observable Concatenation 이해하기

순차적인 저장 기능을 구현하기 위해 **옵저버블 연결(Observable concatenation)** 개념을 알아본다. 아래 예제에서 RxJS의 `contat()` 함수를 이용해 두 개의 옵저버블을 연결한다. 

```typescript
const series1$ = of('a', 'b');
const series2$ = of('x', 'y');

const result$ = concat(series1$, series2$);

result$.subscribe(console.log);
```

`of` 생성 함수를 통해 `series1$` 과 `series2$` 라는 옵저버블을 생성한 후, `series1$` 과 `series2$` 의 연결 결과인 `result$` 옵저버블을 생성했다.

위 코드의 결과로 `result$` 옵저버블이 방출한 값은 아래와 같다.

```bash
a
b
x
y
```

보는 것처럼 첫 번째 옵저버블의 수행이 끝난 후에 두 번째인 `series2$` 옵저버블의 값이 출력된 것을 볼 수 있다.

## Observable Concatenation Marble Diagram

정확하게 어떻게 굴러가는지 알려면 마블 다이어그램을 보자.

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/01-rxjs-concat.png" alt="RxJs Map Operator marble diagram" style="zoom:67%;" />

첫번째 옵저버블의 값 b 뒤의 세로 막대는 값 a와 b를 가진 첫 번째 옵저버블 `series1$` 가 완료된 시점을 나타낸다.

- 두 옵저버블 `series1$` 과 `series2$` 가 `concat()` 함수로 넘겨진다.
- `concat()` 함수는 첫번째 인수로 넘어온 옵저버블 `series1$` 만 subscribe하고, 두번째 옵저버블은 하지 않는다.
- `series1$` 이 값 a를 방출하면 바로 `result$` 옵저버블의 output에 반영된다.
- `series2$` 는 아직 값을 방출하지 않았음을 다시 짚고 넘어가자. 얘는 아직 subscribe되지도 않았다.
- `series1$` 은 값 b도 방출하고, `result$` 옵저버블의 output에 반영될 것이다. 
- 그러고나면 `series1$` 는 완료된다. `series1$` 이 완료되었으므로 `concat()` 는 이제 `series2$` 를 subsribe한다.
- `series2$` 가 값을 다 방출하고 완료하고 나면, `result$` 옵저버블도 완료된다.

### Observable Concatenation의 키 포인트

옵저버블 concatenation은 옵저버블 completion과 관련이 있다. 우리는 첫번째 옵저버블을 받아 값을 사용하고, 완료되고 나면 다음 옵저버블을 사용한다.

다시 higher-order 옵저버블 매핑 예제로 돌아와서 연결의 개념이 어떻게 유용한지 살펴보자.

## 순차적인 저장 기능 구현을 위해 Observable Concatenation 사용하기

form value를 순차적으로 저장하기 위해서 각각의 form value를 `httpPost$` 옵저버블로 매핑한다. 그러고 나면 옵저버블을 구독해야 하는데, 다음 `httpPost$` 옵저버블을 구독하기 전에 저장을 완료해서 순차적으로 처리하고 싶다.

> 순차성을 보장하기 위해서 여러 `httpPost$` 옵저버블들을 concatenation 해야한다.

## RxJS concatMap Operator

```typescript

this.form.valueChanges
    .pipe(
        concatMap(formValue => this.http.put(`/api/course/${courseId}`, 
                                             formValue))
    )
    .subscribe(
       saveResult =>  ... handle successful save ...,
        err => ... handle save error ...      
    );
```

concatMap Operator를 사용하면 HTTP 요청은 이전 저장 요청이 끝나야지만 시작된다. concapMap Operator는 아래와 같이 처리 순서를 보장한다.

- concatMap은 각각의 form value를 받고 저장을 위한 HTTP 옵저버블로 변환한다. 이 HTTP 옵저버블을 inner Observable이라고 하자.
- concatMap은 그후에 inner Observable을 구독하고 옵저버블의 결과를 result 옵저버블로 보낸다.
- 두번째 form value가 이전 값이 서버에 저장되기 전에 온다면 **새로운 form value는 바로 HTTP 요청으로 매핑되지 않는다.**
- 대신, concatMap은 이전 HTTP 옵저버블이 완료될 때까지 기다렸다가 새로운 form value를 HTTP 옵저버블에 매핑하고 이를 구독해서 다음 저장 작업을 발생시킨다.

## Observable Merging

Observable concatenation을 일련의 HTTP 저장 작업에 적용하는 건 순서에 따라 일을 처리하기에 좋다.

그러나 이전 inner Observable이 완료될 때까지 기다리지 않고 작업을 동시에 처리해야하는 상황도 있다. 그러기 위해서는 **옵저버블을 병합(Observable Merging)**하는 방법이 있다. 옵저버블을 연결(concatenation)하는 것과 달리 병합(merge)은 다음 옵저버블을 구독하기 전에 이전 옵저버블이 완료되기를 기다리지 않는다.

대신 merge는 병합된 모든 옵저버블을 동시에 구독한 다음, 시간이 지남에 따라 각 source 옵저버블의 결과를 결합된 result 옵저버블에 출력한다.

### Merge 예제

Merging이 completion과 관계가 없다는 것을 명확히 하기 위해 다음의 절대 끝나지 않는 두 옵저버블을 생성한다.

```typescript
const series1$ = interval(1000).pipe(map(val => val*10));

const series2$ = interval(1000).pipe(map(val => val*100));

const result$ = merge(series1$, series2$);

result$.subscribe(console.log);
```

`interval()` 로 만들어진 옵저버블은 1초마다 0, 1, 2 와 같은 값을 방출한다. 두 옵저버블을 구분하기 쉽게 하기 위해서 각각 10을 곱하고 100을 곱하는 map을 적용했다.

결과는 다음과 같다.

```bash
0
0
10
100
20
200
30
300
```

### Merging과 Observable Completion

위에서 본 것처럼 병합된 source 옵저버블은 값을 방출하는 즉시 result 옵저버블에 나타난다. 병합된 옵저버블 중에 하나가 완료될지라도 다른 옵저버블이 끝날 때까지 계속 값을 방출한다.

## Merge 마블 다이어그램

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/04-rxjs-merge.png" alt="RxJs merge Example" style="zoom:67%;" />

source 옵저버블의 값은 즉시 result 옵저버블에 반영되고, result 옵저버블은 모든 source 옵저버블이 완료될 때까지 완료되지 않는다.

merge를 이해했다면 higher-order Observable mapping에서 어떻게 사용될 수 있는지 알아보자.

## RxJS mergeMap Operator

Higher-order 옵저버블 매핑의 개념과 merge 를 결합한 게 RxJS mergeMap Operator다. 

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/04-rxjs-mergeMap-2.png" alt="RxJs mergeMap Example" style="zoom:67%;" />

mergeMap Operator는 아래와 같이 동작한다.

- 각 source 옵저버블의 값들은 concatMap 때와 같이 inner 옵저버블로 매핑된다.
- inner 옵저버블은 mergeMap에 의해 구독된다.
- concapMap과 다르게 이전 inner 옵저버블의 완료를 기다리지 않고 다음 inner 옵저버블이 실행된다.
- mergeMap을 활용하면 여러 inner 옵저버블들을 같은 시간대에 동시에 값을 방출하도록 할 수 있다.

## Observable Switching

switching의 개념은 옵저버블이 끝나기를 기다리지 않는다는 점에서 concatenation보단 merging에 가깝다.

### Switch Marbel Diagram

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/05-switch.png" alt="RxJs switch Example" style="zoom:67%;" />

대각선에 주목하자. switch의 경우 그림에서 가장 위의 라인 higher-order 옵저버블을 나타내는 게 중요하다.

이 higher-order 옵저버블은 그 자체로 옵저버블인 값을 방출한다.

> 대각선이 higher-order 옵저버블 라인에서 분기되는 순간은 옵저버블 값이 방출되고 switch에 의해 구독된 순간이다.

- 고차 옵저버블이 방출한 첫번째 inner 옵저버블 (a-b-c-d)은 switch에 의해 구독된다.
- 첫번째 inner 옵저버블 (a-b-c-d)은 값 a, b를 방출하고 이 값들은 바로 반영된다.
- 그러나 두번째 inner 옵저버블 (e-f-g)이 방출되면서 첫번째 옵저버블 (a-b-c-d)의 **구독이 취소된다**. 이게 **switching의 핵심**이다. 말 그대로 다음 옵저버블이 방출되는 순간 이전 옵저버블을 갈아끼우는(switch) 것이다.
- 두번째 inner 옵저버블 (e-f-g) 은 값을 방출하고 이는 결과에 반영된다. 그러나 **첫번째 inner 옵저버블 (a-b-c-d)도 여전히 값을 방출하고 있다. unsubscribe해서 ouput에 반영되지 않을 뿐이다.**

## RxJS switchMap Operator

고차 매핑에 switch를 적용해보자. 값 1, 3, 5를 방출하는 input 스트림이 있다고 가정한다.

이 값들을 각각 옵저버블로 매핑해 고차 옵저버블을 얻을 것이다.

<img src="https://s3-us-west-1.amazonaws.com/angular-university/blog-images/rxjs-map-operators/06-switchMap-2.png" alt="RxJs switchMap Example" style="zoom:67%;" />

- source 옵저버블이 값 1, 3, 5를 방출한다.
- 이 값들이 매핑 함수가 적용되어 옵저버블들로 변환된다.
- 매핑된 inner 옵저버블들은 switchMap에 의해 구독된다.
- inner 옵저버블들이 값을 방출하면 값은 바로 결과에 반영된다.
- 그러나 이전 옵저버블이 완료되기 전에 5와 같은 새로운 값이 방출되면, 이전의 inner 옵저버블 (30-30-30)은 unsubscribe되고 결과에 반영되지 않는다.
- 나머지 하나의 30은 방출되었지만 unsubscribe되어 결과에 반영되지 않았을 뿐이라는 것에 주의하자.































