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

우리는 명시적으로 promise를 반환할 수도 있습니다. 아래의 코드도 같은 결과를 출력합니다.

```js
async function f() {
  return Promise.resolve(1)
}

f().then(alert); // 1
```

![promise-test-1.png](https://images.velog.io/post-images/jakeseo_me/e5d27130-ade9-11e9-bdc2-ada958a32967/promise-test-1.png)
> 번역자의 경우에는 구글 크롬 개발자도구에서 console로 찍어서도 확인해보았습니다.

`async`는 함수가 promise를 리턴하는 것을 보장해줍니다. 그리고 promise가 아닌 것을 리턴했을 때는 promise로 감싸서 resolve promise를 반환하는 것을 확인했을 것입니다. 상당히 간단하죠? 하지만 그것 뿐만이 아닙니다. 또 다른 키워드 `await`이 있습니다. 이 키워드는 오직 `async` 키워드가 붙은 함수와 함께 동작합니다. 그리고 꽤 멋진 키워드입니다.

### Await

문법은 다음과 같습니다.

```js
// 오직 async 함수 내부에서만 동작합니다.
let value = await promise;
```

키워드 `await`은 자바스크립트가 promise가 작업 이후 결과 값을 리턴할 때까지 잠시 기다리게 만듭니다. 

1초 후에 resolve하는 promise의 예제를 봅시다.

```js
async function f() {
  
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("done!"), 1000);
  });
  
  let result = await promise; // promise가 resolve될 때까지 기다립니다 (*)
  
  alert(result); // 끝입니다!
}

f();
```

함수 실행이 `(*)`의 라인에서 잠시 멈춥니다. 그리고 promise가 완료됐을 때, `result`에는 promise의 결과값이 할당됩니다. 그래서 위의 코드에서 1초 후에 "done!"이라는 메세지가 출력되는 이유입니다.

다음의 내용을 잘 기억합시다: `await`은 말 그대로 자바스크립트가 promise가 끝날 때까지 기다리게 만드는 것입니다. 그 후에 promise의 결과 값을 갖고 다음 부분을 진행합니다. 이 과정은 어떠한 CPU 리소스도 소모하지 않습니다. 왜냐하면 엔진이 그 동안 다른 일을 할 수 있기 때문입니다: 다른 스크립트를 실행하고 이벤트를 다루는 등의 일을 합니다.

이 문법은 promise의 결과를 받고 `promise.then`을 사용하는 것보다 더 우아한 문법입니다. 읽기도 더 쉽고 작성하기도 더 쉽습니다.

> 경고! 일반적인 함수에서는 `await`을 사용할 수 없습니다!

비동기 함수에서 `await`을 사용하려 시도하면, 문법 에러가 발생할 것입니다.

```js
function f() {
  let promise = Promise.resolve(1);
  let result = await promise; // Syntax error
}
```

함수 앞에 `async`라는 키워드를 붙여주지 않는다면, 우리는 이러한 에러를 받게 될 것입니다. 말했던 것처럼 `await`은 오직 `async function` 내부에서만 작동합니다.

`showAvatar()` 예제를 봅시다. 이 예제는 [Promise Chaining](https://javascript.info/promise-chaining)이라는 챕터에서 가져왔습니다. 그리고 우린 이 예제를 `async/await`을 이용하여 다시 작성해볼 것입니다.

기존의 소스는 아래와 같은 형태였습니다.

```js
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .then(githubUser => new Promise(function(resolve, reject) {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  }))
  // triggers after 3 seconds
  .then(githubUser => alert(`Finished showing ${githubUser.name}`));
```

1. 우리는 `.then`을 `await`으로 대체할 것입니다.
2. `await`을 쓰기 위해, 함수에 `async`를 붙여줄 것입니다.

```js
async function showAvatar() {
  // read our JSON
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.join();
  
  // read github user
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();
  
  // show the avatar
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);
  
  // wait 3 seconds
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));
  
  img.remove();
  
  return githubUser;
}

showAvatar();
```

꽤 깔끔하고 읽기 쉬워졌습니다. 맞죠? 이전보다는 훨씬요

> `await`은 최상위 수준(top-level) 코드에서 작동하지 않습니다.

`await`을 이제 막 쓰기 시작한 사람들은 `await`을 최상위 수준(top-level) 코드에서 사용할 수 없다는 사실을 종종 잊는 경향이 있습니다. 예를 들면 다음과 같은 코드는 작동하지 않습니다.

```js
// syntax error in 최상위 수준(top-level) code
let response = await fetch('/article/promise-chaining/user.json');
let user = await response.json();
```

우린 위의 코드를 익명 async 함수 안에 감쌀 수 있습니다. 다음과 같이요.

```js
(async () => {
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();
})();
```

> `await`은 "thenable"를 받습니다.

`promise.then`처럼, `await`은 thenable 오브젝트('thenable' 오브젝트란 `.then` 메소드 호출이 가능한 메소드를 말합니다.)를 사용합니다. 제 3 오브젝트는 Promise가 아닐 수도 있다는 겁니다. Promise와 호환 가능하면: 만일 `.then`메소드를 지원만 한다면, `await`과 함께 사용할 수 있는 겁니다.

여기 데모 `Thenable` 클래스가 있습니다. 아래의 `await`은 `Thenable`의 인스턴스를 받습니다.

```js
class Thenable {
  constructor(num) {
    this.num = num;
  }
  then(resolve, reject) {
    alert(resolve);
    // 1초 후에 입력된 숫자의 2배의 값과 함께 resolve됩니다.
    setTimeout(() => resolve(this.num * 2), 1000); // (*)
  }
};

async function f() {
  // waits for 1 second, then result becomes 2
  let result = await new Thenable(1);
  alert(result);
}

f();
```

`await`이 `.then` 메소드를 가진 promise가 아닌 오브젝트를 받았을 때, `await`은 native 함수인 `resolve`, `reject`를 인자로 `.then`메소드를 호출합니다. 그 후에, `await`은 둘 중 하나가 호출될 때까지 기다립니다. (이 예제에서는 `(*)`이 적혀있는 라인에서 그 일이 일어나게 됩니다.) 그리고 그 후에 결과 값과 함께 나머지 코드가 계속됩니다.

> Async 메소드

Async 클래스 메소드를 선언하기 위해서 할 일은, 그냥 `async`라는 키워드를 앞에 붙이면 됩니다.

```js
class Waiter {
  async wait() {
    return await Promise.resolve(1);
  }
}

new Waiter()
  .wait()
  .then(alert); // 1
```

결국 의미하는 바는 같습니다. 이 클래스는 반환되는 값이 promise이며 `await`을 사용 가능하게 보장합니다.

### 에러 핸들링

만일 promise가 일반적으로 resolve한다면, `await promise`는 결과를 반환합니다. 하지만 rejection이 된 경우, promise는 에러를 내뱉게 됩니다. 코드 라인에 `throw`가 있는 것 처럼 말입니다.

다음 코드는:

```js
async function f() {
  await Promise.reject(new Error("Whoops!"));
}
```

는 다음 코드와 같습니다.

```js
async function f() {
  throw new Error("Whoops!");
}
```

실제 상황에서, promise는 reject 당하기 전 약간의 시간을 소모할 수도 있습니다. 그래서 `await`은 대기할 것입니다. 그리고 그 이후 에러를 `throw`하게 될 것입니다.

우리는 그 에러를 `try...catch`문을 이용하여 잡아낼 수 있습니다. 일반적인 `throw`와 동일합니다.

```js
async function f() {
  try {
    let response = await fetch('http://no-such-url');
  } catch (err) {
    alert(err); // TypeError: failed to fatch
  }
}

f();
```

에러의 경우, 제어가 `catch` 블록으로 넘어갑니다. 우리는 `try...catch`를 통해 감싸줄 수 있습니다.

```js
async function f() {
  try {
    let response = await fetch('/no-user-here');
    let user = await response.json();
  }catch (err) {
    // catches errors both in fetch and response.json
    alert(err);
  }
}
```

만일 우리에게 `try...catch`문이 없었다면, 비동기 함수 `f()`의 호출에 의해 생성된 promise는 그냥 reject될 것입니다. 우리는 그러한 흐름을 제어하기 위해 `.catch`를 붙일 수 있습니다.

```js
async function f() {
  let response = await fetch('http://no-such-url');
}

// f() becomes a rejected promise
f().catch(alert); // TypeError: failed to fetch // (*)
```

만일 우리가 `.catch`를 거기에 추가하는 것을 잊었다면, 우리는 제대로 제어되지 않는 promise 에러를 갖게 되는 것입니다. (콘솔에서는 볼 수 있습니다.) 전역 이벤트 핸들러를 사용하여 이러한 에러를 잡아낼 수 있습니다. [Error handling with promise](https://javascript.info/promise-error-handling) 챕터에서 그러한 내용을 볼 수 있습니다.

> `async/await` 그리고 `promise.then/catch`

우리가 `async/await`을 사용할 때, 우리는 간혹 `.then`이 필요합니다 왜냐하면 `await`은 우리를 위한 작업 대기를 처리합니다. 그리고 우리는 `.catch` 대신에 일반적인 `try...catch` 구문을 사용할 수 있습니다. 항상은 아닐지라도 일반적인 구문을 사용하는 것이 편리한 경우가 더 많습니다.

>`async/await`은 `Promise.all`과 잘 작동합니다.

다수의 promise를 기다려야 할 필요가 있을 때, 우리는 그것들을 `Promise.all`로 묶어주고 `await`을 걸 수 있습니다.
소스코드는 다음과 같이 나올 것입니다.

```js
// 배열의 결과를 기다립니다.
let results = await Promise.all([
  fetch(url1),
  fetch(url2),
  ...
]);
```

에러가 난 경우에는, 일반적인 케이스와 같이 전파됩니다: 실패한 promise에서 `Promise.all`로 넘기고, 우리가 `try...catch`를 이용하여 캐치할 수 있는 예외가 됩니다.

### 요약

함수 전의 `async` 키워드는 2가지 효과를 갖습니다.

1. 언제나 promise를 반환합니다.
2. 함수 내부에서 `await`을 사용할 수 있게 해줍니다.

promise 앞의 `await` 키워드는 자바스크립트가 해당 promise가 끝날 때까지 잠시 기다리게 합니다. 그리고

1. 에러가 발생한 경우, 예외가 만들어지고, 그 자리에서 `throw error`가 호출된 것처럼 동작합니다.
2. 에러가 발생하지 않았다면, 결과를 반환합니다. 그래서 우리가 그 결과 값을 변수에 넣을 수 있습니다.

`async/await`은 읽기 쉽고 쓰기 쉬운 비동기 코드를 작성하는데 좋은 프레임워크입니다.

`async/await`과 함께, 우리는 가끔 `promise.then/catch`를 작성할 필요가 있습니다. 하지만 우리는 여전히 그들이 promise 기반이라는 것을 잊어선 안됩니다. 왜냐하면 때때로 (예를 들면, 가장 바깥 스코프에서) 우리는 그 메소드들을 써야 합니다. 또한 `Promise.all`은 여러 작업들을 일제히 기다리는데 사용하기 매우 좋은 문법입니다.

## 과제

### 1. async/await을 이용하여 재작성하기

아래 예제는 Promise Chaining의 예제입니다. `.then/catch` 대신에 `async/await`을 이용하여 재작성해보세요.

```js
function loadJson(url) {
  return fetch(url)
    .then(response => {
      if (response.status === 200) {
        return response.json();
      } else {
        throw Error(response.status);
      }
  });
}

loadJson('no-such-user.json')
  .catch(alert); // Error: 404
```

> 정답은 

> .

> .

> .

> .

> .

```js
async function loadJson(url) { // (1)
  let response = await fetch(url); // (2)
  
  if(response.status === 200) {
    return response.json(); // (3)
  }
  
  throw new Error(response.status);
}

loadJson('no-such-user.json')
  .catch(alert); // Error : 404 (4)
```

여러분이 알아야 할 것은
1. `loadJson` 함수가 `async` 함수가 되었습니다.
2. 모든 `.then`이 `await`으로 교체되었습니다.
3. `return reponse.json()`을 `await`하지 않고 할 수 있습니다.
4. `loadJson`으로부터 던져진 에러는 `.catch`에 의해 처리됩니다. 여기서는 `await loadJson(...)`을 사용할 수 없습니다. 우리는 `async` 함수 내부에 있지 않기 때문입니다.

### 2. "rethrow"를 async/await으로 재작성하기

아래에서 "rethrow" 예제를 확인할 수 있습니다. `.then/catch` 대신에 `async/await`을 이용하여 재작성해보세요.

그리고 루프를 선호하는 `modeGithubUser` 내부의 재귀를 없애보세요. 그리고 `async/await`을 써보세요. 훨씬 하기 쉬울 것입니다.

```js
class HttpError extends Error {
  constructor(response) {
    super(`${response.status} for ${response.url}`);
    this.name = 'HttpError';
    this.response = response;
  }
}

function loadJson(url) {
  return fetch(url)
    .then(response => {
      if(response.status === 200) {
        return response.json();
      } else {
        throw new HttpError(response);
      }
    });
}

// 깃허브가 유효한 사용자를 반환할 때까지 사용자 이름을 요청해보세요.
function demoGithubUser() {
  let name = prompt("Enter a name?", "iliakan");
  
  return loadJson(`https://api.github.com/user/${name}`)
    .then(user => {
      alert(`Full name: ${user.name}.`);
      return user;
    })
    .catch(err => {
      if (err instanceof HttpError && err.response.status === 404) {
        alert("No such user, please reenter.");
        return demoGithubUser();
      } else {
        throw err;
      }
    });
}

demoGithubUser();
```

> 정답은 

> .

> .

> .

> .

> .

트릭은 없습니다. 그냥 대체하는 겁니다. `.catch`를 `try...catch`로 대체하고 `async/await`을 필요한 곳에 넣어주면 됩니다.

```js
class HttpError extends Error {
  constructor(response) {
    super(`${response.status} for ${response.url}`);
    this.name = 'HttpError';
    this.response = response;
  }
}

async function loadJson(url) {
  let response = await fetch(url);
  
  if(response.status === 200) {
    return response.json();
  }
  
  throw new HttpError(response);
}

async function demoGithubUser() {
  let name = prompt("Enter a name?", "iliakan");
  
  let user;
  while(true) {
    try{
      user = await loadJson(`https://api.github.com/user/${name}`);
      break;
    } 
    catch (err) {
      if (err instanceof HttpError && err.response.status === 404) {
        // 루프는 alert 이후에 계속됩니다.
        alert("No such user, please reenter.");
      } else {
        // 알 수 없는 에러가 날 시에, rethrow
        throw err;
      }
    }
  }
  
  alert(`Full name: ${user.name}.`);
  return user;
}

demoGithubUser();
```

### 3. async가 아닌 함수에서 async 함수 호출하기

우리는 "일반" 함수를 갖고 있습니다. 어떻게 `async`를 일반 함수에서 호출할까요? 그리고 결과를 어떻게 이용할 수 있을까요?

```js
async function wait() {
  await new Promise(resolve => setTimeout(resolve, 1000));
  
  return 10;
}

function f() {
  // ... 여기에 무엇을 적어야 할까요?
  // 우리는 async wait()을 호출하고 10을 반환받을 때까지 기다려야 합니다.
  // 우리가 "await" 쓸 수 없음을 기억하세요.
}
```

P.S. task는 기술적으로 매우 쉽습니다 하지만 async/await을 처음 다뤄보는 개발자들에게 이 질문은 매우 일반적입니다.

> 정답은 

> .

> .

> .

> .

> .

```js
async function wait() {
  await new Promise(resolve => setTimeout(resolve, 1000));
  
  return 10;
}

function f() {
  wait().then(result => alert(result));
}

f();
```


수고하셨습니다.
