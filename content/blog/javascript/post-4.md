---
title: '클로저 Closure'
date: 2021-03-01 13:00:00
category: 'javascript'
draft: false
---

자바스크립트 책을 펼치면 필수적으로 나오는 개념 클로저, <br/>
그 클로저에 대해 책 여러권을 보며 나름대로 내린 결론과 과정들을 포스팅해보려고 한다.<br/><br/>

참조한 책은 아래와 같다.<br/>
속깊은 자바스크립트<br/>
자바스크립트 핵심가이드<br/>
자바스크립트 200제<br/>
자바스크립트 인사이드<br/><br/>

Closure 뜻: 폐쇄<br/><br/>
도데체 클로저가 자바스크립트에서 어떤 의미이길래 폐쇄라고 하는걸까?
하나씩 차근차근 알아가보자.

## 1. 간단한 예제

함수안에 함수를 반환하는 클로저 예시는 꼭 등장하는 예시이다. 아래와 같은 함수를 보자.

```js
function sum(base: number) {
  var inClousure = base
  return function(adder: number) {
    console.log(inClousure, adder)
    return inClousure + adder
  }
}
var fiveAdder = sum(5) //콘솔 로그가 찍히지 않음.
fiveAdder(3) // 5 3
fiveAdder(2) // 5 2
```

클로저란 도데체 어떻게 생겨먹었길래, 첫 함수에서는 콘솔로그가 찍히지 않고
나중엔 adder 매개변수를 넣어준 적도 없는데 왜 실행이 되는걸까? <br/><br/>

sum함수를 정의한 곳은 넘어가고, 첫 줄부터 보자.<br/>
var fiveAdder = sum(5) 절에서 함수를 실행시켜 대입하고 있는데, 이곳에서는 콘솔로그가 찍히지 않는다. 이게 어찌된일일까?
우선 매개변수 base에 5를 받아서, 그 수를 inClousure 변수에 저장을 하고있다.<br/>
그리고 함수를 하나 리턴하는데, 첫 줄에서는 이 함수를 리턴해 fiveAdder는 이 익명함수를 할당받게 되어 갖고 있게 된다.<br/><br/>

fiveAdder의 스코프 체인은 sum(5)함수는 익명함수가 참조된 채 글로벌 스코프의 fiveAdder를 참조하고 있고, fiveAdder는 adder 익명 함수를 참조한 상태가 된다. 얼핏 생각해보면 순환 구조로 되어있는 듯 하지만, 모든 스코프 체인은 글로벌 영역에서 끝난다. <br/>
그리고 글로벌 영역에서 fiveAdder가 가지고 있는 것은 다시 익명함수(adder)를 참조하는 스코프 체인이 아니라 함수 자체에대한 레퍼런스를 갖고 있는 것이다. 쉽게 참조하고 있다고 생각하면 안될까? <br/><br/>

그래서 나중에 fiveAdder함수를 호출하면, 익명함수가 호출되어 5에 3을 더하는 동작을 수행하게 된다. <br/>
다시 다른 숫자를 넣어 fiveAdder 함수를 호출하면 inClousure 변수는 그대로인 상태에서 adder 변수만 바뀐채로 호출이 된다. <br/>
처음 fiveAdder에 대입했을 그 상황을 그대로 간직(참조)하고 있는 것이다. 해당 스코프를 계속해서 참조하고 있다고 할 수 있다. <br/><br/>

```js
var threeAdder = sum(3)
fiveAdder(3) // 3 3
```

이렇게 호출하면 어떻게 될까? 당연히, 새로운 스코프가 생겨나면서 inClosure 변수에 3이 대입되고 함수와 함께 반환되어 3+3의 값이 계산된다.
threeAdder, fiveAdder 각각의 스코프를 가지고 있고 스코프 체인을 유지하면서 그 체인 안에 있는 모든 변수의 값들을 유지한채로 해당 스코프에 계속 접근하게 되는것이다. <br/><br/>

이렇게 내부의 함수가 반환될 때 클로저 (폐쇄)가 발생되는 것이다. 폐쇄라는 말이 맞는 것 같다. 각자의 스코프를 가지고 있고 한번 반환되었던 변수 외에는 접근할수가 없기때문이다.<br/><br/>

이렇듯 클로저를 가장 많이 사용하는 것은 자바스크립트 라이브러리나 모듈에서 private으로 나의 변수를 보호하고 싶을 때나 static 변수를 이용하고 싶을 때이다. 그리고 일상적으로 콜백 함수에 추가적인 값들을 넘겨줘서 활용하거나 처음에 초기화했던 값을 계쏙 유지하고 싶을때도 유용하게 사용할 수 있다.
<br/>

## 2. 다른 책들에서 정의한 클로저

위 예제가 이해가 됐다면, 책들에서 정의한 내용들도 모두 이해가 될것이다.
다른 책에서도 비슷한 예제를 가지고 설명해준다. 조금 다른 듯 보이지만 결은 모두 비슷하다.

