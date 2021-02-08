---
title: 'useCallback'
date: 2021-02-09 08:20:00
category: 'react'
draft: false
---

## 1. useCallback

useCallback은 react의 hooks 중 하나로, 주로 렌더링 성능을 최적화 하기 위해 사용된다. useCallback을 사용하면 함수를 필요할 때만 생성할 수 있다. 보통 컴포넌트가 리렌더링 될때마다 함수들이 새로 생성되는데, 이 현상을 방지할 수 있다.

나는 실제로 프로젝트에서 라우터를 분기에 따라 리턴 해 주는 함수를 만들었는데, 라우터가 변경될때마다 memory reak 현상이 발생되었다.
기존 소스를 보자.

```js{3}
import React from 'react'

const mypageNaviList = [
  { type: "wallet", component: <Wallet />, txt: "내 지갑" },
  { type: "report", component: <Report />, txt: "리포트" },
];

const MypageRouter = () => {
    if (!category) {
      if (baseData.memNo === currentMemNo) {
        return <MyProfile />;
      } else {
        return <OtherProfile />;
      }
    } else {
      const currentNaviObj = mypageNaviList.find((v) => v.type === category);
      return currentNaviObj ? currentNaviObj.component : <></>;
    }
  });

  return (
    <>
      <h2>mypage</h2>
      <MypageRouter />
    </>
  );
```

위 소스는 간단하게 마이페이지 라우터를 구현한 것인데, Params에 caregory가 있다면 그에 해당하는 컴포넌트를 리턴하고, 없다면 내가 갖고 있는 회원번호 Params로 들어오는 회원번호가 일치하는지 비교하여 내 프로필을 보여주고 그렇지 않을 경우에는 다른 사람의 프로필을 보여주는 라우터이다.

실제로 이 소스는 잘 작동했지만, react memory leak 워닝이 페이지가 바뀔때 마다 떴다. 컴포넌트가 바뀔때마다 계속해서 함수를 새로 생성하기 때문이다. 그래서 위 소스를 useCallbak을 사용하여 아래와 같이 변경하였다.

```js{3}
import React from 'react'

const mypageNaviList = [
  { type: 'wallet', component: <Wallet />, txt: '내 지갑' },
  { type: 'report', component: <Report />, txt: '리포트' },
]

const MypageRouter = useCallback(() => {
  if (!category) {
    if (baseData.memNo === currentMemNo) {
      return <MyProfile />
    } else {
      return <OtherProfile />
    }
  } else {
    const currentNaviObj = mypageNaviList.find(v => v.type === category)
    return currentNaviObj ? currentNaviObj.component : <></>
  }
}, [category])

return (
  <>
    <h2>mypage</h2>
    <MypageRouter />
  </>
)
```

라우터 함수에 useCallback을 사용하고, 두번째 파라미터로 변경되는 값을 넣어준다. 만일 두번째 파라미터를 빈값으로 넣는다면 라우터가 바뀌어야 할 시점(Params가 바뀌었을때)에 함수를 실행하지 않기때문에 라우터가 바뀌지 않는다.

이렇게 소스를 수정하면 더이상 memory leak 워닝이 뜨지 않는다. 함수를 재사용했기때문이다. 불필요한 렌더링을 방지하기 위한 useCallback 사용은 리액트 최적화에 있어서 꼭 필요한 요소이다.
