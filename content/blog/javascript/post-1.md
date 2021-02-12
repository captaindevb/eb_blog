---
title: '자바스크립트 비동기, 동기, 비동기처리 '
date: 2021-02-12 18:30:00
category: 'javascript'
draft: false
---

## 1. 자바스크립트 비동기?

비동기란 특정 로직의 실행이 끝날 때 까지 기다리지 않고 순차적으로 다음 코드를 바로 실행하는 것을 말한다.<br/>
아래 예시코드를 보자.

```js
console.log('1')
setTimeout(function() {
  console.log('2')
}, 3000)
console.log('3')
```

자바스크립트 비동기의 특성을 이해하지 못했다면, 결과는 123이라고 생각했을 것이다.<br/> 하지만 setTimeout이 실행됨과 동시에 바로 다음 console.log가 찍히기 때문에 결과는 1,3,그리고 삼초 후 2가 찍히게된다.

## 2. 동기 VS 비동기

동기는 요청을 보낸 후 해당 응답을 받아야 다음 동작을 실행,<br/>
비동기는 요청을 보낸 후 응답에 관계 없이 다음 동작을 실행한다.

## 3. 비동기 처리

대표적으로 비동기 처리가 필요한 곳은 클라이언트에서 서버로 데이터를 요청했을때이다.<br/>
자바스크립트의 비동기 특성에 따르면, 서버로 데이터를 요청했을때 그 응답을 기다리지 않고 바로 다음 코드를 실행할것이다.<br/>
그러나 클라이언트는 응답 후의 데이터로 화면에 무언가를 그리거나 하려면 응답한 데이터가 필요하다.<br/>
그러므로 이러한 자바스크립트의 비동기 특징을 잘 처리해야만 클라이언트는 요청한 데이터를 가져온 후 데이터 처리를 할수 있다.<br/>
이런 일련의 방식들을 비동기 처리라고 하며, 대표적으로 ajax통신에서 콜백함수 사용하기, promise객체 사용하기, es6의 async await 사용법등이 있다.

## 4. async await

async와 await는 자바스크립트의 비동기 처리 패턴 중 가장 최근에 나온 문법이다. 기존의 비동기 처리 방식인 콜백 함수와 프로미스의 단점을 보완하고 개발자가 읽기 좋은 코드를 작성할 수 있게 도와준다.

```js
const data = fetchData('domain.com/list')
if (data.id === 1) {
  console.log(data.title)
}
```

위 코드는 fetchData가 실행되고 응답을 기다리기 전에 if문을 실행한다. 그러므로 data.id값을 찾을 수 없어서 console.log를 찍지 못할것이다.<br/>
위와 같은 코드를 async await 문법을 적용시켜주면 어떨까? 간단명료하게 비동기를 처리해줄 수 있다.

```js
async function getTitle() {
  var data = await fetchUser('domain.com/list')
  if (data.id === 1) {
    console.log(data.title)
  }
}
```

아주 간단하다. 전체적인 코드를 함수로 한번 감싸는데, 그대신 async를 붙여준다. 그러면 그 안에서 await를 사용할 수 있게된다.<br/>
async를 사용해서 비동기 처리 할 함수라고 표현해주고, 그 안에서 응답을 기다려야 할 함수 앞에 await를 붙여주면 data에 응답값이 담기길 기다렸다가 다음 코드를 실행하게된다. 그럼 data에 id값, title값이 모두 잘 담길것이고 title console.log가 찍힐것이다.<br/><br/>

```js
const getMemberProfile = async () => {
  const { result, data, message } = await getProfile({
    memNo: memNo,
  })
  if (result === 'success') {
    setCurrentProfile(data)
  } else {
    alert(message)
  }
}
```

실제로 실무에서 대표적으로 사용되는 예제이다. 클라이언트 단에서 프로필을 가져오는 api함수를 호출하였고, 응답 데이터 중 result, data, message를 각각 구조분해하여 사용하였다. result가 성공일땐 프로필 데이터를 담아주고, 실패일땐 알럿 메시지를 띄우는 방식이다.<br/>
getProfile을 조금더 자세히 들여다보자. 여기서도 비슷한 패턴이 사용된다.

```js
type responseType = {
  result: string,
  data: any,
  message: string,
}

const ajax = async (method: string, path: string, data?: dataType) => {
  let url: string = (() => {
    // api서버와 path를 url에 합쳐준다.
    return API_SERVER + path
  })()

  if (data !== undefined) {
    if (method === Method.GET) {
      let queryString = '?' // data로 넘겨받은 객체를 쿼리스트링화 해준다.
      Object.keys(data).forEach((key, i, self) => {
        if (self.length === i + 1) {
          queryString += `${key}=${data[key]}`
        } else {
          queryString += `${key}=${data[key]}&`
        }
      })
      url += queryString
    }
  }

  return await fetch(url).then(res => {
    //완성된 url로 서버에 요청을 보낸다.
    if (res.status === HttpStatus.OK) {
      return res.json() //완료되면 응답 데이터를 Json 형식으로 반환한다.
    }
  })
}

export async function getProfile(data: {
  memNo: string,
}): Promise<responseType> {
  return await ajax(Method.GET, '/profile', data)
}
```

실제 클라이언트에서 서버 통신 하는 부분을 간략화 한 것이다. getProfile자체가 async 함수이며 await로 ajax통신을 하고있다. <br/>
ajax 통신역시 들여다보면 async 함수이고 직접적으로 서버에 요청을 보내는 부분 또한 await가 추가되어있다. <br/>

이렇듯 클라이언트와 서버 소통에 비동기 처리는 필수적이다. 꼭 기억해서 잘 사용하자.
<br/><br/><br/>
