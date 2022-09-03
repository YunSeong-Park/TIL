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
