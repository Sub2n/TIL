## Web Cookie

HTTP 쿠키(웹 쿠키, 브라우저 쿠키)는 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각이다. 브라우저는 이 데이터 조각들을 저장했다가 동일한 서버에 재요청 시 저장된 데이터를 함께 전송한다.

이를 이용하여 사용자의 로그인 상태를 유지할 수 있다. stateless HTTP 프로토콜에서 상태 정보를 기억하기 때문이다.



### 쿠키의 사용 목적

1. 세션 관리 (Sesscion Managament)

   서버에 저장해야 할 로그인, 장바구니, 게임 스코어 등의 정보 관리

2. 개인화 (Personalization)

   사용자 선호, 테마 등의 세팅

3. 트래킹 (Tracking)

   사용자의 행동을 기억하고 분석하는 용도

   

과거 클라이언트 측에 정보를 저장할 때는 쿠키를 주로 사용했으나 모든 요청마다 쿠키가 함께 전송되기 때문에 성능이 떨어지는 원인이 될 수 있다.

지금은 modern storage APIs 중 하나인 웹 스토리지 API(localStorage, sessionStorage)와 IndexedDB를 사용하면 된다.

localStorage는 이전에 사용해보았으나 IndexedDB는 처음 들어봤다.

## IndexedDB

IndexedDB는 사용자의 브라우저에 데이터를 영구적으로 저장할 수 있는 방법 중 하나로. IndexedDB를 사용하면 웹 애플리케이션이 온라인과 오프라인 환경 모두에서 동작할 수 있다.





## Redux Store 이용 시 주의할 점

```react
import React from 'react';
import { Redirect } from 'react-router-dom';
import { connect } from 'react-redux';
import { signin, signout } from '../modules/auth';
import SignIn from '../components/SignIn';

const SignInContainer = ({ authenticated, signin, location }) => {
  const { from } = location.state || { from: { pathname: '/' } };
  if (authenticated) return <Redirect to={from} />;

  return <SignIn signin={signin} />;
};

export default connect(
  state => ({
    user: state.auth.user,
    authenticated: state.auth.authenticated,
  }),
  {
    signin,
    signout,
  },
)(SignInContainer);
```

위의 connect mapStateToProps 함수 부분에서 store.module이름.state이름으로 하지 않고 곧장 store.state이름으로 할당하여 계속 undefined를 참조하고 상태를 참조할 수 없는 실수를 했다.