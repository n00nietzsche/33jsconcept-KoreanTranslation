## 들어가기 전에

- 이 포스팅은 https://scotch.io/tutorials/javascript-promises-for-dummies#toc-new-kid-on-the-block-observables 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://scotch.io/tutorials/javascript-promises-for-dummies#toc-new-kid-on-the-block-observables If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #25 자바스크립트 : 바보를 위한 Promise

**Javascript Promise** 는 사실 어렵지 않습니다. 하지만, 처음에는 많은 사람들이 Promise를 이해하기 어렵다고 생각합니다. 그래서, 저자는 어떻게 Promise를 이해했는지 한번 그대로 적어보겠습니다. 바보가 이해했던 그 방법을요.

## Promise 이해하기

Promise는 요약하자면 다음과 같습니다.

"여러분이 **아이**라고 상상해보세요. 여러분의 어머니가 **새로운 스마트폰**을 다음 주에 사주기로 약속합니다."

여러분은 다음 주에 정말로 새로운 스마트폰을 가질 수 있을지 *알 수 없습니다.* 어머니는 진짜로 새로운 스마트폰을 *사줄 수도 있고,* 만일 기분이 좋지 않다면, 약속을 어기고, 스마트폰을 사주는 것을 잠시 *보류할 수도 있습니다.*

이게 바로 **Promise(약속)** 입니다. Promise는 3가지 상태를 가집니다.

1. Pending(미결) : 여러분은 새로운 스마트폰을 가질 수 있을지 *알 수 없습니다.*
2. Fulfilled(이행) : 어머니의 기분이 괜찮아서, 스마트폰을 사줬습니다.
3. Rejected(거절) : 어머니가 기분이 괜찮지만, 스마트폰은 사주지 않기로 했습니다.

## Promise 만들기

이제 위에서 배웠던 내용을 자바스크립트 코드로 만들어봅시다!

```js
// ES 5 //
var is MomHappy = false;

// Promise
var willIGetNewPhone = new Promise(
  function (resolve, reject) {
    if(isMomHappy) {
      var phone = {
        brand: 'Samsung',
        color: 'black'
      };
      resolve(phone); //fulfilled
    }
    else {
      var reason = new Error('mom is not happy');
      reject(reason); //reject
    }
  }
);
```

코드만 봐도 대략 무슨 내용인지 알기 쉽습니다.

```js
// promise의 문법은 대략 다음과 같이 생겼습니다.
new Promise(/_ executor _/ function (resolve, reject) { ... });
```

## Promise 사용하기

이제 Promise를 만들어보았으니, 사용해봅시다.

```js
// ES 5 //
...

// Promise 호출
var askMom = function () {
  willGetNewPhone
    .then(function (fulfilled) {
      // 와! 새 폰을 얻었다!
      console.log(fulfilled);
    // output: { brand: 'Samsung', color: 'black' }
    })
    .catch(function (error) {
      // 이런. 엄마가 폰을 안사주네..
      console.log(error.message);
    // output: 'mom is not happy'
    });
}
```

예제를 실행해보고 결과를 봅시다.

