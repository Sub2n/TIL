## startWith

![startWith marble diagram](https://rxjs-dev.firebaseapp.com/assets/images/marble-diagrams/startWith.png)

Source Observable이 스트림을 방출하기 전에 무조건 인자로 받은 스트림을 한 번 방출한다.

`startWith(0)` 이면 왜 이렇게 동작하는거지.....

## throttleTime, auditTime, debounceTime

- throttleTime

  ![throttleTime marble diagram](https://rxjs-dev.firebaseapp.com/assets/images/marble-diagrams/throttleTime.png)

  `throttleTime(300)` 이면 300ms 간격으로 가장 먼저 들어온 스트림만 처리한다.

- debounceTime

  ![debounceTime marble diagram](https://rxjs-dev.firebaseapp.com/assets/images/marble-diagrams/debounceTime.png)

  `debounceTime(300)` 이면 스트림이 계속 들어와도 마지막 스트림이 들어온 후 300ms가 지나야지만 실행한다.

- auditTime

  ![auditTime marble diagram](https://rxjs-dev.firebaseapp.com/assets/images/marble-diagrams/auditTime.png)

  `auditTime(300)` 이면 300ms 간격으로 실행하는데 300ms 간격 중 가장 마지막에 들어온 스트림만 처리한다.

  

## Component - Service 구조

