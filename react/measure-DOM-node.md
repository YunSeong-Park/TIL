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

단 ref로 전달된 함수는 컴포넌트가 마운트 되거나, 언 마운트 될때만 호출된다. 때문에 실시간으로 변경되는 style을 가져와야 하는 경우는 [ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)를 사용해야합니다.
