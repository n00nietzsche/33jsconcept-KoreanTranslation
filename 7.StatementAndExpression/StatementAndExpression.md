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
