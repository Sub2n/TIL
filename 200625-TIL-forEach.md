## forEach는 루프를 도중에 멈출 수 없다.

```typescript
 menu.forEach(m => m.title === title ? m.action(title) : null);
```

find()를 사용해서 

```
menu.find(({title: menuTitle}) => menuTitle === title)?.action(title);
```

이렇게 바꾸거나 some을 사용해서 루프를 최소화 한다.

```typescript
menu.some(m => {
  if (m.title === title) {
    m.action(title);
    return true;
  }
  return false;
});
```

> #### 객체 디스트럭처링
>
> 