---
title: '[CSS] styled-jsx의 CSS 선택자 우선순위 (feat. stylis)'
date: 2018-03-25 12:09:41
tags:
- css
- css selector
- github
- styled-jsx
- nextjs
- stylis
---
CSS는 일련의 우선순위(가중치)에 따라서 어떤 속성을 가장 우선적으로 적용할 것인지 결정합니다. 우선순위에 대한 정보는 조금만 검색해보면 나오므로 간단히 [링크](https://gist.github.com/mjj2000/5873872)로 대체합니다.

현재 회사에서 [nextjs]((https://github.com/zeit/next.js/)를 사용한 프로젝트를 진행하고 있습니다. nextjs는 서버사이드렌더링(SSR)을 지원하는 리액트 전용 프레임워크입니다. [create-react-app](https://github.com/facebook/create-react-app)처럼 zero-configuration을 지향하며 일부 설정은 수정할 수 있는 장점도 가지고 있습니다. nextjs 내에서 여러가지 라이브러리들이 사용되고 있습니다. 그 중에서 CSS 코드를 react 컴포넌트(jsx)에서 작성하기 위해 [styled-jsx](https://github.com/zeit/styled-jsx)를 사용합니다. 


이런 경우가 있었습니다.
```
<div>
  <div>
    <span className="title">빨간색으로 나와야 할텐데..</span>
  </div>
</div>
<style jsx>{`
  div > div > span {
    color: blue;
  }
  .title {
    color: red;
  }
`}</style>

```
결과는 아래와 같았습니다.

<span style="color:blue">빨간색으로 나와야 할텐데..</span>

우리가 익히 알고 있는 css 선택자의 우선순위는 `class`가 `element` 보다 명시도가 높습니다. 왜냐하면 아래 표처럼 element가 1점이라면 class는 10점으로 평가되기 때문입니다. 즉, 첫번째 스타일은 3점, 두번째 스타일은 10점이 되는 것이죠.

| css | specificity(명시도) |
| :-: | :-: |
| div>div>span | 0 0 0 3 |
| .title | 0 0 1 0 |

그런데 styled-jsx(`v2.2.x` 사용)를 통해서 컴파일이 완료되면 실제 명시도는 아래처럼 변경됩니다.

| css | specificity(명시도) |
| :-: | :-: |
| div.jsx-1234>div.jsx-1234>span.jsx-1234 | 0 0 3 3 |
| .title.jsx-1234 | 0 0 2 0 |
 
첫번째 스타일이 두번째 스타일보다 명시도가 높게 되었습니다. 원래 기대했던 것과는 전혀 다른 결과가 나왔습니다. 사용법에 문제가 있었던 것인지, 아니면 이미 보고된 버그인지를 확인하기 위해 styled-jsx github 페이지에 [버그](https://github.com/zeit/styled-jsx/issues/424)를 보고했습니다.

하루가 지나니 `bug`로 등록되었습니다!!!

{% asset_img bug.png %}

쓰레드에 달리는 글을 보니 styled-jsx에서 사용하는 `stylis`라는 라이브러리가 각 컴포넌트에 사용된 id, class, element 에 `jsx-1234` class를 붙여주는 역할을 하고 있었습니다. 이 문제를 곰곰히 생각해보니 가중치에 상관없이 모든 요소(id, class, element)에 class를 붙여주는게 문제였습니다. 그래서 각 CSS 선택자마다 하나의 class만 붙여주면 되지 않을까하는 생각이 들더군요.

첫 element에만 class를 붙이거나, 

| css | specificity(명시도) |
| :-: | :-: |
| div.jsx-1234>div>span | 0 0 1 3 |
| .title.jsx-1234 | 0 0 2 0 |

마지막 element에 class를 붙이는식으로 처리하는 것입니다.

| css | specificity(명시도) |
| :-: | :-: |
| div>div>span.jsx-1234 | 0 0 1 3 |
| .title.jsx-1234 | 0 0 2 0 |

모든 케이스를 고려해 보지 않았지만, 이런 방식으로 처리하면 문제가 해결될 것이라고 생각되었습니다. 그래서, [댓글](https://github.com/zeit/styled-jsx/issues/424#issuecomment-375518440
)을 달았습니다. 그랬더니, 와!! 

{% asset_img answer.png %}

이후로 `stylis` [이슈](https://github.com/thysultan/stylis.js/issues/101) 에서 해결방안이 활발히 논의되고 있습니다(맨 앞 요소와 맨 뒤 요소에 `jsx-1234를 붙여주는걸로 가닥을 잡은 것 같습니다). 제가 직접 코드를 기여하지 않았습니다만, 제 의견이 반영되니 묘한 기분이 들더군요. 지금껏 오픈소스에 기여한적이 없었기 때문에 그랬는지도 모르겠습니다. 다들 이 맛에 오픈소스 하는구나라는 생각이 드는 경험이었습니다.

#### 참고자료
- 
https://gist.github.com/mjj2000/5873872
- https://developer.mozilla.org/ko/docs/Web/CSS/Specificity