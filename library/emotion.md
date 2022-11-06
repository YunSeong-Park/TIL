# Emotion

## specificity issues

> It has predictable composition to avoid specificity issues with CSS.

emotion github에 따르면 emotion은 specificity issues를 피하고 `예측하기 쉬운 컴포넌트`를 구성하도록 돕는다고 한다.

여기서 specificity issues는 고유 명사로 css의 `명확도 문제`를 말한다.

아래의 wrapper 는 background color로 어떤 색상이 적용될까?

```tsx
<div id="wrapper" className="bg__black bg__blue">
  ...
</div>
```

어떤 색상이 적용될지 알 수 없다.

css에 rule이 충돌되는 경우 어떤 rule이 더 명확한지 우선순위를 결정하여 적용한다.

위 경우에서 css는 나중에 선언된 class의 rule이 우선 순위를 부여하기 때문에 bg**black과 bg**blue 중 나중에 선언된 class의 색상을 적용할 것이다.

그 결과 css 선언을 따로 확인하지 않으면, 위 컴포넌트 코드는 어떻게 화면에 그려질지 `예측할 수 없는 코드`가 된다.

## emotion 사용

그렇다면 emotion은 어떻게 `예측하기 쉬운 컴포넌트` 를 구성할 수 있게 할까?

emotion 은 css in js 로 style을 js 객체로 생성, 관리한다.

```tsx
import { css } from "@emotion/react";

const bgBlackStyle = css`
  background-color: black;
`;

const bgBlueStyle = css`
  background-color: blue;
`;
```

아래와 같이 babel 설정하면 className을 props로 받을 수 있는 element나 Component는 css props을 받을 수 있다.

```tsx
{
  "presets": ["@emotion/babel-preset-css-prop"]
}
```

css props에 위에서 생성한 emotion style 객체를 전달하면 emotion은 전달된 style을 평가하고 계산해서 className props에 적용합니다.

아래 wrapper 는 background-color: black 이 적용됩니다.

```tsx
<div id="wrapper" css={bgBlackStyle}>
  ...
</div>
```

아래 wrapper는 background-color: blue 가 적용됩니다.

```tsx
<div id="wrapper" css={[bgBlackStyle, bgBlueStyle]}>
  ...
</div>
```

emotion은 전달된 style을 평가할 때, rule이 중복되는 경우 뒤에 전달된 style의 rule을 우선시 합니다. 이러한 평가 과정을 통해 CSS의 `명확도 문제를 해결`하고, 위 컴포넌트 코드만 보아도 어떤 style이 우선적으로 적용될 것인지 `예측 가능`해집니다.

## emotion 활용

emotion은 style을 평가해서 생성된 className을 props로 넘기기 때문에 여러 style을 composition해서 유연하게 style을 적용할 수 있다.

```tsx
const style = css`
	...
`;
const themes = {
  primary: css`
    background: #20c997;
		...
  `,
  secondary: css`
    background: #e9ecef;
		...
  `,
  tertiary: css`
    background: none;
		...
  `,
};

const sizes = {
  small: css`
    height: 1.75rem;
    font-size: 0.75rem;
    padding: 0 0.875rem;
  `,
  medium: css`
    height: 2.5rem;
    font-size: 1rem;
    padding: 0 1rem;
  `,
  big: css`
    height: 3rem;
    font-size: 1.125rem;
    padding: 0 1.5rem;
  `,
};

const Button = ({ children, theme, size, onClick }) => {
  return (
    <button css={[style, themes[theme], sizes[size]]} onClick={onClick}>
      {children}
    </button>
  );
};
```

참고:

[https://github.com/emotion-js/emotion](https://github.com/emotion-js/emotion)

[The css Prop](https://emotion.sh/docs/css-prop)

[Storybook을 활용하여 본격적으로 디자인 시스템 구축하기](https://velog.io/@velopert/create-your-own-design-system-with-storybook)