```js
var quo = function(status: string) {
  return {
    get_status: function() {
      return status
    },
  }
}

var myQuo = quo('나른해진 오후햇살')
console.log(myQuo) // { get_status: [Function: get_status] }
console.log(myQuo.get_status()) // 나른해진 오후햇살
```

quo를 호출하면 get_status메소드가 있는 객체를 반환하게 되고, 이 객체에 대한 참조는 myQuo에 저장된다.
get_status 메소드는 quo가 이미 반환된 뒤에도 계속 quo의 get_status에 접근할 수 있는 권한을 가지게 된다.
get_status는 status 매개변수의 복사본에 접근할 수 있는 게 아니라, 매개변수 그 자체에 대한 접근 권한을 가지게된다.<br/><br/>
이러한 작동이 가능한 것은 함수가 자신이 생성된 함수, 즉 자신을 내포하는 함수의 문맥(context)에 접근할 수 있기 때문이고
이런 개념을 클로저라고 부른다.<br/><br/>

위에서 살펴본 예제의 내용과 완전히 동일하다.

<br/>

또 다른 예시를 보자.

클로저를 이해하려면 자바스크립트가 어떻게 변수의 유효범위를 지정하는지(Lexical scoping)를 먼저 이해해야 한다. 다음 예시를 보자.

```js
var person = '엄지'

function print() {
  var person2 = '은하'
  function innerPrint() {
    console.log(person)
    console.log(person2)
  }
  innerPrint()
  console.log('프린트 끝')
}

print()
console.log('함수 모두 끝')
```

결과는
엄지
은하
프린트 끝
함수 모두 끝
순서대로 출력된다.

위 코드에 대한 실행 컨텍스트가 실행될 때 시간에 따라 어떻게 스택이 쌓이고 제거되는지 보자.
우선 전역-print-innterPrint 까지 실행 컨텍스트를 쌓고 순서대로 실행하며 스택을 제거해나간다.
실행 컨텍스트는 렉시컬 환경을 가지고 있는데, 렉시컬 환경은 환경 레코드와 외부 실행 환경에 대한 참조 두가지를 가지고 있다.
각각 외부 실행 환경을 참조하고 있어서, 각 렉시컬 환경에 연결되어 있고 이것을 스코프 체인이라고 한다.

위처럼 렉시컬 스코핑(렉시컬 환경)을 알아보았다. 그 다음 클로저는 어떨까?
아래와 같은 예시를 보자.

```js
function createCounterClosure() {
  let count = 0
  return {
    add: function() {
      count++
    },
    getCount: function() {
      return count
    },
  }
}

const counter1 = createCounterClosure() //add와 getCount 메소드를 가진 객체 반환
const counter2 = createCounterClosure()

counter1.add()
counter1.add()
console.log(counter1.getCount()) //2
counter2.add()
console.log(counter2.getCount()) //1
```

위 코드에서 counter1과 counter2의 메소드들이 다른 count에 접근하는 것은 다른 렉시컬 환경에서 count에 접근하는 것이다. 이러한 현상이 가능한 이유가 바로 클로저 때문이다. 즉, 클로저란 함수가 정의될 때의 렉시컬 환경을 기억하는 함수를 말한다.
<br/>
4라인과 7라인의 add와 getCount 함수가 정의 될 때 렉시컬 환경은 createCounterClosure 실행 컨텍스트의 렉시컬 환경이다. 이 실행 컨텍스트는 13,14 라인에서 각각 counter1과 counter2를 정의할 때 생성된다. 그래서 add함수와 getCount 함수는 createCounterClosure 실행 컨텍스트의 렉시컬 환경을 기억하고 있는 클로저가 된다.<br/>
위 렉시컬 스코핑 예제에서 봤듯이 대체로 실행 컨텍스트가 스택에서 제거되면 해당 환경은 사라지기 마련이지만, 위 예제처럼 클로저가 만들어지면 해당 환경은 사라지지 않는다. 왜냐하면 해당 참조를 물고있기 때문이다.(그래서 클로저는 메모리를 소모한다는 단점을 가지고 있다.)

## 3. 클로저 결론

클로저라는 말은 뭔가 어렵고 거창해보이지만, 실제로 코드를 살펴보면 자바스크립트 개발자라면 음~ 당연한거 아닌가? 하고 넘어갈만한 것이 대부분이다. 즉 실무를 경험하며 이미 클로저를 활용하며 개발을 하고 있기때문이고, 그 개념이 명확히 정리가 되지 않았기 때문이 아닐까 한다.
이번 기회에 나는 여러 책들을 접해보면서 다양한 클로저의 예시와 개념 설명을 보고 확실히 나만의 방법으로 클로저의 개념을 정리해보게 되었다.
혹 새로운 책들을 보며 괜찮은 클로저에 대한 설명을 발견하면, 다시 와서 내용을 정리하도록 하겠다.

<br/><br/><br/>
