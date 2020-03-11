# OOP 와 FP

OOP(Object Oriented Programming : 객체 지향 프로그래밍)와 FP(Functional Programming : 함수형 프로그래밍)의 차이점에 대해서 알아본다.

프로그래밍 패러다임에는 명령형 프로그래밍과 선언형 프로그래밍이 있다.

#### 명령형 프로그래밍

프로그래밍의 상태와 상태를 변경시키는 구문의 관점에서 연산을 설명하는 방식. 알고리즘을 명시 O 목표 명시 X

- 절차지향 프로그래밍: 수행되어야 할 연속적인 계산 과정을 표함하는 방식 (C, C++)
- 객체지향 프로그래밍: 객체들의 집합으로 프로그램의 상호작용을 표현 (C++, Java, C#)

#### 선언형 프로그래밍

어떤 방법으로 해야하는지(How)를 나타내기보다 무엇(What)을 하는지를 설명하는 방식. 알고리즘 명시 X 목표 명시 O

- 함수형 프로그래밍: 순수 함수(Pure Function)를 조합하여 소픝웨어를 만든느 방식(Closure, Haskell, Lisp)

#### 명령형 vs 선언형 자바스크립트로 비교

```react
// 명령형
function double (arr) {
  let results = []
  for (let i = 0; i < arr.length; i++){
    results.push(arr[i] * 2)
  }
  return results
}

function add (arr) {
  let result = 0
  for (let i = 0; i < arr.length; i++){
    result += arr[i]
  }
  return result
}

$("#btn").click(function() {
  $(this).toggleClass("highlight")
  $(this).text() === 'Add Highlight'
    ? $(this).text('Remove Highlight')
    : $(this).text('Add Highlight')
})


// 선언형
function double (arr) {
  return arr.map((item) => item * 2)
}

function add (arr) {
  return arr.reduce((prev, current) => prev + current, 0)
}

<Btn
  onToggleHighlight={this.handleToggleHighlight}
  highlight={this.state.highlight}>
    {this.state.buttonText}
</Btn>
```

"명령형 프로그래밍은 어떻게 할 것인가(How)를 표현하고, 선언형 프로그래밍은 무엇을 할 것인가(What) 표현한다."

|                      | 명령형 프로그래밍        | 함수형 프로그래밍           |
| -------------------- | ------------------------ | --------------------------- |
| 프로그램이란?        | 프로그램은 명령의 수행   | 프로그램은 함수의 계산      |
| 중점적 시각          | 어떻게(how to)에 초첨    | 무엇(what)에 초점           |
| 이론적 배경          | 튜링 머신                | 람다 계산식                 |
| 주요 프로그래밍 언어 | C, Java 등 대부분의 언어 | Schema, Haskell, ML, Erlang |

함수형 프로그래밍에 필요한 개념으로는 1급 객체(First class object), 고차 함수(High-order function), 불변성(Immutablility) 유지, 순수 함수(Pure function) 등이 있다.

함수형 프로그래밍 패러다임을 더 잘 이해하기 위해서는 책을 좀 읽어봐야할 것 같다.

