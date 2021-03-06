## 틸트(~) 대신 캐럿(^) 사용하기

npm에서 package.json에서 의존성의 버전을 명시할 때 기존 기본값인 ~1.2.3 대신 ^1.2.3 처럼 사용한다.

#### Semactic Versioning Specification (SemVer)

> 1. MAJOR version when you make incompatible API changes,
> 2. MINOR version when you add functionality in a backwards compatible manner, and
> 3. PATCH version when you make backwards compatible bug fixes.

- MAJOR version

  API의 호환성이 깨질만한 변경사항

- MINOR version

  하위 호환성을 지키는 범위 내에서 기능이 추가된 것

- PATCH version

  하위 호환성을 지키는 범위 내에서 버그가 수정된 것

버전을 명시할 때 MAJOR.MINOR.PATCH 형식으로 작성하여 관리한다.

#### 틸트(~)

`npm install` 을 사용하여 dependency를 업데이트할 때 틸트(~)를 사용하면 지정한 버전의 마지막 자리 범위에서만 업데이트를 한다.

0.0.x 까지 써있으면 PATCH 버전 내에서만 업데이트

0.x 까지 써있으면 MINOR 버전 내에서만 업데이트

x 까지만 써있으면 MAJOR 버전 내에서 업데이트

- `~0.0.1` : `>=0.0.1 <0.1.0`
- `~0.1.1` : `>=0.1.1 <0.2.0`
- `~0.1` : `>=0.1.0 <0.2.0`
- `~0` : `>=0.0 <1.0` 

#### 캐럿(^)

캐럿(^)은 틸트(~)와 달리 Nods.js 모듈이 위의 SemVer 규약을 따른다는 것을 신뢰한다는 가정 하에서 동작한다. MAJOR와 달리 MINOR와 PATCH 버전은 하위 호환성이 보장되어야하므로 해당 버전의 범위 내에서 업데이트를 한다.

- `^1.0.2` : `>=1.0.2 <2.0.0`
- `^1.0` : `>=1.0.0 <2.0.0`
- `^2` : `>=2.0.0 <3.0.0`

즉, 틸트(~)와 달리 `1.x.x` 내에서는 하위 호환성이 보장되므로 그 내에서 모두 업데이트 하겠다는 의미인데 예외사항이 있다.

버전이 1.0.0 미만인 경우 (MAJOR version이 0인 경우) 0.x.x 버전은 pre-release라고 부르며 API가 수시로 변경된다. 따라서 캐럿(^)을 사용할 때 `0.x.x` 버전에서는 틸트(~)처럼 동작해 지정한 버전의 자릿수 내에서만 업데이트한다.

- `^0.1.2` : `>=0.1.2 <0.2.0`
- `^0.1` : `>=0.1.0 <0.2.0`
- `^0` : `>=0.0.0 <1.0.0`
- `^0.0.1` : `==0.0.1` (`0.0.x` 인 경우 하위 호환성 유지가 안 될 가능성이 높으므로 지정한 버전만을 사용)





---

**Reference**

https://semver.org/

https://docs.npmjs.com/about-semantic-versioning





## Mono Repository

![img](https://miro.medium.com/max/1342/1*jXZ26bo8TQE1Q0RBMan8kQ.jpeg)



Monorepo 는 single version controlled repository로, 여러 개의 분리된 프로젝트를 하나의 레포지토리로 관리한다. 이 방식은 공유되는 코드를 통합하고, 중복되는 로직을 피하고, 팀의 협업에 도움을 줄 수 있다.

#### Angular 에서 MonoRepo 구현하는 법

```bash
ng generate application <project-name>
```

위 명령어로 프로젝트를 만들면 `projects/<project-name>`으로 프로젝트가 생성된다.

생성한 애플리케이션을 실행하려면 

```bash
ng serve --project=<project-name> --port=4021
```

빌드하려면

```bash
ng build --project <project-name> --prod
```

공용으로 사용되는 library 만들 때!

```bash
ng generate library <lib-name>
```



---

**Reference**

https://www.c-sharpcorner.com/article/project-setup-with-mono-repo-angular-architecture/