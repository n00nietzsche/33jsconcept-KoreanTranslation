## 들어가기 전에

- 이 포스팅은 https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e, https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e, https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #24 자바스크립트 : 예제와 함께 자바스크립트 ES6 iterator, Generator 이해하기

## Iterator

자바스크립트의 **Iterator**를 분석해봅시다. **Iterator는 자바스크립트의 collection을 반복하는 새로운 방법입니다.** ES6에서 소개된 개념이고 매우 유용하고 많은 곳에서 사용되고 있기 때문에 인기가 많습니다.

우리는 개념적으로 Iterator가 어떤 것인지 이해하고 예제와 함께 어디에 사용해야 하는지 배워볼 것입니다. 그리고 자바스크립트 내부에서 Iterator가 구현된 곳도 살펴볼 것입니다.

### 소개

다음과 같은 배열이 있다고 가정해봅시다.

```js
const myFavouriteAuthors = [
  'Neal Stephenson',
  'Arthur Clarke',
  'Isaac Asimov', 
  'Robert Heinlein'
];
```

우리가 주로 하고 싶은 작업들은 배열의 값들을 꺼내서 화면에 출력하거나, 배열 값들을 수정하거나, 또는 배열 값들을 이용하여 무언가를 수행하고 싶어하겠죠. 만일 제가 여러분들에게 어떻게 할 것이냐고 물어본다면, 아마 `for`, `while`, `for-of`를 이용하여 반복문을 돌려 작업을 수행할 것이라고 말하는 사람들이 있을 겁니다. 예제의 구현은 다음과 같을 것입니다.

![loop1.png](https://images.velog.io/post-images/jakeseo_me/727dc720-922d-11e9-add5-3155c3e0f52d/loop1.png)
> 배열에서의 다양한 반복 테크닉

이제, 여러분이 이전의 배열과 같은 형태의 데이터가 아닌, 아래와 같이 사용자 설정의 데이터 구조를 가진 데이터를 갖고 있다고 생각해보면...

![custom_data.png](https://images.velog.io/post-images/jakeseo_me/d4bf20f0-922d-11e9-86db-31f8b943b3f7/customdata.png)
> Custom Data Structure



## Generator

ES6는 **Generator (또는 Generator 함수)** 형태에서 함수와 Iterator를 다루는 방법을 새롭게 소개했습니다. 제너레이터는 함수를 **중간에서** 멈추고, *다시 멈췄던 부분부터* 실행할 수 있게 합니다. **요약하면, Generator는 함수의 형태를 띄지만, Iterator처럼 동작합니다.**

**재미있는 사실은** `async/await`이 Generator를 기반으로 한 것이라는 겁니다. [여기](https://tc39.github.io/ecmascript-asyncawait/)에서 더 자세히 읽어보세요.

Generator는 Iterator와 복잡하게 연결되어 있습니다.
