# 그리디 알고리즘

- 그 순간 최적(locally optimal)의 해법을 찾는 방법.
  - 다음 단계를 고려하지 않음.
- 최종적으로 최적(globally optimal)의 결과가 아닐 수도 있음.
- 근사(approximation) 알고리즘
  - 확실한 결과 값을 얻을 수 있는 알고리즘이 아니다!
  - 하지만, 성능 향상을 위해서 사용할 수 있음.

## 장점

- 충분히 훌륭한 결정을 빨리 내릴 수 있음.
  => 보통 랜덤한 선택보다 나음

## 사용하기 적합한 경우

- 제대로된 해법을 구하는 알고리듬의 복잡도가 너무 높은 경우
- 동적 계획법을 사용할 수 없는 경우
  - 중복되는 하위 문제가 없는 경우

## 팁

- 최소/최대화 문제
- 정렬을 해야 속도가 빨라질 수도 있음.

## 예시 동전 교환 문제

- 아래는 순간 최적만으로 최종적으로 최적의 결과가 나오는 문제
- 10, 50, 100, 500원의 동전으로 거스름돈을 줄 때 가장 적은 동전 수로 거스름돈을 주는 법

=> 거스름 돈 보다 작으면서 가장 큰 동전을 반복해서 준다.

하지만, 동전이 1, 5, 7, 10이라면 최적의 결과가 나오지 않음.

## 그리디 접근법으로 풀 수 있는 문제

- 인터벌 파티셔닝
- 지연 시간 최소화
- 다익스트라의 최단 경로
- 운영체제의 job 스케줄링
- k-센터 문제
- 결정 트리 학습법
- 허프만 코딩
- 그 외 다수
