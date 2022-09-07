# Rendering: repaint, reflow/relayout, restyle

[출처](https://www.phpied.com/rendering-repaint-reflowrelayout-restyle/)

## 렌더링 프로세스

브라우저마다 렌더링 프로세스 방식은 다르지만, 전반적인 컨셉을 확인할 수 있다.

- 브라우저는 HTML 소스 코드를 해석하여 DOM tree를 생성합니다.
  - root node는 documentElement(`<html>` 태그)입니다.
- 브라우저는 CSS 코드를 파싱합니다.
  - 여기서 이해할 수 없는 rule을 무시합니다.
  - 스타일링 정보 cascades: 브라우저 기본 stylesheets, author stylesheets - external, imported, inline => 최종 style attributes
- 최종적으로 render tree를 구성합니다.
  - render tree는 DOM tree와 비슷하지만, 조금 다릅니다.
    렌더트리는 스타일에 대해 알고 있고, display: none을 사용하여 div를 숨기는 경우 렌더 트리에 표시되지 않습니다. head elements와 같이 눈에 보이지 않는 element 또한 표시되지 않습니다.
  - 1개의 element가 여러개의 node로 표현될 수도 있습니다.
    ex) `<p>`태그의 경우 안에 있는 모든 줄이 각각 하나의 node가 됩니다.
  - 렌더 트리의 node를 frame, or box라고 합니다.
  - 각 노드에는 width, height, border, margin 등이 있습니다.
- render tree가 생성되면, 브라우저는 render tree nodes를 스크린에 그립니다.

## 렌더링 프로세스 예시

HTML source:

```html
<html>
  <head>
    <title>Beautiful page</title>
  </head>
  <body>
    <p>Once upon a time there was a looong paragraph...</p>

    <div style="display: none">Secret message</div>

    <div><img src="..." /></div>
    ...
  </body>
</html>
```

DOM tree는 기본적으로 HTML 문서의 각 태그와 노드 사이의 각 텍스트를 하나의 node로 바라봅니다.

```
documentElement (html)
    head
        title
    body
        p
            [text node]

        div
            [text node]

        div
            img

        ...
```

render tree는 DOM tree의 시각적인 요소만 표시합니다.

```
root (RenderView)
    body
        p
            line 1
	    line 2
	    line 3
	    ...

	div
	    img

	...
```

render tree의 root node는 다른 모든 요소를 포함하는 프레임(상자)입니다. 페이지가 펼쳐질 수 있는 제한된 영역이므로 브라우저 창 내부라고 생각할 수 있습니다.
기본적으로 (0, 0) 부터 (window.innerWidth, window.innerHeight)까지의 viewport 라고 볼 수 있습니다.

화면에 정확히 무엇을 어떻게 표시할지 알아내는 것은 렌더 트리를 통한 재귀적 flow를 포함합니다.

## Repoints and refloews

페이지를 처음 로드 될때 layout 생성과 paint는 최소 한번을 수행됩니다.

그 이후 , 입력정보를 변경하면 다음과 같은 일 중 하나 혹은 두가지가 발생할 수 있습니다.

1. render tree의 일부(또는 전체 트리)의 유효성을 다시 확인하고 node 영역을 다시 계산해야 합니다. 이 과정을 reflow/ layout이라고 합니다.
2. background color와 같은 스타일 변경으로 인해 화면의 일부를 업데이트 합니다. 이를 repaint/ redraw라고 합니다.
   repaint와 reflow는 많은 비용이 들고 UI를 느리게 보이게 할 수 있습니다.

## reflow와 repaint의 트리거

render tree를 구성하는 데 사용되는 입력 정보를 변경하는 것은 reflow, refpaint를 유발할 수 있습니다.

- DOM nodes 추가, 삭제, 수정
- dispaly: none; 으로 DOM node 숨기기(reflow and repaint) or visibility: hidden(rpaint, geometry 변경이 없음)
- page 내에있는 DOM node를 움직이거나 animation을 적용하는 것
- stylesheet추가, style properties 조정
- 창 크기 조정, 글꼴 크기 변경, 스크롤

예시

```javascript
var bstyle = document.body.style; // cache

bstyle.padding = "20px"; // reflow, repaint
bstyle.border = "10px solid red"; // another reflow and a repaint

bstyle.color = "blue"; // repaint only, no dimensions changed
bstyle.backgroundColor = "#fad"; // repaint

bstyle.fontSize = "2em"; // reflow, repaint

// new DOM element - reflow, repaint
document.body.appendChild(document.createTextNode("dude!"));
```

