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
- 개발자로서 우리는 항상 모든 데이터를 반환하는 정확한 메소드를 알아둘 필요가 있습니다. 이 경우에는, `getAllAthors`라는 이름의 메소드입니다.
- `getAllAuthors`는 모든 author의 문자열의 배열을 반환합니다. 만일, 다른 개발자가 오브젝트를 다음과 같은 포맷으로 반환하면 어떻게 할까요?

```js
[ {name: 'Agatha Christie'}, {name: 'J. K. Rowling'}, ...]
```

개발자는 반드시 모든 데이터를 반환하는 메소드의 **정확한 이름과 반환 타입**을 알아야 할 것입니다.

만일, 우리가 메소드의 **이름과 반환 타입이 고정되어 있고 변하지 않는다는 규칙**을 만들면 어떨까요?

이 메소드를 바로 ***iteratorMethod***라고 합니다.

이와 비슷하게 사용자 정의 오브젝트를 반복하는 프로세스의 표준화가 **ECMA**에 의해 진행되었습니다. 하지만, `iteratorMethod`라는 이름을 사용하는 대신에, ECMA는 `Symbol.iterator`라는 이름을 사용했습니다. **Symbol**은 유일(unique)한 이름을 제공합니다. 그리고 다른 프로퍼티 이름과 충돌이 발생하지 않습니다. 또한, **`Symbol.iterator`는 `iterator`라 불리는 오브젝트를 반환합니다.** 이 `iterator`는 `next`라 불리는 메소드를 가질 것입니다. `iterator`는 또한 `value`와 `done`이라는 키를 가진 오브젝트입니다.

`value` 키는 현재의 값을 포함할 것입니다. 이 값은 어떠한 타입이든 될 수 있습니다. `done`은 `boolean`입니다. `done`은 모든 값이 전달(fetched)되었는지 아닌지를 나타냅니다.

아래의 그림이 **iterables, iterators, next** 사이의 관계를 알아보는데 도움을 줄 수 있습니다. **이 관계를 Iteration Protocol(반복 프로토콜)이라고 부릅니다.**

![iteration1.png](https://images.velog.io/post-images/jakeseo_me/f0d55250-9bec-11e9-8e89-732c0e0c57cd/iteration1.png)

>이 그림은 Dr Axel Rauschmayer의 책 **Exploring JS**에 나오는 내용입니다.

- **iterable**은 자신의 원소들이 외부에서 접근 가능하도록 만들길 원하는 자료 구조입니다. 키가 `Symbol.iterator`인 메소드를 구현함으로써 원소들이 외부에서 접근 가능하도록 만듭니다. `Symbol.iterator` 메소드는 **iterator**를 위한 공장이라고 보면 됩니다. **iterator**들을 만들어냅니다.
- **iterator**는 자료 구조의 원소들을 순회할 수 있는 포인터입니다.

### 오브젝트를 반복 가능(iterable)하게 만들어보기

이전 섹션에서 배웠듯이, 우리는 `Symbol.iterator` 라 불리는 메소드를 구현할 필요가 있습니다. [computed property 문법](http://es6-features.org/#ComputedPropertyNames)을 이용하여 키를 설정해봅시다. 아래에 짧은 예제가 있습니다.

![iterator2.png](https://images.velog.io/post-images/jakeseo_me/3eace230-9bee-11e9-88b1-8170e490bd45/iterator2.png)
> iterator의 예제

4번째 줄에서, 우리는 iterator를 만들었습니다. `next` 메소드가 정의된 오브젝트입니다. `next` 메소드는 `step` 변수에 따라 값을 반환합니다. 25번째 줄에서 우리는 `iterator`를 가져옵니다. 27번째 줄에서, 우리는 `next`를 호출합니다. `done`이 `true`가 될 때까지 next를 계속하여 호출합니다. 

이게 바로 `for-of` 반복 내부에서 일어나는 일입니다. `for-of` 반복은 **iterable**한 것을 인자로 받아서 **iterator**로 만듭니다. 그리고 `next()`메소드를 `done`이 true가 될 때까지 호출합니다.

### 자바스크립트 내에서 반복 가능(Iterable)한 것들

자바스크립트에서 많은 것들이 반복 가능합니다. 즉시 보이진 않을 수 있어도, 면밀하게 조사해보면, 반복 가능(iterable)한 것들이 보이기 시작합니다.

**다음에 나열된 것들은 전부 반복 가능(Iterable)한 것들입니다.**

- **배열**과 **[타입이 정해진 배열](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)**
- **문자열** - 각 문자 또는 [유니코드](https://developer.mozilla.org/en-US/docs/Glossary/Unicode) 코드-포인트를 반복합니다.
- **맵** - 키-값 쌍을 반복합니다.
- **셋** - 원소를 반복합니다.
- `arguments` - 함수의 배열과 같은 특별한 변수를 반복합니다.
- DOM 원소들 

**자바스크립트에서 몇몇 반복가능한 것(iterables)을 인자로 사용하는 생성자들은 다음과 같습니다.**

- `for-of` 반복 - `for-of` 반복은 반복 가능한 것을 필요로 합니다. 반복이 불가능하다면, `for-of`는 `TypeError`를 던질 것입니다.

```js
for (const value of iterable) { ... }
```

- **배열의 비구조화** - 비구조화는 반복 가능(iterable)하기에 일어납니다. 정확히 어떤 일이 일어나는지 살펴봅시다.

```js
const array = ['a', 'b', 'c', 'd', 'e'];
const [first, ,third, ,last] = array;
```

위의 코드는

```js
const array = ['a', 'b', 'c', 'd', 'e'];
const iterator = array[Symbol.iterator]();
const first = iterator.next().value
iterator.next().value // Since it was skipped, so it's not assigned
const third = iterator.next().value
iterator.next().value // Since it was skipped, so it's not assigned
const last = iterator.next().value
```



## Generator

ES6는 **Generator (또는 Generator 함수)** 형태에서 함수와 Iterator를 다루는 방법을 새롭게 소개했습니다. 제너레이터는 함수를 **중간에서** 멈추고, *다시 멈췄던 부분부터* 실행할 수 있게 합니다. **요약하면, Generator는 함수의 형태를 띄지만, Iterator처럼 동작합니다.**

**재미있는 사실은** `async/await`이 Generator를 기반으로 한 것이라는 겁니다. [여기](https://tc39.github.io/ecmascript-asyncawait/)에서 더 자세히 읽어보세요.

Generator는 Iterator와 복잡하게 연결되어 있습니다.
