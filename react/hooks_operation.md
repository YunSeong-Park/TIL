# React hooks의 동작 방식

[원문](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e)

## 읽고 나서

아마 본문에서 설명하는대로 array 형태로 state를 관리하지 않을 것이라는 생각이 든다. 단순히 array로 관리한다면 조건부로 렌더링되는 상황에서 error가 발생한다. 추후 기회가 된다면 React 내부 코드를 확인해보자

## 서문

hooks는 두 가지 규칙이 있습니다.

- hooks를 루프, 조건문, 중첩된 함수 안에서 호출하지 말 것
- 함수형 컴포넌트 내부에서만 호출할 것

hooks는 컴포넌트 단위로 기능을 동작시키기 때문에 두 번째 규칙은 자명해보입니다.

하지만, 첫 번째 규칙은 왜 그렇게 동작하는 지 바로 짐작하기 어렵습니다.

왜 첫 번째 규칙을 지켜야하는 지, hooks의 동작 방식을 살펴보면서 알아보겠습니다.

## React useState() 동작 방식

### state management

useState이 component 별 state를 관리를 Array를 통해 한다고 가정합니다. 실제 React 내부 구현은 이와 다를 수 있습니다.

### execution context

`useState()`는 특정 component를 렌더링하기 위한 execution context 내에서 동작합니다. 이 말은 data가 component 외부에 저장됐다는 의미입니다. 이 data는 다른 component와 공유되지는 않지만, component의 자식들의 후속 렌더링에서 접속 할 수 있는 범위 입니다.

### 상황 별 동작

1. Initialisation  
   빈 배열 `setters` 와 `state`를 생성합니다.
2. First render
   함수형 component가 최초로 실행될 때입니다.

   각 `useState()` 호출은 `setters`와 `state`에 대응 되는 값을 push 합니다.

3. Subsequent render
   후속 렌더링 시 해당 값을 배열에서 읽어 옵니다.
4. Event handling
   setter가 호출될 때 그에 대응되는 state의 값을 변경합니다.

### 구현

```ts
let state = [];
let setters = [];
let firstRun = true;
let cursor = 0;

function createSetter(cursor) {
  return function setterWithCursor(newVal) {
    state[cursor] = newVal;
  };
}

// This is the pseudocode for the useState helper
export function useState(initVal) {
  if (firstRun) {
    state.push(initVal);
    setters.push(createSetter(cursor));
    firstRun = false;
  }

  const setter = setters[cursor];
  const value = state[cursor];

  cursor++;
  return [value, setter];
}

// Our component code that uses hooks
function RenderFunctionComponent() {
  const [firstName, setFirstName] = useState("Rudi"); // cursor: 0
  const [lastName, setLastName] = useState("Yardley"); // cursor: 1

  return (
    <div>
      <Button onClick={() => setFirstName("Richard")}>Richard</Button>
      <Button onClick={() => setFirstName("Fred")}>Fred</Button>
    </div>
  );
}

// This is sort of simulating Reacts rendering cycle
function MyComponent() {
  cursor = 0; // resetting the cursor
  return <RenderFunctionComponent />; // render
}

console.log(state); // Pre-render: []
MyComponent();
console.log(state); // First-render: ['Rudi', 'Yardley']
MyComponent();
console.log(state); // Subsequent-render: ['Rudi', 'Yardley']

// click the 'Fred' button

console.log(state); // After-click: ['Fred', 'Yardley']
```

### loop, 조건문 내부에 넣지 말아야하는 이유

컴포넌트가 조건부로 hooks를 호출하면 내부 저장된 state와 component의 매칭이 흐트러 진다.
