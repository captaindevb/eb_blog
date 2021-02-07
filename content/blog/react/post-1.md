---
title: 'useEffect와 useLayoutEffect의 차이점'
date: 2021-02-07 15:21:13
category: 'react'
draft: false
---

## 1. useEffect

대표적인 hooks중 하나로, 클래스형 컴포넌트의 생에주기로 생각하면 componentDidMount와 componentDedUpdate를 합친형태라고 생각하면 된다.
레이아웃 배치, 화면 그리기를 완료 한 후 실행된다.
사용법은 마운트 될때 한번만 실행할 경우 혹은 특정 값이 바뀌었을때 사용할 경우 두가지의 경우가 있다.

```js{3}
import React, { useEffect } from 'react'

//컴포넌트가 렌더링 될 때 최초 1회
useEffect(() => {
  console.log('1')
}, []) // 두번째 파라미터에 비어있는 배열을 넣는다.

//state의 값이 바뀔 때 마다
useEffect(() => {
  console.log('2')
}, [state]) // 원하는 업데이트 값을 넣어준다.
```

위와 같이 작성하면 컴포넌트가 렌더링 될때 콘솔 1을, 그 다음에 2를 찍는다.
두번째 useEffect 같은 경우 파라미터로 넣어준 state값이 바뀔때마다 실행된다.
단, 최초 초기값이 바뀔시 (선언시)에도 한번 실행되므로 유의해야 한다.
초기값이 빈값일 경우 실행하면 안되는 코드가 들어갔을 경우, 이를 막는 코드를 넣어주어야 한다.

또한 return 구문을 넣어 뒷정리 함수를 넣어줄 수 있다.

```js{3}
import React, { useEffect } from 'react'

useEffect(() => {
  console.log('1')

  return => { console.log('언마운티드 되었을 때')}
}, [])
```

위 코드를 실행하게되면 컴포넌트를 빠져나올때 return 구문이 실행되게 된다.

## 2. useLayoutEffect

useEffect와 비슷한 역할을 하지만, 다른 점은 화면이 렌더링 되기 전에 실행된다는 점이다.
useEffect는 레이아웃 배치가 다 된 후 실행하지만 useLayoutEffect는 그 전에 실행된다. 다음 코드를 보자.

```js{3}
import React, { useEffect, useLayoutEffect } from 'react'

useEffect(() => {
  console.log('1')
}, [])

useLayoutEffect(() => {
  console.log('2')
}, [])
```

위 코드를 실행하게되면 console.log는 차례대로 2, 1를 찍게된다.
즉, 화면이 렌더링 되기 전에 실행해야 할 함수가 있다면 유용하게 쓰일 수 있다.