몇몇 reflow는 더 많은 비용이 발생합니다.

아래 두 상황을 예로 들면,

- body 의 직계 자손인 node를 조작한다면, 다른 많은 node들이 유효하게 사용할 수 있습니다.
- 페이지 상단 div에 확장하는 애니메이션을 적용하고 나머지 부분을 아래로 밀어내는 경우 비용이 많이 듭니다.

## Browsers are smart(브라우저 동작 방식)

렌더트리의 변경으로 유발되는 reflows 와 repaints는 많은 비용이 들기 때문에, 브라우저는 이 비용을 최대한 줄이고자 합니다.

rendering 비용 줄이기 전략

### 1. 일을 하지 않거나 지연시키기

브라우저는 스크립트로 부터 받은 DOM 변경 사항들의 queue를 설정하고 일괄적으로 수행합니다. 이렇게 하므로써 한 번의 reflow로 여러 변경 사항을 적용할 수 있습니다.

브라우저는 대기 중인 변경 사항이 일정 수에 도달하거나 시간이 충분히 경과하면 이를 flush 합니다.

- flush: queue를 비우고 DOM 변경사항들을 일괄적으로 적용시키는 것으로 보임

하지만 즉시 flush를 일으켜서 reflow의 최적화를 막는 script가 있습니다. 이 것은 style information을 요청할 때 발생합니다.

1. offsetTop, offsetLeft, offsetWidth, offsetHeight
2. scrollTop/Left/Width/Height
3. clientTop/Left/Width/Height
4. getComputedStyle

위 요청들은 결구 ㄱ노드에 스타일 정보를 요청하는 것이고, 요청할 때마다 브라우저는 가장 최신의 값을 제공해야합니다. 그렇기 때문에 모든 변경사항을 적용하고 리플로우해야 합니다.

예를 들어, 다음과 같이 (루프에서) style을 가져와서 적용하는 것은 좋지 않습니다.

```ts
el.style.left = el.offsetLeft + 10 + "px";
```

## Repaints/ Reflow 최소화하기

repaints/reflows으로 인한 UX에 부정적인 영향을 줄이기 위한 단순한 전략은 reflow/ repaints을 줄이고 스타일 정보 요청을 줄이는 것입니다.

### 개별적으로 하나하나씩 style을 수정하지 마세요.

정적인 스타일을 관리할 떄는 className을 사용하고, 동적인 스타일을 관리할 떄는 cssText를 사용하세요.

```ts
// bad
var left = 10,
  top = 10;
el.style.left = left + "px";
el.style.top = top + "px";

// better
el.className += " theclassname";

// or when top and left are calculated dynamically...

// better
el.style.cssText += "; left: " + left + "px; top: " + top + "px;";
```

### DOM 변경 사항을 일괄적으로 처리하고 'offline'으로 처리하세요.

offline은 DOM tree위에서 실시간으로 수정사항을 적용하지 말라는 의미입니다.

- documentFragment를 사용하여 일시적인 변화를 보관하세요.
- 업데이트하려는 node를 복제하고 복사본에서 작업한 다음 원본을 업데이트된 복제로 교체하세요.
- display none으로 으로 요소를 숨김 => 100개의 변경 수행 => display 복원
- 계산된 스타일을 과도하게 요구하지마세요.계산된 결과 값으로 작업해야하는 경우는 한 번 가져와서 로컬 var에 캐시하고 로컬 복사본으로 작업하세요.

```ts
// no-no!
for (big; loop; here) {
  el.style.left = el.offsetLeft + 10 + "px";
  el.style.top = el.offsetTop + 10 + "px";
}

// better
var left = el.offsetLeft,
  top = el.offsetTop;
esty = el.style;
for (big; loop; here) {
  left += 10;
  top += 10;
  esty.left = left + "px";
  esty.top = top + "px";
}
```

- 대부분의 경우, render tree를 변경하고 재검증이 필요한 부분에 대해 생각해야합니다. 예를 들면 body의 자식 element에 absolute positioning 사용한다고 하면, reflow 시 다른 대부분의 node의 영향을 받지 않을 것입니다.
