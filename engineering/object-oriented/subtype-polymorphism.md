# 객체지향에서 서브타입 다형성

## 개요

다형성이란 프로그램 언어의 각 요소들(상수, 변수, 식, 객체, 메소드)가 다양한 type에 속하는 것이 허가되는 성질을 가리킨다.

다음과 같은 종류가 있다.

- Ad hoc polymorphism: (overloading)
- Parametric polymorphism: (generic)
- Subtype polymorphism

흔히 객체지향을 논할 때 나오는 다형성은 subtype polymorphism에서 비롯된 개념으로 보인다.

이 문서에서 객체지향에서 subtype polymorphism에 대해서 정리해보고자 한다.

## 개념

subtype polymorphism은 `상위타입(supertype)의 함수가 하위타입(subtype)에서도 존재하는 것`이다.

### 예시

아래 코드는 Supertype interface를 구현하는 Subtype class는 Supertype의 method를 모두 구현한다. 따라서 subtype polymorphism을 지킨다고 볼 수 있다.

```ts
interface Supertype {
  run: () => void;
}

class Subtype implements Supertype {
  constructor() {}
  run() {
    console.log("run");
  }
}
```

아래 코드는 subtype polymorphism을 지킨다고 볼 수 있을까?

```ts
const normal = {
  console: () => console.log("normal"),
};

const special = {
  console: () => console.log("special"),
};
```

아래 조건을 만족한다면, subtype polymorphism을 지켰다고 생각한다.

- 코드 작성자가 console이라는 메시지를 처리하는 Logger라는 역할을 고려했다.
- typeScript의 duck typing이라는 특징을 이용해 이를 생략했을 뿐이다.

```ts
interface Logger {
  console: () => void;
}
const normal: Logger = {
  console: () => console.log("normal"),
};

const special: Logger = {
  console: () => console.log("special"),
};
```

## 의미

객체지향에서 이러한 subtype polymorphism을 도입한 이유는 뭘까? 나는 `동일한 메시지를 수신한 객체의 타입에 따라 다르게 수행하기 위해` 생각한다.

예시에서 자세히 살펴보자.

### 예시

Animal interface를 구현하는 Human, Dog은 모두 `move()`라는 메소드를 가지고 있고, 호출할 때 객체의 타입을 신경 쓰지 않아도 된다.

```ts
interface Animal {
  move: () => void;
}

class Human implements Animal {
  constructor() {}
  move() {
    console.log("human move");
  }
}

class Dog implements Animal {
  constructor() {}
  move() {
    console.log("dog move");
  }
}

const animals: Animal[] = [human, dog];

animals.forEach((animal) => {
  animal.move();
});
```

이를 기반으로 객체 지향의 핵심 개념인 책임, 역할, 메세지의 개념이 정립된다.

- 책임: 각 클래스가 구현해야하는 기능들

  ex) Human, Dog class의 `move()`메소드

- 역할: 책임들을 추상화한 것

  ex) Animal

- 메세지: 역할이 가진 기능을 수행하도록 요청하는 것

  ex) animal에 move 라는 메시지를 보내면 실제 구현체의 `move()` 메소드를 호출한다.

## 조건

`동일한 메시지를 수신한 객체의 타입에 따라 다르게 수행하기 위해`를 알맞게 적용하려면 다음과 같은 조건을 만족해야한다.

1.  대체가능성(substitution)
2.  내적일관성(internal identity)

### 대체가능성(substitution)

subtype 객체는 supertype으로 대체 가능하다.

```ts
const Worker = class {
  run() {
    console.log("working");
  }
  print() {
    this.run();
  }
};

// Overriding
const HardWorker = class extends Worker {
  run() {
    console.log("HardWorking");
  }
};

const worker: Worker = new HardWorker();

console.log(worker instanceof Worker); // true. 대체가능성
```

### 내적일관성(internal identity)

supertype으로 대체되더라도 생성 시 원본 객체를 유지한다.

```ts
worker.print(); // HardWorker의 print. 내적일관성
```
