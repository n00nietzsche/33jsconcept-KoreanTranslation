
자바스크립트 개발자라면 알아야 할 33가지 개념 #3 값(Value) vs 참조(Reference) (번역)


## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

## 자바스크립트에서 값(Value)과 참조(Reference)
> 컴퓨터 메모리를 잠시 들여다 보면 그 안에 해답이 있습니다.

![메모리 이미지.jpeg](https://images.velog.io/post-images/jakeseo_me/ddd4f260-51bd-11e9-a806-8f3a20f1c19e/메모리-이미지.jpeg)

> 참고 : 이 글은 [Step Up Your JS: A Comprehensive Guide to Intermediate JavaScript](https://www.educative.io/collection/5679346740101120/5707702298738688?authorName=Arnav%20Aggarwal) 이 링크에 존재하는 저자의 강의 중 일부를 저자가 재구성하여 작성한 글입니다.. [Quiz Section](https://www.educative.io/collection/page/5679346740101120/5707702298738688/5685265389584384) 이 링크에서 저자가 만든 인터렉티브한 퀴즈를 풀 수 있습니다.

자바스크립트는 ***값에 의한 전달(passed by value)*** 이 일어나는 5가지의 데이터타입(`Boolean`, `Null`, `Undefined`, `String`, `Number`)을 가지고 있습니다. 우리는 이러한 데이터 타입을 ***원시 타입(Primitive Types)*** 이라고 부릅니다. 

또 자바스크립트는 ***참조에 의한 전달(passed by reference)*** 이 일어나는 3가지의 데이터 타입(`Array`, `Function`, `Object`)도 가지고 있습니다. 사실 이 3가지는 크게 보자면 전부 `객체(Objects)`로 볼 수 있습니다.

## 원시타입(Primitives)
어떠한 원시타입이 변수에 할당된다면, 우리는 그 변수를 원시타입을 가진 변수라고 생각할 수 있습니다.

```javascript
var x = 10;
var y = 'abc';
var z = null;
```

이 경우에는 `x`는 `10`이란 값을 가지고 있고, `y`는 `abc`란 값을 가지고 있습니다. 이러한 것들을 이해했다면 메모리 상에 존재하는 변수들을 다음과 같은 이미지로 나타낼 수 있을 것입니다.


![메모리상 이미지.png](https://images.velog.io/post-images/jakeseo_me/2b321030-51c1-11e9-87aa-df2c53a2fd5f/메모리상-이미지.png)

우리가 이 변수들을 다른 변수에 `=`이라는 키워드를 이용하여 할당할 때, 우리는 새로운 변수에 값을 **복사(Copy)** 하게 됩니다. 이 변수들은 값에 의해 복사되죠.

```javascript
var x = 10;
var y = 'abc';

var a = x;
var b = y;

console.log(x, y, a, b); // -> 10, 'abc', 10, 'abc'
```