데모는 [여기서](https://jsbin.com/nifocu/1/edit?js,console) 할 수 있습니다.

![promisetest1.webp](https://images.velog.io/post-images/jakeseo_me/877b5a80-ace4-11e9-9e4b-3bee38b6a611/promisetest1.webp)

## Promise 연계(Chaining)하기

Promise는 연계 가능(Chainable)합니다.

여러분은 **아이(Kid)** 입니다. 새로운 스마트폰을 사면 친구들에게 보여주기로 **약속(Promise)** 했다고 생각해봅시다.

새로운 Promise를 작성해봅시다. 

```js
...

// 2nd promise
var showOff = function (phone) {
  return new Promsie(
    function (resolve, reject) {
      var message = 'Hey friend, I have a new ' +
          phone.color + ' ' + phone.brand + ' phone';
      
      resolve(message);
    };
  );
};
```

더 짧게 작성하면 다음과 같습니다.

```js
// shorten it
...

// 2nd promise
var showOff = function (phone) {
  var message = 'Hey friend, I have a new ' + 
              phone.color + ' ' + phone.brand + ' phone';
  
  return Promise.resolve(message);
};
```

이제 Promise를 연계해봅시다. `willGetNewPhone` Promise를 수행한 이후에만, `showOff` Promise를 수행할 수 있습니다.

```js
...

// call our promise
var askMom = function () {
  willGetNewPhone
  .then(showOff) // 여기서 연계합니다.
  .then(function (fulfilled) {
    console.log(fulfilleded);
    // output: 'Hey friend, I have a new black Samsung phone.'
  })
  .catch(function (error) {
    // oops, mom don't buy it
    console.log(error.message);
  // output: 'mom is not happy'
  });
};
```

Promise를 연계하는 것은 이렇게나 쉽습니다.

## Promise는 비동기(Asynchronous)다.

Promise는 비동기입니다. Promise를 호출한 전과 이후에 메시지를 로깅해봅시다.

```js
var askMom = function () {
  console.log('before asking Mom'); // log before
  willGetNewPhone
    .then(showOff)
    .then(function (fulfilled) {
      console.log(fulfilled);
    })
    .catch(function (error) {
      console.log(error.message);
    });
  console.log('after asking Mom');
}
```

출력 순서가 어떻게 될까요? 맞춰보세요.

아마 다음과 같을 수 있겠죠?

```
1. before asking mom
2. Hey friend, I have a new black Samsung phone.
3. after asking mom
```

하지만 사실 실제 출력은 다음과 같이 이뤄집니다.

```
1. before asking mom
2. after asking mom
3. Hey friend, I have a new black Samsung phone.
```

![promisetest2.webp](https://images.velog.io/post-images/jakeseo_me/03b123e0-acf0-11e9-bfa7-2912c87d7587/promisetest2.webp)

**왜냐구요? JS는 누구도 기다려주지 않으니까요.**

여러분도 어머니가 새로운 스마트폰을 사준다는 약속을 계속 기다리기만 하진 않을 것입니다. 그 동안 밖에 나가서 뛰어 놀기도 하고 그러겠죠? 아닌가요? 그게 우리가 말하는 **비동기(Asynchronous)** 입니다. 코드는 어떠한 방해나 결과에 대한 기다림 없이 돌아갈 것입니다. Promise는 오직 코드가 흘러가길 기다릴 뿐입니다. 여러분은 `.then`을 작성하여 코드가 흘러갔을 때 추가적으로 해야할 일을 코딩할 수 있습니다.

## ES5, ES6/2015, ES7에서의 Promise

### ES5 - 주류 브라우저들

위에 작성했던 데모 코드들은 만일 여러분들이 [Bluebird](http://bluebirdjs.com/docs/getting-started.html) Promise 라이브러리만 설치했다면, ES5 환경(모든 주류 브라우저 + 노드JS 환경을 말합니다.)에서 돌아갑니다. ES5는 자체적으로는 Promise를 지원하지 않습니다. Bluebird외에도 다른 유명한 Promise 라이브러리인 [Q](https://github.com/kriskowal/q)가 있으니 참고하세요.

### ES6 / ES2015 - 현대 브라우저들과 NodeJs v6

위의 작성했던 데모 코드들이 라이브러리 없이도 작동합니다. 왜냐하면 ES6는 Promise를 네이티브하게 지원하기 때문입니다. 추가로 ES6 함수들과 함께라면, 화살표 함수를 이용하여 코드를 훨씬 더 간단히 만들 수 있습니다. 그리고 `const`나 `let`과 같은 선언문으로 변수 선언도 가능합니다.

ES6의 코드 예제는 다음과 같습니다.

```js
// ES 6 //
const isMomHappy = true;

// Promise
const willGetNewPhone = new Promise(
  (resolve, reject) => { // 화살표 함수
    if (isMomHappy) {
      const phone = {
        brand: 'Samsung',
        color: 'black'
      };
      resolve(phone);
    }
    else {
      const reason = new Error('mom is not happy');
      reject(reason);
    }
  }
);

const showOff = function (phone) {
  const message = 'Hey friend, I have a new ' +
            phone.color + ' ' + phone.brand + ' phone';
  return Promise.resolve(message);
};

// call our promise
const askMom = function () {
  willGetNewPhone
    .then(showOff)
    .then(fulfilled => console.log(fulfilled))
    .catch(error => console.log(error));
};

askMom();
```

### ES7 - Async Await이 문법을 더 예쁘게 만들어줍니다.

ES7은 `async`와 `await` 문법을 도입했습니다. 두가지 문법은 비동기 문법을 더 예쁘고 이해하기 쉽게 만들어줍니다. 심지어 `.then`과 `.catch`도 필요 없습니다.

우리 예제를 ES7으로 재작성해봅시다.

```js
/_ ES7 _/
const isMomHappy = true;

// Promise
const willIGetNewPhone = new Promise(
    (resolve, reject) => {
        if (isMomHappy) {
            const phone = {
                brand: 'Samsung',
                color: 'black'
            };
            resolve(phone);
        } else {
            const reason = new Error('mom is not happy');
            reject(reason);
        }

    }
);

// 2nd promise
async function showOff(phone) {
    return new Promise(
        (resolve, reject) => {
            var message = 'Hey friend, I have a new ' +
                phone.color + ' ' + phone.brand + ' phone';

            resolve(message);
        }
    );
};

// call our promise
async function askMom() {
    try {
        console.log('before asking Mom');

        let phone = await willIGetNewPhone;
        let message = await showOff(phone);

        console.log(message);
        console.log('after asking mom');
    }
    catch (error) {
        console.log(error.message);
    }
}

(async () => {
    await askMom();
})();
```

## 왜 Promise이고 언제 써야 할까요?

우리는 왜 Promise가 필요할까요? Promise가 있기 전에는 어떻게 코딩을 했을까요? 이러한 지룸넹 대답하기 전에 다시 근본적인 부분으로 넘어가봅시다.

### 일반 함수 vs 비동기 함수

두 가지 예제를 봅시다. 두 예제 모두 두 숫자를 더하는 예제입니다. 하나는 일반 함수를 이용해 더하고 하나는 원격에서 더합니다.

#### 일반 함수로 두 숫자 더하기

```js
// add two numbers normally

function add (num1, num2) {
    return num1 + num2;
}

const result = add(1, 2); // you get result = 3 immediately
```

#### 비동기 함수로 두 숫자 더하기

```js
// add two numbers remotely

// get the result by calling an API
const result = getAddResultFromServer('http://www.example.com?num1=1&num2=2');
// you get result  = "undefined"
```

여러분이 만일 일반 함수로 두 숫자를 더한다면, 결과를 즉시 볼 수 있을 것입니다. 하지만, 여러분이 원격 서버를 이용하여 두 숫자 더하기에 대한 결과 값을 구한다면, 여러분은 기다려야 합니다. 즉시 값을 얻진 못합니다.

또는 이렇게 설명할 수도 있습니다. 여러분은 서버가 다운되거나 응답 지연 때문에 값을 얻을 수 있을지 없을지도 모릅니다. 여러분은 서버가 값을 반환할 때까지 모든 프로세스를 잠시 멈춰두길 원하진 않을 것입니다. 그래서 비동기가 필요합니다.

API를 호추하는 일, 파일을 다운로드하는 일, 파일을 읽는 일 등은 주로 비동기 함수를 사용하여 코딩합니다.

### Promise가 있기 전에 코딩하던 방법 : Callback

우리가 반드시 비동기 호출을 사용해야 할까요? 정답은 아닙니다. Promise 전에 우리는 callback을 사용했습니다. Callback은 여러분이 결과 값을 받으면 수행할 함수입니다. callback의 개념을 받아들이기 위해 이전 예제를 수정해봅시다.

```js
// add two numbers remotely
// get the result by calling an API

function addAsync (num1, num2, callback) {
    // use the famous jQuery getJSON callback API
    return $.getJSON('http://www.example.com', {
        num1: num1,
        num2: num2
    }, callback);
}

addAsync(1, 2, success => {
    // callback
    const result = success; // you get result = 3 here
});
```

문법은 그럭저럭 괜찮아보입니다. 그렇다면 우리는 왜 Promise를 사용해야 할까요?
