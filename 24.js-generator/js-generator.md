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

이제, `myFavouriteAuthors`는 또 다른 객체인 `allAuthors`를 가진 객체입니다. `allAuthors`는 `fiction`, `scienceFiction`, `fantasy` 라는 키를 가진 3개의 배열로 이루어져 있습니다. **지금 여러분에게 모든 작가 정보를 가져오기 위해서 `myFavouriteAuthors`에 대한 루프를 돌아보라고 하면, 여러분의 접근법은 어떻게 될까요?** 모든 데이터를 가져오기 위해서 반복을 조합하려 할 것입니다.

만일, 이전처럼 하려고 한다면...

```js
for (let author of myFavouriteAuthors) {
  console.log(author);
}

// 타입 에러: {} 는 반복할 수 없습니다.
```

여러분은 객체(Object)를 *반복할 수 없다(not iterable)*는 `TypeError`를 만나게 될 것입니다. **반복할 수 있다는 것(Iterable)이 어떤 의미인지 살펴보고 어떻게 객체(Object)를 반복할 수 있게 만들 수 있는지 알아봅시다.** 이 글 마지막에서, 여러분은 `for-of` 루프를 사용자 정의 객체에 어떻게 적용하는지 알게될 것입니다. 그리고 이제 `myFavouriteAuthors`를 반복 가능(Iterable)하게 만들어봅시다.

### 반복 가능한 것(Iterable)과 Iterator

이전 섹션에서 우리에게 어떤 문제가 발생했는지는 알았을 것입니다. 사용자 정의 객체에서 'author'에 들어있는 목록들을 가져올 수 있는 쉬운 방법이 없었습니다. 내부 데이터를 차례로 밖으로 노출시키는 방법을 통한 어떤 메소드를 원합니다. 

메소드 `myFavouriteAuthors` 내부에 `getAllAuthors` 메소드를 추가해봅시다. 이 메소드는 모든 author를 반환할 것입니다. 다음과 같이요.

![getAllAuthors.png](https://images.velog.io/post-images/jakeseo_me/9b3ddee0-932f-11e9-8bae-714c47bf13a6/getAllAuthors.png)
> getAllAuthors 구현

위에 나온 구현 방법은 정말 간단한 접근법입니다. 모든 author를 가져오긴 합니다. 하지만, 아직 몇가지 문제들이 이 구현된 코드 속에 남아있습니다. 몇가지는 다음과 같습니다.

- `getAllAuthors`는 매우 제한적입니다. 만일 어떤 사람이 `myFavouriteAuthors`를 만든다면, 그 사람은 이 메소드의 이름을 `retrieveAllAuthors`로 바꿀 것입니다.
- 

## Generator

ES6는 **Generator (또는 Generator 함수)** 형태에서 함수와 Iterator를 다루는 방법을 새롭게 소개했습니다. 제너레이터는 함수를 **중간에서** 멈추고, *다시 멈췄던 부분부터* 실행할 수 있게 합니다. **요약하면, Generator는 함수의 형태를 띄지만, Iterator처럼 동작합니다.**

**재미있는 사실은** `async/await`이 Generator를 기반으로 한 것이라는 겁니다. [여기](https://tc39.github.io/ecmascript-asyncawait/)에서 더 자세히 읽어보세요.

Generator는 Iterator와 복잡하게 연결되어 있습니다.
