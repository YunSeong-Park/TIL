# prototype-based programming

`prototype-based programming`은 기존 객체(prototype)을 재사용하는 객체지향 프로그래밍 스타일이다.

clone 및 extends 할 수 있는 일반화된 객체를 사용합니다.

예를들면, `fruit` 객체는 과일의 속성과 기능을 일반화 시킨 객체입니다. `banana` 는 `fruit` 객체에서 복제되고 특정한 속성이 추가된 것입니다.

각 `banana` 객체는 `banana`에서 복제됩니다.

## Design and implementation

```
prototype 객체를 생성하고, 인스턴스를 합니다. JavaScript에서 객체는 수정가능하기 때문에 인스턴스에 새로운 method와 field를 추가할 수 있습니다.
그렇게 수정된 인스턴스는 또 새로는 객체의 prototype이 될 수 있습니다. 유사한 객체를 많이 만들기 위해 클래스가 필요하지 않습니다.

- Douglas Crockford
```

prototype 지지자들은 prototype-based programming이 다음과 같은 장점이 있다고 주장합니다.

- 프로그래머들을 각 객체들의 다른 동작에 집중할 수 있게 한다.
- class와 같은 원형 객체로 분리는 나중에 고려할 수 있게 한다.

많은 prototype 기반 언어들은 런타임 동안 prototype 변경을 권장하는 반면, 클래스 기반 객체 지향 언어들은 런타임 동안 객체 구조 변경을 지원하지 않습니다.

거의 모든 prototype 기반 시스템은 동적 타입을 기반으로 합니다. 정적인 타입이면서 prototype 기반인 시스템은 Omega가 있습니다.

## Object construction

prototype 기반 언어에는 명시적인 class가 없습니다. 객체는 prototype 객체의 property를 통해 직접 상속합니다. 그 javaScript에서 prototype property는 `prototype`이라고 합니다.

새로운 객체를 생성하는 방법은 두 가지 입니다.

1. 무에서 새로운 객체를 만든다.
2. 기존 객체를 복사한다.

첫 번째 방법은 object literal로 많이 지원됩니다.

클래스 기반 언어에서 새 인스턴스는 클래스의 constructor 함수를 통해 생성됩니다.

많은 prototype 언어들은 모든 객체들의 root가되는 객체를 가지고 있습니다.

clone은 prototype 객체의 동작을 복사하여 새 객체를 만드는 것을 이야기 합니다.

prototype객체와 clone 객체는 link로 연결되어 있고 prototype이 객체가 변경되면 clone 객체도 이에 영향을 받습니다.

```js
const foo = { one: 1, two: 2 };

// bar.[[prototype]] = foo
const bar = Object.create(foo);

bar.three = 3;

bar.one; // 1
bar.two; // 2
bar.three; // 3
```

## Delegation

delegation을 이용하는 prototype 기반 언어는 delegation pointer를 따라가면서 method를 디스패치하거나 올바른 데이터를 찾습니다.

## Criticism

class 지지자들은 prototype을 다음과 같은 이유에서 비판하는데 대부분 다음과 같은 요소가 포함돼있습니다.

- 정확성
- 안전성
- 예측 가능성
- 효율성
- 프로그래머의 익숙함

위에 있는 3가지 요소는 동적 타입이 가지는 단점과 유사합니다.

효율성의 경우는 클래스를 선언하면 클래스는 컴파일러 최적화가 단순화 됩니다.

https://en.wikipedia.org/wiki/Prototype-based_programming
