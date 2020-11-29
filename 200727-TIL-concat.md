## 처리 순서 보장 concat

![concat](https://rxjs.dev/assets/images/marble-diagrams/concat.png)

concat은 옵저버블의 순서를 보장한다. 이전 옵저버블이 끝나야 다음 옵저버블로 넘어간다.

```typescript
forkJoin([
  concat(this.project$,
         this.fetchFromQueryParamMap$),
  ...
]).pipe(
  takeUntil(this.destroyed$)
).subscribe();
```

그래서 이렇게 쓰면 project$로 비동기 데이터를 불러온 후에 그 데이터를 이용해서 다음 작업을 할 수 있다.