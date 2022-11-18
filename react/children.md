# React children 동작 방식

## 개요

Context API 를 사용하는 컴포넌트가 두 번씩 렌더링 되는 현상을 발견했고 원인을 분석해 보았다.

```ts
// 문제 상황
 // setValue 호출하면 ChildrenComponent 전체가 렌더링됨
const FC = () => {
  const [value, setValue] = useState();
  return (
    <Context.Provider value={{value, setValue}}>
      <ChildrenComponent />
    </Context.Provider>
  );
};

// 정상 상황
// setValue를 호출해도 Context를 사용하는 컴포넌트만 렌더링됨
const FC = () => {
  return (
    <ProviderComponent>
      <ChildrenComponent>
    </ProviderComponent>
  )
}

const ProviderComponent = ({children}) => {
  const [value, setValue] = useState();
  return (
    <Context.Provider value={{value, setValue}}>
      {children}
    </Context.Provider>
  );
}

```

## 왜 이렇게 동작할까?

두 상황 모두 `ChildrenComponent`의 부모 컴포넌트가 리렌더 되었지만, 문제 상황에서는 `ChildrenComponent`가 리렌더 되었고 정상 상황은 그렇지 않았다.

이 상황을 이해하기 위해서는 컴포넌트를 사용 시 실제로 어떻게 동작하는지 확인이 필요하다.

Provider 컴포넌트를 babel로 변환한 코드는 다음과 같다.

```ts
const ProviderComponent = ({ children }) => {
  const [value, setValue] = useState();
  return React.createElement(
    Context.Provider,
    {
      value: {
        value,
        setValue,
      },
    },
    children
  );
};
```

children이라는 변수에 내부 컴포넌트 정보가 한 차례 래핑되어 전달된다. 따라서 setValue가 호출되어도 동일한 참고값을 들고 있는 children은 리렌더링하지 않는다.

그리고 이는 매우 이상적인 동작이다. children 으로 전달되는 ReactElement들은 부모 컴포넌트들과 의존성이 없다. 따라서 부모 컴포넌트가 렌더링에 영향을 받지 않는 것이 더 낫다.
