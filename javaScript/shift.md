# 시프트 연산

## floor

자바스크립트는 숫자를 64bit float으로 저장하지만, shift 연산을 지원한다. 이 과정에서 64bit를 32bit 정수로 변환하기 때문에 다음과 같은 핵이 존재한다.

```ts
133.44 >> 0; // 133
```

`Math.floor()` 와 비교하면 매우 간단해서 매력적이지만,
js의 공식적인 API인 Math.floor를 아닌 해당 코드를 사용하면, 다른 사람이 읽기 어려운 코드가 될 수도 있겠다.