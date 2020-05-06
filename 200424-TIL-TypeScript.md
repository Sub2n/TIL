## TypeScript

### Class

TypeScript가 지원하는 클래스는 ES6의 클래스와 상당히 유사하지만 몇 가지 TypeScript만의 고유한 확장 기능을 가진다.

#### 1. 클래스 정의

ES6 클래스는 클래스 body에 메소드만 포함할 수 있다. 즉, 클래스 property는 body가 아닌 constructor 내부에서만 선언하고 초기화한다.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  
  walk() {
    console.log(`${this.name} is walking`);
  }
}
```

위 코드를 TypeScript로 변경하면 컴파일 에러가 발생한다.

```bash
Property 'name' does not exist on type 'Person'
```

TypeScript 클래스는 **클래스 body에 클래스 property를 사전에 선언**해야 한다.

```typescript
class Person {
  name: string;
  
  constructor(name: string) {
    this.name = name;
  }
}
```

### 2. 접근 제한자

TypeScript 클래스는 클래스 기반 객체지향 언어가 지원하는 접근 제한자(Access modifier) public, private, protected를 지원한다.

단, 접근 제한자를 명시하지 않을 경우 private이 아닌 public으로 암묵적 선언된다.

| 접근 가능성      | public | protected | private |
| ---------------- | :----: | :-------: | :-----: |
| 클래스 내부      |   O    |     O     |    O    |
| 자식 클래스 내부 |   O    |     O     |    X    |
| 클래스 인스턴스  |   O    |     X     |    X    |

### 3. 생성자 파라미터에 접근 제한자 선언

생성자의 파라미터에도 접근 제한자를 선언할 수 있다. 이 때 **접근 제한자가 사용된 생성자 파라미터는 암묵적으로 클래스 property로 선언**되고, **생성자 내부에서 별도의 초기화가 없어도 암묵적으로 초기화가 수행**된다.

생성자 파라미터에 접근 제한자를 선언하지 않을 경우 생성자 파라미터는 생성자 내부의 지역 변수가 되어 외부에서 참조가 불가능하다!

#### 4. readonly 키워드

TypeScript의 readonly 키워드를 사용하여 선언한 클래스 property는 선언 시 또는 생성자 내부에서만 값을 할당할 수 있다. 이를 이용해 상수의 선언에 사용한다.

```typescript
class Foo {
  private readonly MAX_LEN: number = 5;
  private readonly MSG = string;
  
  constructor() {
    this.MSG = 'hello';
  }
}
```

### 4. static 키워드

ES6 클래스에서 static 키워드는 클래스의 정적 메소드(static method)를 정의한다. 정적 메소드는 클래스 인스턴스가 아닌 클래스 이름으로 호출한다. TypeScript도 똑같다.

### 5. 추상 클래스 (Abstract Class)

추상 클래스는 하나 이상의 추상 메소드를 포함하며 일반 메소드도 포함할 수 있다. 추상 메소드는 내용 없이 메소드 interface(이름과 타입)만 선언된 메소드를 말하며, 선언 시 `abstract` 키워드를 사용한다.

추상 클래스도 abstract 키워드로 선언하는데 직접 인스턴스를 생성할 수 없고 상속만을 위해 사용된다. 추상 클래스를 상속한 클래스는 추상 클래스의 추상 메소드를 반드시 구현해야 한다.

```typescript
// 추상 클래스
abstract class Animal {
  // 추상 메소드
  abstract makeSound(): void;
  
  move(): void {
   	console.log('moving');
  }
}

class Dog extends Animal {
  // 추상 클래스를 상속한 클래스는 추상 클래스의 추상 메소드를 반드시 구현
  makeSound() {
    console.log('boww~~~')
  }
}
```



**interface는 모든 메소드가 추상 메소드이지만, 추상 클래스는 하나 이상의 추상 메소드와 일반 메소드를 포함할 수 있다.**

### 6. 인터페이스 (Interface)

인터페이스는 일반적으로 **타입 체크를 위해 사용**되며 **변수, 함수, 클래스에 사용**할 수 있다. 인터페이스는 여러가지 자료형을 갖는 프로퍼티로 이루어진 새로운 자료형을 정의하는 것이다. **인터페이스에 선언된 프로퍼티 또는 메소드의 구현을 강제하여 일관성을 유지**하도록 한다. 인터페이스는 ES6은 지원하지 않고 TypeScript에서만 지원한다.

인터페이스는 프로퍼티와 메소드를 가질 수 있다는 점에서 클래스와 유사하지만 직접 인스턴스를 생성할 수 없고, **모든 메소드가 추상 메소드**이다. 그러나 abstract 키워드는 사용하지 않는다.

#### 6.1. 변수와 인터페이스

interface는 변수의 타입으로 사용할 수 있다. 이 때 변수는 interface에 정의된 타입을 구현해야한다.

```typescript
interface Todo {
  id: number;
  content: string;
  completed: boolean;
}
```



#### 6.2. 함수와 인터페이스

interface는 함수의 타입으로 사용할 수 있다. 함수의 interface는 파라미터 리스트와 리턴 타입을 정의한다.

```typescript
interface iFunc {
  (num: number): number;
}

const func: iFunc = function (num: number) {
  return num * num;
}
```

#### 6.3. 클래스와 인터페이스

클래스 선언문의 implements 뒤에 interface를 선언하면 해당 클래스는 지정된 interface를 반드시 구현해야 한다. interface는 프로퍼티와 메소드를 갖지만 클래스처럼 인스턴스를 생성할 수는 없다!

```typescript
interface IIodo {
  id: number;
  content: string;
  complement: boolean;
}

class Todo implements ITodo {
  constructor (
    public id: number,
    public content: string,
    public completed: boolean
  ) {}
}
```

interface는 프로퍼티 뿐만 아니라 메소드도 포함할 수 있다 단, 모든 메소드는 추상 메소드여야 한다.

### 6.4. Duck Typing

interface를 구현했다는 것이 타입 체크를 통과하는 유일한 방법은 아니다. 타입 체크에서 중요한 건 값을 실제로 가지고 있는 것이기 때문에 interface를 implements 하지 않았어도 해당 interface에서 정의한 프로퍼티나 메소드를 가지고 있다면 그 interface를 구현한 것으로 인정한다.

이를 Duck Typing 또는 Structural Typing이라 한다.

### 7. 제네릭 Generic

```typescript
class Queue<T> {
  protected data : Array<T> = [];
  
  push(item: T) {
    this.data.push(item);
  }
  
  pop(): T {
    return this.data.shift();
  }
}

const numberQueue = new Queue<number>();
```

같은 구조를 가진 클래스가 다양한 타입을 지원해야할 경우 타입 별로 클래스를 상속 받지 않고 제네릭을 이용하면 된다.

**제네릭은 선언 시점이 아니라 생성 시점에 타입을 명시하여 하나의 타입만이 아닌 다양한 타입을 사용할 수 있도록 하는 기법**이다. 한 번의 선언으로 다양한 타입에 재사용이 가능하다는 장점이 잇다.

T는 제네릭을 선언할 때 관용적으로 사용되는 식별자로 타입 파라미터(Type parameter)라고 한다.

```typescript
function reverse<T>(items: T[]): T[] {
  return items.reverse();
}
```

