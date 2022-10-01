# 데이터 압축 알고리듬

    원본 데이터보다 적은 비트 수로 정보를 표현하는 방법

목표

- 저장공간 절약
- 전송속도 단축

## 압축 알고리듬의 분류

### 손실

- 원본 데이터와 비슷하게 복구
- 비교적 압축 파일 크기가 작음
- 종류
  - JPEG
  - MP3

### 무손실

- 원본 데이터와 비슷하게만 복구
- 비교적 압축파일 크기가 작음
- 종류
  - ZIP
  - RLE

## 양자화(quantization)

- 원본에서 비슷한 값들을 합쳐서 개수를 줄이는 방법

- 값 표현에 사용하는 비트 수를 줄일 수 있음.

- 품질 손상을 최소화할 수 있는 방법 고안이 중요
  - JPG는 주파수 데이터로 바꾼 뒤 (DCT) 양자화
  - 보간(interpolation)

## 문자열 전송하기

banana\*bab

- 위와 같은 문자열을 전송하려면?
- 총 80비트

### 팔레트를 사용한 압축

```
b = 0b00;
a = 0b01;
n = 0b10;
* = 0b11;
```

한 글자 당 2비트를 차지하기 때문에 총 20비트로 전송 가능

### 허프만 코딩(Huffman coding)

- 입력 문자들에 적합한 가변 부호(code)를 선택하는 알고리듬
- 최적 접두어 코드(optimal prefix code)