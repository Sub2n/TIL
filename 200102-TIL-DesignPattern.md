## Today I Learned

자바스크립트 개발에 사용되는 프레임워크가 주로 사용하는 아키텍처는 다음과 같다.

이런 패턴의 기본 원칙은 View를 구성하는 로직과 Data를 처리하는 로직을 분리하는 것이다.

#### MVW (Model - View - Whatever)

Whatever란 말그대로 아무 거나 될 수 있다. 따라서 MVW는 MV*로 MVC, MVP, MVVM 패턴 들을 포함하는 개념이다.

![MVC vs MVP vs MVVM](https://blog.xenomity.com/assets/images/2014-03-19-image_4.png)

### MVC (Model - View - Controller)

가장 유명한 패턴으로 Model, View, Controller 3가지 요소로 구성된다.

- Model: 애플리케이션에서 사용하는 데이터를 관리하는 영역
- View: 사용자에게 보이는 부분
- Controller: 사용자의 입력을 받아 모델 데이터를 조회/수정하고 변경 사항을 뷰에 반영

MVC 패턴은 Model과 View 사이에 강한 결합(Coupling)이 있어 서로 의존적이라는 단점이 있다. 이들의 의존성을 줄이기 위해서 나온 패턴이 MVP이다.

### MVP(Model-View-Presenter)

MVP는 Controller 대신 Presenter가 존재한다.

* Presenter: 뷰에서 정보를 요청하면 모델 데이터를 조회/수정해서 뷰로 전달

Model과 View 사이에 Presenter를 넣어 Model-View 사이의 의존성이 없어졌으나 Presenter와 View 가 강한 의존성을 갖게 된다. 이를 보완한 패턴이 MVVM이다.

### MVVM (Model - View - ViewModel)

View Model이라는 새로운 개념이 등장한다.

* View Model: 뷰를 표현하기 위해서 만들어진 모델

MVVM은 Command Pattern과 Data Binding 2가지 디자인 패턴을 이용한다.

뷰에서 입력이 들어오면 Command 패턴으로 뷰모델에 명령을 내린다.

뷰모델은 모델에 필요 데이터를 요청하고 응답 받은 데이터를 가공해 저장한다.

뷰와 뷰모델 간의 Data Binding으로 인해 데이터의 변경에 따라 뷰가 변경된다.

### React

데이터 변경 사항을 반영하는 과정에서 뷰는 변형(mutate)된다. 애플리케이션 규모가 크면 데이터 변화를 감지하고 뷰를 변형하는 부분에서 성능이 떨어질 수 있다. 리액트는 최대한 성능을 아끼고 편안한 사용자 경험을 제공하면서 구현하고자 개발되었다.

