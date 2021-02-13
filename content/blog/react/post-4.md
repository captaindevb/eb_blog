---
title: '커스텀 Hook'
date: 2021-02-13 13:47:00
category: 'react'
draft: false
---

## 1. 커스텀 hook?

자신만의 Hook을 만들면 컴포넌트 로직을 함수로 뽑아내어 재사용할 수 있다. 즉, 중복 로직을 줄일 수 있고 함수의 재사용성을 높이는데 필수요소라고 할 수 있다.

### 첫번쨰 규칙. 커스텀 hook의 이름은 맨 앞에 use를 꼭 붙인다.

그렇지않으면 리액트 내에서 자동적으로 hook 규칙이 맞는지 확인하지 못한다.

### 두번쨰 규칙. 각 hook은 같은 로직을 공유하지만, 같은 state값을 공유하지는 않는다.

hook은 state적인 특성을 갖고 있어서 공유하지 않는다. 완전히 독립적인 개체이다.

## 2. custom Hook 예제코드

```js
import React, { useState, useEffect } from 'react'

const useInputTag = defaultValue => {
  const [value, setValue] = useState(defaultValue)

  const onChange = e => {
    const {
      target: { value },
    } = e
    setValue(value)
  }

  return { value, onChange }
}

const App = () => {
  return (
    <div>
      <input {...useInputTag} type="password" />
      <input {...useInputTag} type="email" />
      <input {...useInputTag} type="name" />
    </div>
  )
}

export default App
```

위 코드는 useInputTag 커스텀 훅을 만들어서 각 input마다 다른 함수들과 state를 만들지 않아도 되는 코드이다. <br/>
그리고 그 함수의 반환값을 value와 onChange를 사용하여 바로 input에 넣어주는 편리함도 있다.

만일, 기존의 리액트라면

```js
<input value={name} onChange={e => setName(e.target.value)} />
```

이런식으로 각각 input 값마다 풀어서 써주어야 했을것이다.
이상 커스텀훅으로 반복코드를 줄이고 재사용성을 높이는 방법에 대해 알아보았다.
앞으로 커스텀훅으로 만든 코드는 계속해서 업데이트 할 예정이다.

<br/><br/><br/>
