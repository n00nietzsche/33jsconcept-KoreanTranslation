## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://dev.to/promhize/javascript-in-depth-all-you-need-to-know-about-expressions-statements-and-expression-statements-5k2 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #7 표현식(Expression)과 문장(Statement) (번역)

이 글을 거의 다 읽었을 때쯤에는 아래의 이미지의 코드가 어떻게 동작하는지 왜 동작하는지에 대해서 자세히 설명할 수 있게 될 것입니다.

![ExpressionAndStatement1.PNG](https://images.velog.io/post-images/jakeseo_me/503a8310-5f38-11e9-88b2-25d00148b532/ExpressionAndStatement1.PNG)

자바스크립트에는 대표적인 2가지 문법적 카테고리가 있습니다.

1. Statements (문장)
2. Expressions (표현식)

표현식(Expression)은 문장(Statement)처럼 동작할 수 있기 때문에 이 둘을 구분하는 것은 중요합니다. 그리고 이것이 표현문(Expression statement)이 존재하는 이유기도 하죠. 하지만 반대로 봤을 때 문장(Statement)은 표현식(Expression)처럼 동작할 수 없습니다.

# 표현식(Expressions)
## 표현식은 값을 만들어낸다

표현식은 값 하나로 귀결되는 자바스크립트 코드 조각(snippet)입니다. 표현식은 우리가 원하는만큼 길어질 수 있지만 언제나 동일한 값이 나오진 않죠.

```js
2 + 2 * 3 / 2

(Math.random() * (100-20)) + 20

functionCall()

window.history ? useHistory() : noHistoryFallback()

1+1, 2+2, 3+3

declaredVariable

true && functionCall()

true && declaredVariable
```

위의 모든 코드는 표현식입니다. 그리고 표현식은 자바스크립트 코드 중 값이 들어가는 곳이면 어디에나 넣을 수 있습니다.  그래서 아래 `console.log`의 인자는 콘솔이 로깅될 때 하나의 값으로 변합니다.

```js
console.log(true && 2 * 9) // 18
```

## 표현식은 반드시 상태(State)를 바꿀 필요는 없다.

예를 들면,

```js
const assignedVariable = 2; // 이건 문장(Statement)입니다. assignedVariable은 상태입니다.

assignedVariable * 4 // 표현식(Expression)입니다.

assignedVariable * 10 // 표현식(Expression)입니다.

assignedVariable - 10 // 표현식(Expression)입니다.

console.log(assignedVariable) // 2
```

위의 짤막한 코드의 모든 표현식에도 불구하고 assignedVariable의 값은 여전히 2입니다. 그래서 왜 이 섹션의 제목에 왜 `반드시`라는 단어를 등장시켰냐면 함수 호출은 표현식이기 때문입니다. 하지만 함수는 값을 변화시키는 문장(Statement)을 포함할 수 있습니다. foo 내부의 `foo()` 함수는 undefined나 어떤 다른 값을 반환할 수 있는 표현식입니다. 하지만 만일 `foo`가 다음과 같이 작성됐다면,

```js
const foo = foo () => {
  assignedValue = 14  
}
```

그 땐, foo를 호출하는 것은 표현식일지라도, 함수를 호출하면 결국 상태(state)가 바뀌게 됩니다. 그래서 foo 함수를 더 나은 방법으로 재작성하려면 문장(Statement)는 다음과 같을 것입니다.

```js
const foo = foo () => {
  return 14; // 가독성을 위한 명시적 반환  
}

assignedVariable = foo()
```

아니면 더 나은 방법으로

```js
const foo = foo (n) => {
  return n // 가독성을 위한 명시적 반환  
}

assignedVariable = foo(14)
```

이 편이 더욱 가독성이 좋고, 어딘가에 끼워넣기 좋으며 표현식(Expression)과 문장(Statement) 사이에서 확연히 구분이 됩니다. 이것이 선언적이고 함수적인 자바스크립트의 기반입니다.

# 문장(Statements)

함수형 프로그래밍의 관점에서 문장은 골치덩어리(headache)입니다. 기본적으로 문장은 무언가 수행합니다.

자바스크립트에서 문장은 값이 들어와야 할 곳에 들어갈 수 없습니다. 그래서 그들은 함수의 인자로도, 대입 연산의 값으로도, 연산자의 피연산자로도 사용될 수 없습니다.

```js
foo(if () {return 2}) // js engine mind = blown
```

자바스크립트의 문장(Statement)은 다음과 같습니다.
1. if
2. if-else
3. while
4. do-while
5. for
6. switch
7. for-in
8. with (deprecated)
9. debugger
10. variable declaration

브라우저의 콘솔에 다음과 같은 코드를 치고 엔터를 치면 어떻게 될까요?

```js
if (true) {9+9}
```

아마 18이 리턴된 것이 보일 것입니다. 하지만 우리는 이 결과를 표현식 처럼 사용하거나 자바스크립트 코드 내에 값이 들어갈 어딘가에 넣을 수 없습니다. 이 결과는 이상합니다. 우리는 문장(statement)이 아무것도 반환하지 않을 것이라 예상했기 때문일 겁니다. 우리가 반환된 값을 이용할 수 없으면 문장(statement)이 값을 반환하는 것은 아무런 의미가 없습니다. 이게 바로 자바스크립트입니다. 이상합니다.

## 함수 선언, 함수 표현식 그리고 네임드(Named) 함수 표현식

함수 선언은 문장(Statement)입니다.

```js
function foo (func) {
  return func.name;  
}
```

함수 표현식은 표현식입니다. 바로 우리가 익명 함수라 부르는 것들입니다.

```js
console.log(foo(function () {} )); // ""
```

네임드 함수 표현식은 표현식입니다. 익명 함수처럼요. 하지만 이 함수는 이름이 붙었습니다.

```js
console.log(foo(function myName () {} )); // "myName"
```

표현식으로서의 함수와 선언으로서의 함수의 구분은 아래의 내용을 이해하는 것으로 요약됩니다.
**우리가 자바스크립트에서 값이 들어올 곳에 함수를 선언할 때마다, 자바스크립트는 그것을 값으로 다루려 할 것입니다. 만일 그 함수가 값으로 사용될 수 없다면, 에러가 발생할 것입니다. 
반면에 스크립트, 모듈, 블록 문장(자바스크립트에서 값이 들어가는 곳이 아닌 위치에 있는)의 전역 단계(Global level)에 함수를 선언하는 것은 결과적으로 함수선언입니다.**

예제:

```js
if () {
  function foo () {} // 블록의 가장 상위 레벨, 함수 선언
}

function foo () {} // 전역 레벨, 함수 선언

function foo () {
  function bar () {} // 블록의 가장 상위 레벨, 함수 선언
}

function foo () {
  return function bar () {} // 네임드 함수 표현식  
}

foo(function () {}) // 익명 함수 표현식

function foo () {
  return function bar () {
    function baz () {} // 블록의 가장 상위 레벨, 함수 선언  
  }
}

function () {} // 문법 에러: 함수 문장(statement)은 이름이 필요합니다.
```

## 표현식을 문장으로 바꾸기: 표현식 문장(Expression Statements)

여태까지 자바스크립트에 대한 것중 어느것 하나라도 간단하고 직관적인 것이 있었나요?

```js
2+2; // expression statement
foo(); // expression statement
```

우리는 표현식(Expressions)을 표현식 문장(Expression Statements)으로 바꿀 수 있습니다. 단지 뒤에 세미콜론만 추가하면 됩니다. `2+2`자체는 표현식입니다. 하지만 그 줄(line) 자체는 표현식 문장(Expression Statements)입니다.

```js
2+2 // 그 자체로는 표현식입니다.

foo(2+2) // 그래서 어디든 값이 들어가야 할 곳에서 사용할 수 있죠.

true ? 2+2 : 1 + 1

function foo () {return 2+2}

2+2; // 표현식 문장(Expression Statements)
foo(2+2;) // 문법 에러(Syntax Error)
```

## 세미콜론 vs 콤마 연산자

세미콜론을 붙이면, 여러 줄의 문장(Statements)을 하나의 줄에 넣을 수 있습니다.

```js
const a; function foo () {}; const b = 2;
```

콤마 연산자는 우리가 여러 개의 표현식을 연결할 수 있도록 도와줍니다. 반환은 마지막 표현식만 반환합니다.

```js
console.log( (1+2, 3, 4) ) // 4

console.log( (2, 9/3, function () {}) ) //function () {}

console.log( (3, true ? 2+2 : 1+1) ) // 4
```

> 알아두면 좋은 것 : 자바스크립트 엔진에게 값을 전달할 때, 값이 들어가야 할 곳에 괄호'()'를 통해 값을 전달하세요. 괄호가 없으면 각각을 console.log의 인자로 보냅니다.

```js
function foo () {return 1, 2, 3, 4}
foo() // 4
```

모든 표현식은 왼쪽에서 오른쪽으로 계산됩니다. 그리고 마지막 것이 리턴이 되죠.

## IIFEs (Immediately Invoked Function Expression(즉시 호출되는 함수 표현식))

익명 함수는 표현식으로 쓰일 수 있습니다. 자바스크립트에서 값이 들어갈 곳에 쓰일 수 있다면, 우리가 만약 자바스크립트에서 값이 들어갈 곳에 괄호를 쓸 수 있다면 우리는 익명 함수를 값으로 넘길 수 있다는 것을 의미합니다.

```js
function () {}
```

위의 코드는 유효하지 않은 반면에 아래의 코드는 유효합니다.

```js
(function () {})
```

만일 익명 함수를 괄호 속에 즉시 집어넣는 
