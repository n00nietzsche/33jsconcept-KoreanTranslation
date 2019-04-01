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

`a`와 `x`는 둘 다 지금 `10`이란 값을 갖고 있습니다. `b`와 `y`는 현재 둘 다 `'abc'`라는 값을 갖고 있구요. 하지만 이들은 분리되어 있습니다. 값들이 복사되었기 때문이죠.

![메모리상 이미지2.png](https://images.velog.io/post-images/jakeseo_me/0432ada0-5414-11e9-9723-eb39f385be66/메모리상-이미지2.png)

같은 값을 가진 변수 하나를 바꾸더라도 다른 변수에는 아무런 영향이 없습니다. 각각의 변수들이 아무런 관계도 없다고 생각해도 무방하겠죠.

## 객체(Objects)
이번에 배울 내용은 어렵게 느껴질 수 있습니다. 하지만 천천히 읽어나가고 이해한 뒤에는 아마 쉽다고 느껴질 거에요.

원시 타입이 아닌 값(non-primitive value)이 할당된 변수들은 그 값으로 향하는 *참조(reference)* 를 갖게 됩니다. *참조(reference)* 는 메모리에서의 객체의 위치를 가리키고 있습니다. 변수는 실제로 값을 가지고 있지 않습니다.

객체는 우리의 컴퓨터 메모리 어딘가에 생성됩니다. 우리가 `arr = []` 를 작성했을 때, 우리는 메모리 내부에 배열을 만든 것입니다. 변수 `arr`이 갖는 것은 그 배열이 위치한 주소입니다.

