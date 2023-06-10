---
description: useInterSectionObserver (커스텀 훅)
---

# 무한스크롤

**리액트에서 무한 스크롤을 구현할 수 있는 방법은 두가지가 있음**

1. scroll event 감지 (thrittle, debounce 사용)
2. Intersection Observer API

* 우리는 후자로 구현하기로 했음

#### Intersection Observer API

* 타겟 요소와 상위 요소의 viewport 사이의 `intersection`내의 변화를 비동기적으로 관찰하는 방법
* 타겟 교차 부분이 변경될 때마다 실행될 콜백함수를 부른다.

#### intersection Observer 생성

* intersection observer를 생성하기 위해서는 생성자 호출시 콜백함수를 제공해야한다.
* 콜백함수는 threshole가 한방향 혹은 다른 방향으로 교차할 때 실행된다.

```jsx
let obtions = {
	root: document.querySelector('#scrollArea'), // default : 브라우저 뷰포트
	rootMargin: '0px',
	threshole: 1.0 // 1.0 은 target이 요소 내에서 100% 보여질때 콜백함수 호출하는 것
}
```

* `root` 뷰포트 요소, 기본값은 브라우저 뷰포트, root값이 null이거나 지정되지 않을 때 기본값으로 설정된다.
* `rootMargin` root가 가진 여백, root에 마진값을 주어 범위를 확장.
*   `threshold` 타겟 요소가 얼만큼 들어왔을 때 콜백함수를 실행될 것인지 결정

    (0\~1.0) 기본값은 0이다.

#### 사용 예시

```jsx
// 관측에 적용할 옵션값
const obtions = {
	root: null,
	rootMargin: '0px'
	threshold: 1.0
}

// 관측되었을 경우 실행할 콜백함수
const callback = () => {
	console.log('관측되었습니다.')
}

// observer 선언
// 첫번째 인자로 관측되었을 경우 실행할 콜백함수
// 두번째 인자로 관측에 대한 옵션을 지정
const observer = new IntersectionObserver(callback, options);

// 타겟 요소를 지정
// react에서는 useRef를 활용해서 Dom 선택
const target = useRef();

observer.observe(target); // 타겟 요소 관측 시작
observer.unobserve(target); // 타겟 요소 관측 종료

```

\<aside> 💡 useRef 함수는 current 속성을 가지고 있는 객체를 반환. 인자로 넘어온 초기값을 current 속성에 할당한다.

***

#### 연습 : 무한 스크롤 구현

* target에 위치하면 intersectHandler 콜백함수를 불러옴 (콜백함수 : 아이템 추가)
* 아래 참조 블로그 참고해서 만들었음.

#### useState 버전

```tsx
import { useState, useRef, useEffect } from "react";

const useObserver = ({
  intersectHandler,
  root,
  rootMargin = "0px",
  threshold = 0
}) => {
  const [target, setTarget] = useState(null); 

  useEffect(() => {
    if (!target) return;

    const observer = new IntersectionObserver(intersectHandler, {
      root,
      rootMargin,
      threshold
    });

    observer.observe(target);

    return () => observer.unobserve(target);
  }, [target, intersectHandler, root, rootMargin, threshold]);

  return { setTarget };
};

export default function App() {
  const [pageNum, setPageNum] = useState(1);
  const [listItems, setListItems] = useState([1, 2, 3, 4, 5]);

  const intersectHandler = ([entry]) => {
    if (!entry.isIntersecting) return;

    setPageNum((num) => num + 1);

    const lastNum = listItems.at(-1);
    setListItems((previous) => [
      ...previous,
      ...Array.from({ length: 5 }, (_, i) => lastNum + i + 1)
    ]);

    console.log(pageNum); // 1
    console.log(listItems); // [1,2,3,4,5]
  };

  const { setTarget } = useObserver({
    intersectHandler,
    threshold: 0.8
  });

  return (
    <div style={{ height: "100px" }}>
      {listItems?.map((item, i) => (
        <div
          key={i}
          style={{
            width: "100px",
            height: "500px"
          }}
        >
          {item}
        </div>
      ))}
      <div ref={setTarget}>target</div>
    </div>
  );
}
```

#### useRef 버전

```tsx
import { useState, useRef, useEffect } from "react";

const useObserver = ({
  intersectHandler,
  root,
  rootMargin = "0px",
  threshold = 0
}) => {
  const targetRef = useRef(null);

  useEffect(() => {
    if (!targetRef.current) return;

    const observer = new IntersectionObserver(intersectHandler, {
      root,
      rootMargin,
      threshold
    });

    observer.observe(targetRef.current);

    return () => observer.unobserve(targetRef.current);
  }, [targetRef, intersectHandler, root, rootMargin, threshold]);

  return { targetRef };
};

export default function App() {
  const [pageNum, setPageNum] = useState(1);
  const [listItems, setListItems] = useState([1, 2, 3, 4, 5]);

  const intersectHandler = ([entry]) => {
    if (!entry.isIntersecting) return;

    setPageNum((num) => num + 1);

    const lastNum = listItems.at(-1);
    setListItems((previous) => [
      ...previous,
      ...Array.from({ length: 5 }, (_, i) => lastNum + i + 1)
    ]);
  };

  const { targetRef } = useObserver({
    intersectHandler
  });

  return (
    <div style={{ height: "100px" }}>
      {listItems?.map((item, i) => (
        <div
          key={i}
          style={{
            width: "100px",
            height: "500px"
          }}
        >
          {item}
        </div>
      ))}
      <div ref={targetRef}>target</div>
    </div>
  );
}
```

***

#### useIntersectionObserver 완성 (type 추가 수정 필요)

```jsx
import React, { ReactNode, useEffect, useRef } from 'react';

// type 지정
interface UseIntersectionObserverProps {
  intersectHandler: IntersectionObserverCallback;
  root: ReactNode;
  rootMargin: string;
  threshold: number;
}

// useIntersectionObserver 커스텀 훅
const useIntersectionObserver = ({
  intersectHandler,
  root = null,
  rootMargin = '0px',
  threshold = 0,   // useIntersectionObserver는 인자로 콜백함수와 옵션을 받으므로 해당 값을 props로 전달
}: UseIntersectionObserverProps) => {
  const observeRef = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (!observeRef.current) return; // target에 도달하지 않은 경우 그냥 리턴.

    const observer = new IntersectionObserver(intersectHandler, { root, rootMargin, threshold });

    observer.observe(observeRef.current);

    return () => observer.unobserve(observeRef.current);
  }, [intersectHandler, root, rootMargin, threshold])

  return { observeRef };
};

export default useIntersectionObserver;
```

***

### 참고

{% embed url="https://github.dev/sjoleee/bemyidol-frontend" %}

{% embed url="https://rrecoder.tistory.com/171" %}

{% embed url="https://velog.io/@sjoleee_/React-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4" %}

{% embed url="https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver" %}

{% embed url="https://heropy.blog/2019/10/27/intersection-observer/" %}

{% embed url="https://velog.io/@sjoleee_/debounce-throttle-%EC%A0%95%EB%A6%AC" %}

{% embed url="https://velog.io/@jsi06138/React-Intersection-Observer-API%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0" %}
