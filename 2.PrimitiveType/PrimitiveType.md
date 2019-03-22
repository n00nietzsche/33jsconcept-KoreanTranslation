## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 http://blog.brew.com.hk/not-everything-in-javascript-is-an-object/ 글을 제 멋대로 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from http://blog.brew.com.hk/not-everything-in-javascript-is-an-object/ If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

## 자바스크립트 언어의 정체는 무엇인가?

자바스크립트가 객체지향 프로그래밍 언어(OOP)인지 함수형 언어인지에 대해서는 많은 혼란이 있어왔습니다. 사실 자바스크립트는 두가지 역할 다 가능합니다. 하지만 이러한 사실은 사람들에게 "자바스크립트에 있는 모든 것들은 객체들인가?", "그렇다면 함수들도 객체로 봐야하나?"와 같은 질문을 유발했습니다.

이 포스팅은 이러한 것들을 깔끔하게 정리할 것입니다.

### 기초잡기

자바스크립트에는 6가지 종류의 원시 데이터 타입이 있습니다. 원시 데이터 타입은 쉽게 말하자면 자바스크립트에서 객체가 아닌 것들이며 값 그 자체로 저장된 것입니다. 이게 무슨 뜻인지는 뒤의 내용들이 천천히 설명해줄 것입니다.
- Boolean `true` or `false`
- `null`
- `undefined`
- `number` (두 배의 정밀함을 가진 64-bit float입니다. 자바스크립트에는 정수 타입은 존재하지 않습니다.)
- `string`
- `symbol` (ES6에서 처음 생긴 원시타입입니다.)

이 6가지의 원시 타입들에 더해 ECMAScript 표준은 `Object`를 정의했습니다. `Object`는 간단히 말하자면 키-값 저장소이죠.
```javascript
const object = {
	key: "value" 
}
```

그래서 요약하자면, Primitive Type이 아닌 것은 `Object`입니다. 그리고 `Object`라는 개념은 함수들과 배열들도 포함하죠.

> 모든 함수는 Object이다.

```javascript
// Primitive Types
true instanceof Object; // false -> boolean
null instanceof Object; // false -> null
undefined instanceof Object; // false -> undefined
0 instanceof Object; // false -> number
'bar' instanceof Object // false -> string

// Non-primitive types
const foo = function () {}
foo instanceof Object; // true -> function;
```

### 원시 타입

원시 타입에는 어떠한 메소드도 붙어있지 않습니다. 이를테면 `undefined.toString()`과 같은 형태의 메소드는 본적이 없을 것입니다. 또한 이러한 특성 때문에, 원시 타입은 변하지 않는(immutable) 속성을 갖습니다. 왜냐하면 원시 타입은 자신을 변경할 수 있는 메소드를 갖지 않으니까요.

물론 변수에 원시 타입을 얼마든지 재할당 할 수 있습니다. 하지만 이것은 실제로 변수에 할당되었던 원시타입의 값이 바뀌는 것이 아니라 새로운 원시타입의 값이 들어가는 개념입니다. 원시타입 값 자체는 절대 바뀔 수 없습니다.

> 원시타입은 불변적이다. (Primitive types are immutable)

게다가, 원시 타입은 참조(reference)로 저장되는 `Object`와 다르게 값 그 자체로 저장되어 있습니다. 값이 동일한지 체크할 때 앞의 문장이 정확히 무슨 의미인지 알 수 있습니다.

```javascript
"dog" === "dog"; // true 
14 === 14; // true

{} === {}; // false
[] === []; // false
(function () {}) === (function () {}); // false
```
> 원시 타입은 값(value)으로 저장되고, 객체들은 참조(reference)로 저장됩니다.

### 함수

함수는 특별한 프로퍼티들을 가진 새로운 형태의 객체입니다. 생성자와 콜처럼 말이죠.
```javascript
const foo = function (baz) {} ;
foo.name; // "foo"
foo.length; // 1
```
일반적인 객체와 같이 함수에 새로운 프로퍼티를 추가하는 것도 가능합니다.
```javascript
foo.bar = "baz";
foo.bar // "baz"
```
함수의 이러한 특성은 함수를 1급 객체로 만듭니다. 왜냐하면 다음과 같은 조건을 만족하기 때문입니다.
1. 다른 함수의 인자값으로 넘겨질 수 있다.
2. 변수나 데이터에 할당 가능하다.
3. 객체의 리턴 값으로 리턴 가능하다.
- 이러한 특성은 자바스크립트의 객체들이 갖는 특성과 같습니다.

### 메소드

메소드는 함수와 같이 객체의 프로퍼티입니다.
```javascript
const foo = {};
foo.bar = function () { console.log("baz"); };
foo.bar(); // "baz"
```

### 생성자 함수

생성자 함수가 정확히 어떤 것인지 모르는 개발자들도 많을 것입니다. 자바스크립트에서 생성자 함수란 리턴 값으로 생성하는 생성자 함수를 객체 그 자체로서 반환하는 함수입니다. 같은 코드를 공유하는 여러가지 객체들을 갖고 싶다면, 생성자 함수를 삽입하는 것은 매우 좋은 선택입니다.

사실 생성자 함수는 다른 함수와 별반 다를 것이 없습니다. 그저 `new`라는 키워드가 붙은 이후에 생성자 함수로서 사용된다는 점과 객체 자체를 리턴한다는 점 밖에는요.

> 어떤 함수든 생성자 함수가 될 수 있습니다.

```javascript
const Foo = function () {};
const bar = new Foo();
bar; // {}
bar instanceof Foo; // true
bar instanceof Object; // true
```

생성자 함수는 object를 리턴하게 됩니다. object에 새로운 프로퍼티들을 할당하기 위해 `this`를 함수의 몸통 안에서 사용할 수 있습니다. 우리가 `"baz"`값으로 초기화된 `bar`라는 프로퍼티를 가진 object를 많이 만들고 싶다면 그 로직을 캡슐화하는 Foo라는 생성자 함수를 만들 수 있겠죠.

```javascript
const Foo = function () {
	this.bar = "baz";
};
const qux = new Foo();
qux; // { bar: "baz" }
qux instanceof Foo; // true
qux instanceof Object; // true
```

> 새로운 오브젝트를 만들기 위해서 생성자 함수를 사용할 수 있습니다.

`new` 키워드 없이 단순히 `Foo()`라는 함수를 실행한다면 `Foo`는 일반적인 함수처럼 동작할 것입니다. 함수 내부에 들어있는 `this`라는 키워드는 '실행 컨텍스트'와 응답을 주고 받습니다. 그래서 만일 우리가 `Foo()`라는 함수를 전역 컨텍스트에서 실행시키게 되면, `window` 객체에 `bar`라는 프로퍼티가 추가됩니다. 

```javascript
Foo(); // undefined
window.bar; // "baz"
```

반대로 말하자면, 일반 함수를 생성자 함수로 실행한다면 그저 새로운 빈 오브젝트를 반환할 뿐이라는 거죠. 우리가 본 것처럼 말이죠.

```javascript
const pet = new String("dog");
// pet은 원시 타입의 "dog" 값을 갖는 것이 아니라 생성자 함수로 생성된 String 객체를 갖게 됩니다.
```

### 래퍼 오브젝트 (Wrapper Object, 포장 오브젝트)

`String`, `Number`, `Boolean`, `Function` 와 같은 원시타입을 `new` 키워드로 생성하면 원시타입에 대한 `래퍼 오브젝트(Wrapper Object)`가 생성됩니다. 

