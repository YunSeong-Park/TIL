# 리액트와 선언형(Declarative)

## 소개

리액트와 선언형에 대해 개인적으로 정리한 문서

## 개요

> 어떻게 해야하는지 보다 무엇과 같은지 설명하는 프로그램을 선언형 프로그램이라고 한다.

선언적 프로그램의 반의어는 명령형(imperative)프로그램이다. 이 둘의 차이는 무엇일까? 아래 예제를 보면서 확인해보자

### 예제: UI 생성 시

```jsx
// Hello, World! 화면에 출력하기
// 순수 javaScript 명령형 코드
const root = document.getElementById('root');
const header = document.createElement('h1');
const headerContent = document.createTextNode(
	'Hello, World!'
);

header.appendChild(headerContent);
root.appendChild(header);

// React 코드 (선언적인)
const header = <h1>Hello, World!</h2>; // jsx
ReactDOM.render(header, document.getElementById('root'));
```

명령형으로 작성된 코드의 경우 Hello, World!를 출력하기 위해 컴퓨터가 수행하는 절차를 일일히 코드로 작성해주어야 한다.

반면, React 코드의 경우 내가 UI을 선언하고 render 함수를 호출하면 React가 알아서 절차를 수행해 화면에 출력된다. 즉, 화면에 `어떻게` 그려야할지는 React 내부에 숨겨져 잘 추상화되어 있다.

이 덕분에 다음과 같은 이점을 얻는다.

1. 무엇을 보여줄지에 집중할 수 있다.
2. 코드가 간결해진다.

### 예제: UI 수정 시

```jsx
// Hello World!를 Hello로 변경하고 싶다면?

// 순수 javaScript 명령형 코드
const header = document.getElementById('header') // id가 존재한다고 가정
header.textContent = 'Hello';

// React 코드 (선언적인)
const header = <h1>Hello</h2>; // jsx
ReactDOM.render(header, document.getElementById('root'));
```

명령형 코드는 기존 렌더링된 UI에서 일부 속성을 계속 수정한다. 따라서 동일한 Element를 수정하는 Event가 많을 수록 화면이 어떻게 그려질지 예측하기 어려워진다.

반면, React의 경우 어떻게 변경해야하는 신경 쓸 필요 없이 새로 보여줄 결과 ReactElement 를 선언하고 render 하면된다.

하지만, 새로운 결과를 선언하고 render한다고 해서 기존 element가 완전히 제거되고 새로운 element가 이를 대체한다면, 다음과 같은 문제가 발생한다.

- DOM조작은 큰 비용을 수반하기 때문에 어플리케이션의 퍼포먼스가 저하될 수 있음
- 화면 깜빡임
- 스크롤과 같은 element 내부 상태가 없어짐

⇒ React는 이런 문제를 해결하기 위해 **Virtual DOM** 과 **reconciliation**이라는 개념을 도입했다.

### 정리

React는 선언적으로 UI를 구성하고 업데이트 할 수 있도록 도와주는 라이브러리이다.

선언적이라는 특성으로 다음과 같은 이점을 달성한다.

1. 코드가 간결해진다.
2. 무엇을 보여줄지에 집중할 수 있다.
3. 결과를 예측하기 쉬워진다.

하지만 UI 라이브러리/프레임워크에서 React가 선언적이라는 특징을 독점하는 것은 아니다. 많은 라이브러리가 선언적 프로그래밍을 지향하고, 이를 달성하기 각자의 해결책을 도입하는 것으로 보인다. (React: Virtual DOM/Reconciliation, [Svelte](https://svelte-workshop.netlify.app/what-is-svelte/): Compiler-centric 등)
