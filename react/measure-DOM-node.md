# Measure DOM node

[출처](https://reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)

React 컴포넌트 내에서 DOM node의 width, height를 측정하려면 어떻게 해야할까?

아래와 같은 함수를 ref에 전달해주면된다.

```tsx
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = useCallback((node) => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  );
}
```

이를 통해 유추해보면 DOM node가 변경될 때마다 전달된 ref의 함수를 호출한다. 단 useRef는 호출돼도 컴포넌트를 render하지 않을 뿐이다.
