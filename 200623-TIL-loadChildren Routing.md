## loadChildren은 pathMatch랑 같이 못 쓴다

```typescript
const routes: Routes = [
  {
    path: '',
    pathMatch: 'full', < 에러남!!!!
    canActivate: [I18nGuard, AuthGuard],
    loadChildren: () => import('./pages/pages.module')
      .then(m => m.PagesModule),
  },
  {
    path: 'welcome',
    canActivate: [I18nGuard, AlreadyLoggedInGuard],
    loadChildren: () => import('./auth/welcome/welcome.module')
      .then(m => m.WelcomeModule)
  }
];
```

pathMatch를 써준 이유는 

```typescript
{
  path: '',
  canActivate: [I18nGuard, AuthGuard],
  loadChildren: () => import('./pages/pages.module')
    .then(m => m.PagesModule),
},
```

얘가 위에 있으면 밑의 welcome으로는 라우팅이 안돼서였다. 그래서 전에는

```typescript
const routes: Routes = [
  {path: '', redirectTo: 'home', pathMatch: 'full'},
  {
    path: 'home',
    canActivate: [I18nGuard, AuthGuard],
    loadChildren: () => import('./pages/pages.module')
      .then(m => m.PagesModule),
  },
  {
    path: route.welcome.path,
    canActivate: [I18nGuard, AlreadyLoggedInGuard],
    loadChildren: () => import('./auth/welcome/welcome.module')
      .then(m => m.WelcomeModule)
  }
];
```

이렇게 해결했는데 이러면 /home이라고 쓸데없는 주소가 붙는다. 너무 해결하고 싶었는데 라우트 배열의 순서만 바꿔주면 해결되는 문제였다......... 라우팅을 할 때 순서대로 참고하나보다.

```typescript
const routes: Routes = [
  {
    path: 'welcome',
    canActivate: [I18nGuard, AlreadyLoggedInGuard],
    loadChildren: () => import('./auth/welcome/welcome.module')
      .then(m => m.WelcomeModule)
  },
  {
    path: '',
    canActivate: [I18nGuard, AuthGuard],
    loadChildren: () => import('./pages/pages.module')
      .then(m => m.PagesModule),
  },
];
```

따로 path를 덧붙일 필요 없이 간략하게 원하는대로 라우팅할 수 있다. 공부할 때 조금 더 Angular가 동작하는 원리에 집중해서 공부해야겠다.