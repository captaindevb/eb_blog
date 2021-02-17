---
title: 'AOS, IOS 네이티브 통신'
date: 2021-02-17 19:00:00
category: 'javascript'
draft: false
---

하이브리드 앱 개발을 할때, 웹과 네이티브가 각종 정보를 주고받거나 어떠한 특정 이벤트를 발생시킬때 서로 통신해야 하는 경우가 많다. 그럴 때 사용하는 웹과 네이티브 간 통신 방법을 알아보자.

## 1. Native to Web 통신

네이티브(AOS,IOS)에서 웹으로 이벤트를 전달받을때에는 윈도우에 커스텀 이벤트를 추가하는 방식으로 통신한다.<br/>
전역에서 사용할 interface.js를 생성하고, 그 안에 원하는 이벤트를 만들어서 document에 이벤트리스너를 붙여주는 방식으로 네이티브에서 이벤트를 전달받는다. <br/>네이티브 쪽에서는 type(이벤트명)과 detail(넘겨줄 정보)를 같이 내려준다.

```js
  const update = (event) = >{
    const {type, detail} = event
    switch(type){
      case 'native-test':
        //원하는 동작
        break
      case 'native-close':
        .....
    }
  }
  useEffect(() => {
    document.addEventListener('native-test', update)
    document.addEventListener('native-close', update)
     return () => {
      document.removeEventListener('native-test', update)
      document.removeEventListener('native-close', update)
     }
  },[])
```

useEffect clean-up할시 반드시 이벤트를 지워준다.
주의해야 할 점은 update시에 원하는 동작을 할때 context가 필요하다면 꼭 쓰일 context state 값을 useEffect 두번째 인자로 넘겨주어야 한다. <br/>
그렇지 않으면 이벤트가 add될 당시의 context를 물고 있기때문에, 원하는 결과가 나오지 않을 수 있다.<br/>

## 2. Web to IOS 통신

웹에서 IOS(아이폰)으로 통신할때는 webkit 기반 통신을 한다.

```js
webkit.messageHandlers[func].postMessage('')
webkit.messageHandlers[func].postMessage(info)
```

messageHandlers에 원하는 함수를 붙여주고, postMessage로 보낼 데이터를 붙여주면된다. IOS는 postMessge를 꼭 붙여주어야 하는데, 보낼 값이 없을땐 위처럼 빈 스트링값이라도 보내야한다.

## 3. Web to AOS 통신

AOS(안드로이드) 통신은 훨씬 더 간단하다. window.android를 호출해서 사용해주면 된다.

```js
window.android[func](JSON.stringify(info))
```

window.android에 원하는 함수를 붙이고, 원하는 데이터를 넘겨주면된다. 단 객체일때는 반드시 JSON.stringify를 해준다. string 값의 경우 그냥 넘긴다. <br/><br/>

javascript에서 호출하는 부분은 어떻게 보면 간단할 수 있지만 각 데이터를 넘겨줄때 주의해야 할 부분을 꼭 보면서 전달하자.

<br/><br/><br/>
