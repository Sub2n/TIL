## Promise

자바스크립트가 비동기 처리에 사용하던 콜백 패턴의 문제점은 크게 두 가지이다.

1. 비동기 처리의 결과를 사용한 작업을 하기 위해 콜백 함수가 계속 중첩되어 가독성이 나빠지고 유지보수성이 떨어진다.
2. 비동기 함수의 경우 함수를 실행하는 caller가 비동기 함수를 호출할 당시의 실행 컨텍스트가 아니므로 에러를 캐치할 수 없다.

이런 한계를 극복하기 위해서 ES6에서 Promise가 도입되었다.

## Promise 생성

Promise는 Promise 생성자 함수를 통해 인스턴스화 한다. 이때 Promise 생성자 함수는 비동기 처리를 수행할 콜백 함수를 인자로 전달받고, 이 콜백 함수는 resolve와 reject 함수를 인수로 전달받는다.

```javascript
// Promise 객체 생성
const promise = new Promise((resolve, reject) => {
  // Promise 함수의 콜백 함수 내부에서 비동기식 처리를 수행한다.
  if (/* 비동기 처리 성공 */) {
    resolve('result');
  } else { /* 비동기 처리 실패 */
    reject('failure reason');
  }
});
```

Promise 생성자 함수가 인수로 전달 받은 콜백 함수 내에서 비동기 처리를 수행한다. 이때 처리가 성공하면 resolve 함수를 호출하며 결과를 넘기고, 실패하면 reject 함수를 호출하며 에러를 넘긴다.

Promise로 구현된 비동기 함수는 함수 내부에서 Promise 객체를 생성하고 반환한다. 반환된 Promise 객체는 비동기 처리가 성공(fulfilled)했는지 실해(rejected)했는지 등의 state를 갖는다.

| 상태      | 의미                                                         | 구현                                               |
| --------- | ------------------------------------------------------------ | -------------------------------------------------- |
| pending   | 비동기 처리가 아직 수행되지 않은 상태                        | resolve 또는 reject 함수가 아직 호출되지 않은 상태 |
| fulfilled | 비동기 처리가 수행된 상태 (성공)                             | resolve 함수가 호출된 상태                         |
| rejected  | 비동기 처리가 수행된 상태 (실패)                             | reject 함수가 호출된 상태                          |
| settled   | fulfilled, rejected와 상관 없이 pending이 아닌 상태, 즉 수행은 된 상태 | resolve 또는 reject 함수가 호출된 상태             |

Promise 객체의 state는 resove 또는 reject 함수를 호출하는 것으로 결정된다. resove 또는 reject 함수를 호출할 때 전달한 비동기 처리 결과 또는 에러는 Promise 객체의 후속 처리 메소드에게 전달된다.

## Promise의 후속 처리 메소드

프로미스로 구현된 비동기 함수는 Promise 객체를 반환해야 한다. 프로미스로 구현된 비동기 함수를 호출하는 쪽에서는 Promise 객체의 후속 처리 메소드 then, catch, finally를 통해 비동기 처리 결과 또는 에러 메시지를 전달 받아 후속 처리를 수행한다. Promise의 후속 처리 메소드는 Promise 객체의 state에 따라 체이닝 방식으로 호출한다.

- Promise.prototype.then

  then 메소드는 두 개의 콜백 함수를 인자로 전달 받는다. 첫 번째 콜백 함수는 프로미스의 성공(fulfilled) 시 호출되고 두 번째 함수는 프로미스의 실패(rejected) 시 호출된다. 그런데 실패를 캐치하려면 그냥 catch를 쓰는 게 좋다.  then 메소드는 Promise 객체를 반환한다.

- Promise.prototype.catch

  예외(비동기 처리에서 발생한 에러와 then 메소드에서 발생한 에러)가 발생하면 호출된다. catch 메소드는 Promise 객체를 반환한다.

- Promise.prototype.finally

  프로미스의 성공(fulfilled) 또는 실패(rejected)와 상관 없이 무조건 한 번은 호출된다. 프로미스의 상태와 상관 없이 수행해야할 처리를 하면 된다. 또는 then과 catch에서 공통적으로 수행할 작업을 finally에서 처리하면 중복을 피할 수 있다.













