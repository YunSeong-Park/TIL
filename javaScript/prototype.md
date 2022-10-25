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

https://en.wikipedia.org/wiki/Class-based_programming
