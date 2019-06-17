## 들어가기 전에

- 이 포스팅은 https://blog.bitsrc.io/understanding-higher-order-functions-in-javascript-75461803bad 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://blog.bitsrc.io/understanding-higher-order-functions-in-javascript-75461803bad If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #22 자바스크립트 : 자바스크립트에서 고차 함수(Higher-Order Function) 이해하기

고차 함수(Higher-Order Function)가 무엇인지 배워보고 자바스크립트에서 고차함수를 어떻게 사용하는지 배워봅시다.

여러분이 자바스크립트를 배우는 중이라면, *고차 함수(Higher-Order Function)* 라는 용어를 얼핏 본 적이 있을 것입니다. 용어 자체는 매우 복잡하게 생겼을지 몰라도, 사실 그렇지 않습니다.

자바스크립트를 함수형 프로그래밍에 알맞은 언어로 만들어주는 특성이 바로 자바스크립트가 고차 함수 개념을 받아들인다는 것입니다.

고차 함수는 자바스크립트에서 광범위하게 사용됩니다. 만일 여러분이 자바스크립트에서 프로그래밍을 한지 꽤 됐다면, 여러분은 심지어 고차 함수가 무엇인지 모를지라도, 고차함수를 이용하여 프로그래밍 해왔을 것입니다.

고차 함수의 개념을 완전히 이해하기 위해서는 *함수형 프로그래밍(Functional Programming)* 이 무엇인지와 *퍼스트 클래스 함수(First-Class Functions)* 의 개념을 이해하는 것이 먼저입니다.

> **팁 :** **[Bit (GitHub)](https://github.com/teambit/bit)** 을 사용해보세요. 앱에서 컴포넌트를 공유하고 재사용하기 쉽게 해줍니다. 어떤 JS 코드와도 작동합니다. 그러니 한번 시도해보세요.

> 역자 주 : 이 글의 출처는 위에 소개된 Bit이라는 회사의 테크 섹션입니다. 리액트 컴포넌트가 필요하시다면 한번 방문해보세요.

## 함수형 프로그래밍이란 무엇일까요?

가장 간결한 용어, 함수형 프로그래밍은 함수를 다른 함수의 파라미터로 넘길 수도 있고 반환(return) 값으로 함수를 받을 수도 있는 프로그래밍 형태를 말합니다. 함수형 프로그래밍에서, 우리는 함수라는 용어 하에서 생각하고 코딩하게 됩니다.

자바스크립트, Haskell, Clojure, Scala, Erlang은 전부 함수형 프로그래밍을 구현한 언어입니다.

## 퍼스트클래스(First-Class) 함수

만일 여러분이 자바스크립트를 배워왔다면, 자바스크립트가 함수를 일급 시민(first-class citizen)으로 대해준다는 것을 들어봤을 겁니다. 왜냐하면 자바스크립트 또는 다른 함수형 프로그래밍 언어 함수들은 전부 객체(objects)이기 때문입니다.

자바스크립트에서, 함수는 객체의 특별한 타입입니다. 함수는 `Function` 객체입니다. 

예를 들자면 :

```js
function greeting() {
  console.log('Hello World');
}

// 함수 호출하기
greeting(); // prints 'Hello World'
```

자바스크립트에서 함수가 오브젝트인 것을 증명하기 위해서, 우리는 다음과 같은 코드를 작성할 수 있습니다.

```js
// 우리는 오브젝트에 프로퍼티를 추가하듯 함수에 프로퍼티를 추가할 수 있습니다. 
greeting.lang = 'English';

// Prints 'English'
console.log(greeting.lang);
```

**알아둘 것ㅡ** 위 문법은 자바스크립트에서 완전히 유효한 문법인 반면에, 위와 같이 코드를 작성하는 것은 매우 위험하게 여겨지고 있습니다. 함수 오브젝트에 랜덤한 프로퍼티를 추가하지 않는 것이 좋습니다. 만일 그래야만 한다면 오브젝트를 사용하세요.

자바스크립트에서, object, string, number와 같은 타입으로 할 수 있는 것은, 함수로 할 수 있습니다. 여러분은 함수를 파라미터로 다른 함수에 넘길 수도 있고(콜백), 함수를 다른 변수에 할당하거나 다른 곳으로 넘길 수 있습니다. 이러한 특성 때문에 자바스크립트에 존재하는 함수들이 퍼스트 클래스 함수라 불리는 것입니다.

### 함수를 변수에 할당하기

우리는 자바스크립트에서 함수를 변수에 할당할 수 있습니다. 

예를 들면:

```js
const square = function(x) {
  return x * x;
}

// prints 25
square(5);
```

우리는 또한 함수를 여러 곳에 넘길 수도 있습니다. 

예를 들면:

```js
const foo = square;

// prints 36
foo(6);
```

### 함수를 파라미터로 넘기기

우리는 함수를 여러 곳에 파라미터로 넘길 수 있습니다.

예를 들면 : 

```js
function formalGreeting() {
  console.log('How are you?');
}

function casualGreeting() {
  console.log("What's up?");
}

function greet(type, greetFormal, greetCasual) {
  if(type === 'formal') {
    greetFormal();
  } else if(type === 'casual') {
    greetCasual();
  }
}

// prints "What's up?"
greet('casual', formalGreeting, casualGreeting);
```

이제 우리는 퍼스트 클래스 함수가 무엇인지 알았습니다. 이제 자바스크립트 고차 함수에 대해서 자세히 알아봅시다.


