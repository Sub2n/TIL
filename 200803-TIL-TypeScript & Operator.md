## TypeScript & Operator

타입 Dog와 타입 Cat 을 모두 포용하는 타입을 만들 때는

```typescript
type Annimal = Dog & Cat;
```

이다.

Union Type `|` 는 Dog와 Cat 둘 중 하나만 된다.