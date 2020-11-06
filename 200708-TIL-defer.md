## TIL

### defer

```typescript
public setSomething(object: Object): Observable<void> {
  const { a, b, c } = data;
	return this.apiService.setData(a, b, c, object);
}
```

메서드 바디 영역은 호출시 바로 실행된다.

```typescript
public setSomething(object: Object): Observable<void> {
  return defer(() => {
    const { a, b, c } = data;
		return this.apiService.setData(a, b, c, object);
  })
}
```

위처럼 defer로 감싸면 구독시 실행되어 구독 시점의 data가 할당된다. Observable 래핑은 항상 구독시 실행하게 해두면 좋다.

