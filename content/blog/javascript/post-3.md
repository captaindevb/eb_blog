---
title: 'ppk 자바스크립트 - 코어'
date: 2021-02-21 15:00:00
category: 'javascript'
draft: false
---

PPK자바스크립트(저자 피터 폴 콕) - 코어 파트 부분을 읽으며 잊어버리지 않도록 내용 몇개를 정리해보도록 한다.
비교적 설명이 쉬워서 좋았던 부분들 위주로 정리해보았다.

## 1. 산술 연산자 축약

```js
c = c * a
c *= a // 위 산술 연산자를 이와같이 축약가능.
```

## 2. ++와 --

원래 알고 있던 내용이지만, 이 책에서 굉장히 쉽게 정리가 되어있어서 정리.<br/>

1. ++가 변수명 앞에 있으면 변수가 먼저 증가되고 새 값이 다음 연산자에 전달된다.
2. ++가 변수명 뒤에 있으면 변수의 값이 다음 연산자에 전달된 후에 변수가 증가된다.

```js
var c = (d = 1)
var a = c++ //1
var b = ++d //2
```

1. var a = c++ 는 'c의 이전 값을 a에 대입하고 c를 증가시킨다'는 뜻이다.
2. var b = ++d 는 'd를 증가시키고 새 값을 b에 대입한다'는 뜻이다.

## 3. 비교 연산자

1. 양쪽 연산자가 문자열이지만, 모두 숫자로 해석될 수 있으면 두 값은 숫자로 비교된다.

```js
var x = '100'
var y = '1000'
x < y //true
```

2. 피연산자 중 하나가 숫자로 해석될 수 없으면 문자열 비교 연산이 된다.

```js
var a = 'abc'
var b = 'def'
var c = 'ABC'
var d = '123'

a > b //false. 알파벳 순서에 따라 비교연산이 된다. 'abc'보다 'def'문자열이 알파벳 순서상 뒤로 오므로, false
a > c //true 대문자가 소문자보다 '작다'
a > d //true 문자열을 숫자형으로 변환
```

<참고 > - https://developer.mozilla.org/<br/>

- 숫자와 문자열을 비교할 땐 문자열을 숫자로 변환한다. 우선, 문자열에서 수학적 값을 도출한다. 그 후 가장 가까운 Number 자료형 값으로 반올림하고
  하나의 연산자가 Boolean인 경우, true는 1, false는 0으로 변환한다.<br/>
- 객체를 문자열이나 숫자와 비교하는 경우, JavaScript는 객체의 기본값을 사용한다. 연산자는 우선 객체의 valueOf() 또는 toString() 메서드를 사용해 문자열, 혹은 숫자 값을 받으려 시도하며, 실패할 경우 런타임 오류가 발생한다.<br/>
- 객체를 원시값과 비교하는 경우에만 객체의 변환이 발생한다. 두 연산자가 모두 객체인 경우 변환하지 않으며, 둘 다 같은 객체를 참조하는 경우 참을 반환한다.

## 4. Math 객체

1. Math.round() 는 항상 가장 가까운 숫자로 만든다.
2. Math.floor() 는 항상 내림을 한다.
3. Math.ceil() 은 항상 올림을 한다.

```js
Math.round(1.4) // 1
Math.round(1.6) // 2
Math.floor(1.6) // 1
Math.floor(-1.6) // -2
Math.ceil(1.6) // 2
Math.ceil(-1.6) // -1
```

4. Math.random() 은 0과 1 사이의 난수 값을 생성한다. <br/>
   0과 10 사이의 난수 값이 필요하면, Math.random()에 10을 곱하기만 하면 된다.

## 5. parseInt()와 parseFloat()

둘 다 문자열을 받아서 숫자(정수와 실수)를 생성한다. 이 메소드의 강점은 인자로 들어가는 무자열이 숫자가 아닌 문자를 포함할 수 있다는 데 있다.

```js
parseInt('3.54 apples') // 3
parseFloat('3.54 apples') // 3.54
parseInt('apples 3.54') //NaN
```

각 메소드는 문자열의 왼쪽에서 시작한다. 첫 번째 문자가 숫자 또는 마이너스가 아니면 NaN을 리턴한다. <br/>
첫 번째 문자가 유효하면, 메소드는 숫자가 아닌 문자열이 나타날 때까지 문자열을 계속 읽어가면서 문자가 나타나면 그때까지 추출된 숫자를 리턴한다.

## 6. 인용부호

```js
var ex1 = "I'm developer"
var ex2 = "I'm developer"
```

단일 인용부호를 이스케이프 처리( 문자 앞에 역슬래시를 추가)하면 자바스크립트가 다음 문자를 구분자가 아닌 문자 자체로 인식하게 한다.

## 7. indexOf()와 lastIndexOf()

주 문자열 안에서의 문자열 인덱스를 리턴한다. 문자열 첫번째 문자가 1에서 부터 시작되는게 아니라 0에서 부터 시작되는 것에 주의하자.

```js
var ex1 = "I'm developer"
ex1.indexOf('developer') //4
ex1.indexOf('hacker') //-1
```

요청된 문자열이 없으면 -1을 리턴한다.

```js
var ex2 = 'I am a Javascript hacker'
ex2.indexOf('a') //2
ex2.indexOf('a', 3) //5 인덱스 숫자 3(네 번째 문자)에서 검색을 시작하고 5를 리턴한다.
```

lastIndexOf() 메소드는 문자열의 끝에서부터 반대 방향으로 찾는다.

```js
var ex2 = 'I am a Javascript hacker'
ex2.lastIndexOf('a') //19
ex2.lastIndexOf('a', 18) //10
```

## 8. chartAt()

문자열 안에서 특정 위치에 있는 글자를 얻는 데 사용된다.

```js
var ex2 = 'I am a Javascript hacker'
ex2.charAt(9) //v
```

## 9. substring()

문자열로부터 부분문자열을 얻는 데 쓰인다. 부분 문자열의 시작점과 끝점을 지정해줘야한다.

```js
var ex2 = 'I am a Javascript hacker'
ex2.substring(5, 8) //'a J'
```

주의할 점은, 시작점의 글자는 부분문자열에 포함되지만 끝점의 글자(인덱스8)는 포함되지 않는다.

## 10. split()

문자열을 몇 부분으로 나누는 역할을 하는 메소드이다. 함수의 인자는 문자열이 나뉠 글자를 정의하고 split()은 문자열을 리턴한다.

```js
var ex2 = 'I am a Javascript hacker'
ex2.split('c') // c가 나타나는 곳 마다 문자열을 나눠서 배열로 반환한다. c는 모두 사라진다.
// ['I am a Javas','ript ha','ker']
```

폼 벨리데이션 등에서 " " 빈 띄어쓰기 문자열을 없애는 등의 처리를 할때 편리하다.

<br/><br/><br/>
