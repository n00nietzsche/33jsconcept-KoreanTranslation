## 들어가기 전에

- 이 포스팅은 https://javascript.info/async-await 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://javascript.info/async-await If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #26 자바스크립트 : Async / Await

## Async/await 

더 편안한 환경에서 Promise를 다루기 위해 태어난 특별한 문법이 있습니다. 그 문법은 "async/await"이라 불립니다. 정말 놀랍게도 이해하고 사용하기에 너무나 쉽습니다.

### Async 함수

`async` 키워드의 사용법부터 알아봅시다. 이 키워드는 function 앞에 올 수 있습니다. 다음과 같이요.

```js
async function f() {
  return 1;
}
```

함수 전에 "async"라는 단어가 의미하는 것은 간단합니다 : promise를 반환하는 함수라는 뜻입니다. 심지어 만일함수가 실제로 promise가 아닌 값을 반환해도, "async" 키워드로 정의된 함수는 자바스크립트에서 자동으로 그 값을 resolve promise로 감싸라고 지시합니다.

예를 들면, 위에 우리가 작성했던 코드는 `1`이라는 결과 값의 resolved promise를 반환합니다. 테스트해봅시다.

```js
async function f() {
  return 1;
}

f().then(alert); // 1
```

![promise-test-1.png](https://images.velog.io/post-images/jakeseo_me/e5d27130-ade9-11e9-bdc2-ada958a32967/promise-test-1.png)
