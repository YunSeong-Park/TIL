# Dynamic Programming

`특별한 속성을 가진 복잡한 문제를 푸는 방법`

특별한 속성이란?

- 단순한 하위 문제로 나뉘어져 재귀적으로 풀 수 있는 속성

## 특별한 속성을 가진 문제 예시

배낭(knapsack) 문제

한정된 배낭과 가격과 부피가 존재하는 물건들이 있을 때 배낭에 채워지는 최대한 비싼 가격은 얼마인가?

이 문제는 물건이 3가지 있는 경우 8가지 경우의 수를 확인하는 식으로 주먹구구식으로 해결할 수 있다.

## 메모이제이션(memoization)

- 계산 결과를 캐시에 저장해 둔 뒤, 나중에 재사용하는 기법
- 보통 함수가 매개변수에 따라 반환하는 값을 캐싱하는 것을 지칭

메모이제이션을 사용한 피보나치 함수(top-down)

```tsx
const fibonacci = (number: number) => {
  const cache = new Array(number).fill(0);

  return _fibonacci(number, cache);
};

const _fibonacci = (number: number, cache: number[]) => {
  if (number <= 0) {
    return number;
  }

  if (cache[number] !== 0) {
    return cache[number];
  }

  const result = _fibonacci(number - 2, cache) + _fibonacci(number - 1, cache);

  cache[number] = result;
  return result;
};
```

### 동적 계획법과 메모이제이션

- 메모이제이션: 실행된 결과를 기억해 재사용하는 최적화 기법

- 동적 계획법: 복잡한 문제를 하위 문제로 쪼개서 재귀적으로 푸는 방법

동적 계획법에서 메모이제이션을 많이 사용하기 때문에 두 단어가 혼용되기도 한다. 하지만, 다른 곳에서도 메모이제이션을 사용한다.

### top-down 동적 계획법

- 최종적으로 해결하려고하는 문제에서 시작
- 필요에 따라 재귀적으로 하위 문제를 풂
  - 하위 문제를 평가하는 최적의 순서를 알 필요 없음.
- 기존 재귀 함수에서 캐시 로직을 추가하는 것으로 구현 가능

## 타뷸레이션

- fib(6)을 구하려면 fib(5), fib(4)가 필요
- fib(5)을 구하려면 fib(4), fib(3)이 필요
  ...
- fib(n)을 구하려면 fib(n-1), fib(n-2)가 필요 (n > 2)

위 규칙을 통해 최적화된 순서대로 값을 구하면 캐시를 따로 할당하지 않고 값을 구할 수 있음.

bottom-up 피보나치 수열

```ts
const fibonacci = (number: number) => {
  const cache: number[] = new Array(number + 1).fill(null);

  cache[0] = 0;
  cache[1] = 1;

  for (let i = 0; i <= number; ++i) {
    cache[i] = cache[i - 2] + cache[i - 2];
  }

  return cache[number];
};
```

### bottom-up 동적 계획법

- 가장 작은 문제(리프) 부터 시작
- 순서대로 그보다 하나 더 큰 문제를 풀어나감
- top-down 방식보다 보통 더 빠름
  - CPU 캐시에 좀 더 친화적
  - 재귀 함수 호출을 피할 수 있음
  - 모든 하위 문제를 평가할 필요가 없는 경우에는 예외

## 동적 계획법을 적용할 수 있는 문제의 특징

1. 최적 부분 구조(optimal substructure)

- 하위 문제의 최적 해법으로부터 큰 문제의 최적 해법을 구할 수 있음.
- 예시: 최단 경로 찾기

2. 하위 문제의 반복

- 똑같은 평가를 반복해야 함
- 하위 문제의 크기가 작아야 함

### 분할 정복 vs 동적 계획법

분할 정복은 반복되지 않는 하위 문제, 동적 계획법은 반복되는 하위 문제

## 동적 계획법으로 문제를 푸는 과정

1. 문제에 동적 계획법을 사용할 수 있는지 판단
2. 상태와 매개 변수를 결정
3. 상태 간의 관계를 정립
4. 종료조건을 결정
5. 메모이제이션 혹은 타뷸레이션을 추가

### 동적 계획법을 적용 가능한 문제 판단하기

- 어떤 제약 하에 값을 최적화 (최대/ 최소)
- 그리드로 표현해보기

예시)

- 최단 경로 찾기 (다익스트라 알고리듬)
- 최장 공통부분 문자열
- 와일드카드 패턴 매칭
- 부분집합 합
- 레벤슈타인 거리 (편집 거리)
- 연속 행렬 곱셈
- 등

### 코드

배낭 문제

```ts
type Item = {
  space: number;
  value: number;
};
const getMaxValue = (space: number, items: Item[]) => {
  const numItems = items.length;

  const cache = new Array(numItems)
    .fill(null)
    .map((_) => new Array(space).fill(null));

  for (let s = 1; s <= space; ++s) {
    if (itmes[0].space > s) {
      continue;
    }

    cache[0][s] = items[0].value;
  }

  for (let i = 1; i < numItems; ++i) {
    for (let s = 1; s <= space; ++s) {
      if (items[i].space > s) {
        cache[i][s] = cache[i - 1][s];
        continue;
      }

      const remainingSpace = s - itmes[i].space;
      const remainingMaxValue = cache[i - 1][remainingSpace];

      const choice1 = cache[i - 1][s];
      const choice2 = items[i].value + remainingMaxValue;

      cache[i][s] = Math.max(choice1, choice2);
    }
    return cache[numItems - 1][space];
  }
};
```
