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

### 자바스크립트 내에서 Iterable들

> Iterable은 영어단어로 반복 가능한이란 뜻을 가지는 형용사가 될 수 있지만, 여기서는 일종의 고유명사로 생각하셔야 합니다. Iterable이란 자체가 반복 가능한 어떠한 객체정도로 생각해주세요.

자바스크립트에서 많은 것들이 반복 가능합니다. 즉시 보이진 않을 수 있어도, 면밀하게 조사해보면, iterable이 보이기 시작합니다.

**다음에 나열된 것들은 Iterable입니다.**

- **배열**과 **[타입이 정해진 배열](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)**
- **문자열** - 각 문자 또는 [유니코드](https://developer.mozilla.org/en-US/docs/Glossary/Unicode) 코드-포인트를 반복합니다.
- **맵** - 키-값 쌍을 반복합니다.
- **셋** - 원소를 반복합니다.
- `arguments` - 함수의 배열과 같은 특별한 변수를 반복합니다.
- DOM 원소들 

**자바스크립트에서 iterables을 인자로 사용하는 생성자들은 다음과 같습니다.**

- `for-of` 반복 - `for-of` 반복은 반복 가능한 것을 필요로 합니다. 반복이 불가능하다면, `for-of`는 `TypeError`를 던질 것입니다.

```js
for (const value of iterable) { ... }
```

- **배열의 비구조화 (Destructuring of Arrays)** - 비구조화는 반복 가능(iterable)하기에 일어납니다. 정확히 어떤 일이 일어나는지 살펴봅시다.

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

- **전개 연산자 (Spread operator)**

다음 코드를 봅시다.

```js
const array = ['a', 'b', 'c', 'd', 'e'];
const newArray = [1, ...array, 2, 3];
```

위의 코드는 다음과 같이 쓰여질 수 있습니다.

```js
const array = ['a', 'b', 'c', 'd', 'e'];
const iterator = array[Symbol.iterator]();

const newArray = [1];
for (let nextValue = iterator.next(); nextValue.done !== true; nextValue = iterator.next()) {
  newArray.push(nextValue.value);
}

newArray.push(2)
newArray.push(3)
```

- `Promise.all` 과 `Promise.race` 역시 Promise 전반에서 iterable을 수용합니다.

- **Map과 Set**

Map의 생성자는 Iterable `[key, pair]`의 쌍을 Map으로 변화시킵니다. 그리고 Set의 생성자는 Iterable의 `[key, pair]`의 쌍을 Set으로 변화시킵니다.

```js
const map = new Map([[1, 'one'], [2, 'two']]);
map.get(1);
// one

const set = new Set(['a', 'b', 'c']);
set.has('c')
// true
```

- Iterator는 또한 Generator의 선배입니다.

### myFavouriteAuthors를 Iterable로 만들기
`myFavouriteAuthors`가 iterable이 된 구현이 다음 코드에 있습니다.

```js
const myFavouriteAuthors = {
  allAuthors: {
    fiction: [
      'Agatha Christie', 
      'J. K. Rowling',
      'Dr. Seuss'
    ],
    scienceFiction: [
      'Neal Stephenson',
      'Arthur Clarke',
      'Isaac Asimov', 
      'Robert Heinlein'
    ],
    fantasy: [
      'J. R. R. Tolkien',
      'J. K. Rowling',
      'Terry Pratchett'
    ]
  },
  [Symbol.iterator]() {
    // 모든 작가를 배열로 받기
    const genres = Object.values(this.allAuthors);
    
    // 현재의 장르와 작가 인덱스를 저장하기
    let currentAuthorIndex = 0;
    let currentGenreIndex = 0;
    
    return {
      // next() 구현
      next() {
        // 현재 장르 인덱스에 따른 작가들
        const authors = genres[currentGenreIndex];
        
        // doNotHaveMoreAuthors 는 Authors 배열을 전부 돌았을 때, 참이 됩니다.
        // 모든 아이템이 소비되었을 때, 참이 됩니다.
        const doNotHaveMoreAuthors = !(currentAuthorIndex < authors.length);
        if (doNotHaveMoreAuthors) {
          // 더 이상 불러올 작가가 없을 때, 다음 장르 인덱스(currentGenreIndex)가 다음으로 넘어갑니다.
          currentGenreIndex++;
          // 그리고 작가 인덱스(currentAuthorIndex)가 0이 됩니다.
          currentAuthorIndex = 0;
        }
        
        // 만일 모든 장르가 끝났다면,
        // 우리는 아이터레이터에게 더 이상 우리가 줄 값이 없다는 것을 알려야 합니다.
        const doNotHaveMoreGenres = !(currentGenreIndex < genres.length);
        if (doNotHaveMoreGenres) {
          return {
            value: undefined,
            done: true
          };
        }
        
        // 만일 모든 것들이 맞다면, 현재 장르로부터 작가 이름을 반환합니다.
        // 그리고 작가 인덱스를 하나 늘립니다(increment).
        // 다음에는, 다음 작가가 반환됩니다.
        return {
          value: genres[currentGenreIndex][currentAuthorIndex++],
          done: false
        }
      }
    };
  }
};

for (const author of myFavouriteAuthors) {
  console.log(author);
}

console.log(...myFavouriteAuthors);
```

이번 Iterator 설명 글에서 얻은 지식으로 여러분은 iterator가 어떻게 작동하는지 쉽게 알 수 있었을 것입니다. 로직은 따라가기 힘들 수도 있습니다. 그래서, 코드에 주석을 열심히 달아놓았습니다. 하지만 코드와 개념을 진정으로 이해하려면 브라우저나 노드에서 직접 코드를 실행해보시기 바랍니다.

## 제너레이터(Generator)

ES6는 **제너레이터(Generator) 또는 Generator 함수** 형태에서 함수와 Iterator를 다루는 방법을 새롭게 소개했습니다. 제너레이터는 함수를 **중간에서** 멈추고, *다시 멈췄던 부분부터* 실행할 수 있게 합니다. **요약하면, Generator는 함수의 형태를 띄지만, Iterator처럼 동작합니다.**

**재미있는 사실은** `async/await`이 Generator를 기반으로 한 것이라는 겁니다. [여기](https://tc39.github.io/ecmascript-asyncawait/)에서 더 자세히 읽어보세요.

Generator는 Iterator와 복잡하게 연결되어 있습니다.

Generator를 자세히 알아보기전에 다음 비유를 통해 Generator에 대해 직관적으로 알아봅시다.

여러분이 손톱을 물어뜯게하는 긴박감 넘치는 하이테크 추리소설을 읽고 있다고 상상해보세요. 책의 페이지에 엄청나게 집중하고 있고, 거의 초인종 소리도 못 들을 정도라고 생각해봅시다. 그 상황에서 갑자기 피자배달부가 왔습니다. 여러분은 문을 열어주기 위해서 자리에서 일어났습니다. **문을 열어주기 전에, 여러분은 여러분이 읽던 책 중간에 책갈피를 꽂아뒀습니다.** 여러분은 머릿속에 여러분이 읽고 있던 스토리를 잠시 기억해둡니다. 그리고 여러분은 문 밖으로 나가서 피자를 받습니다. 방에 다시 돌아왔을 때, 여러분은 여러분이 **책갈피를 꽂아둔 페이지부터** 다시 읽기 시작합니다. 여러분은 책을 첫 페이지부터 다시 읽을 필요가 없습니다. 이러한 방식으로 여러분은 이미 Generator 함수처럼 행동하고 있는 것입니다.

## 소개

프로그래밍 중에 발생하는 일반적인 문제를 해결하기 위해 어떻게 제너레이터를 활용할 수 있을지 봅시다. 하지만 그 전에, 제너레이터가 뭔지부터 알아봅시다.

### 제너레이터란 무엇인가?

아래 소스에 있는 함수와 같이 일반적인 함수들은 작업이 끝나기 전엔 끝낼 수 없습니다. 이러한 형태의 함수를 [run-to-complete](https://en.wikipedia.org/wiki/Run_to_completion_scheduling?source=post_page---------------------------) 모델이라고 합니다.

```js
function normalFunc() {
  console.log('I');
  console.log('cannot');
  console.log('be')
  console.log('stopped.')
}
```

`normalFunc`에서 탈출하는 유일한 방법은 `return` 영역에 들어가는 것입니다. 아니면 에러를 `throw`하거나요. 만일 여러분이 함수를 다시한번 호출하면, 함수는 **다시** 맨위 에서부터 실행을 시작할 것입니다.

반대로, 제너레이터는 **중간에 멈출 수 있는** 함수입니다. 그리고 멈춘 부분부터 다시 실행을 시작할 수 있습니다.

제너레이터의 일반적인 정의는 다음과 같습니다.

- 제너레이터는 아이터레이터 작성 작업을 간단하게 해줄 수 있는 함수들의 특별한 클래스입니다.
- 제너레이터는 하나의 값 대신에 결과의 순서를 생성하는 함수입니다. 이를테면 제너레이터는 값의 시리즈를 *만들어(generate)* 냅니다.

자바스크립트에서, 제너레이터는 `next()`를 호출할 수 있는 오브젝트를 반환하는 함수입니다. 여러분이 `next()` 호출을 할 때마다, 다음과 같은 형태의 오브젝트를 반환합니다.

```js
{
  value: Any,
  done: true|false
}
```

`value` 프로퍼티는 값을 가집니다. `done` 프로퍼티는 `true` 혹은 `false`를 갖습니다. `done`이 `true`가 될 때, 제너레이터는 멈추고 더 이상 값을 만들어내지 않습니다.

아래는 제너레이터를 설명한 그림입니다.

![GeneratorArchitecture.png](https://images.velog.io/post-images/jakeseo_me/6bc9dbc0-aae3-11e9-8649-35506338cef4/GeneratorArchitecture.png)
> 일반적인 함수 vs 제너레이터 함수

> 그림에 나온 메시지를 간단히 해석하자면, 제너레이터가 실행되다가 yield를 만나면 멈추고 그 구간을 빠져나오면 다시 시작되고 다시 yield를 만나면 멈춘다는 이야기입니다.

이미지의 제너레이터 부분을 끝내기 전에 **yield-resume-yield** 루프로 다시 연결되는 화살표를 잘 보세요. **제너레이터가 영영 끝나지 않는 경우의 수도 있습니다.** 이 예제는 다음에 다시 살펴봅시다.

### 제너레이터 만들어보기

자바스크립트에서 우리는 어떻게 제너레이터를 만들 수 있을까요?

```js
function * generatorFunction() { // Line 1
  console.log('This will be executed first.');
  yield 'Hello, '; // Line 2
 
  console.log('I will be printed after the pause');
  yield 'World!' ;
}

const generatorObject = generatorFunction(); // Line 3

console.log(generatorObject.next().value); // Line 4
console.log(generatorObject.next().value); // Line 5
console.log(generatorObject.next().value); // Line 6

// This will be executed first.(이게 아마 처음 실행될 것입니다.)
// Hello,
// I will be printed after the pause(정지 후엔 다음과 같은 말이 출력될 것입니다.)
// World!
// undeifned
```

위에서 **function *, yield, generatorObject, generatorObject.next** 부분을 유심히 봅시다. 위의 소스에서 우리는 `function`이라는 일반적인 함수 선언 대신에 `function *`이라는 문법을 사용해서 함수를 선언했습니다. `function` 키워드와 `*` 그리고 함수 이름 사이에는 얼만큼의 빈공간이든 들어올 수 있습니다. 왜냐하면 이건 그냥 함수이고, 함수가 사용되는 곳이면 어디서든 사용할 수 있습니다. 예를 들면, 오브젝트 내부 그리고 클래스 메소드로도 사용 가능합니다.

함수의 바디 부분 내부에서, 우리는 `return` 키워드를 사용하지 않습니다. 그 대신에, 우리는 `yield`라는 키워드를 대신 사용합니다. (Line 2) `yield`라는 키워드는 제너레이터가 멈추게 할 수 있는 연산자(operator)입니다. 제너레이터가 `yield`를 만날 때마다, 제너레이터는 `yield` 뒤에 기재된 값을 반환합니다. 이번 경우에는 `Hello, `라는 값이 반환되었습니다. 하지만, 우리는 제너레이터의 맥락에서는 "반환(리턴)되었다." 라는 표현을 쓰지 않습니다. 우리는 대신에 "제너레이터가 `Hello, `라는 값을 **yield(생산)** 했다." 라고 말합니다.

> yield는 한국어 단어 중 어떤 말로 써도 어색하기에 그냥 영어 문자 그대로를 가져가겠습니다.

제너레이터에서 물론 값을 그냥 반환(return)하는 것도 가능합니다. 하지만, `return`은 `done` 프로퍼티를 `true`로 설정합니다. 그래서 그 이후로는 제너레이터는 어떠한 값도 generate(생산)해낼 수 없습니다.

```js
function * generatorFunc() {
  yield 'a';
  return 'b'; // 제너레이터는 여기서 끝납니다.
  yield 'a'; // 영영 실행될 수 없습니다.
}
```

Line 3에서, 우리는 제너레이터 오브젝트를 만들었습니다. 이는 마치 우리가 `generatorFunction` 함수를 **호출하는 것처럼 보입니다.** 사실대로 말하자면 우리는 정말 그렇게 했습니다. 단지 차이점은 제너레이터는 값을 반환하는 대신에 항상 제너레이터 오브젝트를 반환한다는 것입니다. 제너레이터 오브젝트는 iterator입니다. 그래서 여러분들은 `for-of` 루프 내부에서 제너레이터를 사용할 수도 있습니다. 또는 다른 함수에서도 역시 iterator처럼 받아들여집니다.

Line 4에서, 우리는 `generatorObject` 내부에 존재하는 `next()` 메소드를 호출합니다. 이 호출로 인해, 제너레이터는 실행되기 시작합니다. 처음으로 제너레이터는 `console.log(This will be executed first.)`를 수행합니다. 그 이후에 제너레이터는 `yield 'Hello, '`를 마주하게 됩니다. 제너레이터는 `{ value: 'Hello, ', done: false }`와 같은 형태의 오브젝트 값을 yield하게 됩니다. 그리고 잠시 거기서 일시정지합니다. 그 후에 다음 호출을 기다립니다.

Line 5에서, 우리는 `next()`를 다시 한번 호출합니다. 제너레이터가 깨어나고 남은 부분을 마저 실행하기 시작합니다. 다음 줄에서 `console.log`가 나오고, `I will be printed after the pause `라는 텍스트를 `console.log`합니다. 그리고 또 다른 `yield`를 마주합니다. `{ value: 'World!', done: false }`와 같은 오브젝트 값이 yield됩니다. 우리는 `value` 프로퍼티를 추출해서 로깅합니다. 제너레이터는 다시 잠에 듭니다.

Line 6에서, 우리는 또 `next()`를 호출합니다. 이번에는 다음에 더이상 실행할 라인이 없습니다. 여러분이 기억해야 할 것은 모든 함수가 명시된 return값이 없다면 묵시적으로 `undefined`를 반환한다는 것입니다. 그러므로, 제너레이터는 yield하는 대신에, `{ value: undefined, done: true }` 형태의 오브젝트를 반환하게 됩니다. 이제, 이 제너레이터는 더 이상 값을 반환하거나 재실행 될 수 없습니다. 왜냐하면 더이상 실행할 구문이 남아있지 않기 때문입니다.

이제 다시 값을 생성하기 위해서는 새로운 제너레이터를 만들어야 합니다.

## 제너레이터의 쓰임

제너레이터는 다양한 방법으로 멋지게 쓰일 수 있습니다. 몇가지 예시를 보러갑시다.

### Iterable 수행하기

여러분이 iterator를 수행할 때, 여러분은 `next()` 메소드를 가진 iterator 오브젝트를 직접 만들어야 합니다. 또한, 여러분은 상태를 직접 저장해야 합니다. 가끔씩은, 정말 그렇게 하기 귀찮은 상황이 옵니다. 제너레이터는 iterable(반복 가능)하기 때문에, 제너레이터는 귀찮은 추가적인 보일러플레이트 코드 없이 iterable을 수행하는데에 사용될 수 있습니다. 간단한 예제를 봅시다.

문제: 우리는 커스텀 `This`, `is`, `iterable.`을 반환하는 iterable을 만들기를 원합니다. 여기 iterator를 이용한 예제가 있습니다.

```js
const iterableObj = {
  [Symbol.iterator]() {
    let step = 0;
    return {
      next() {
        step++;
        if (step === 1) {
          return { value: 'This', done: false };
        }
        else if (step === 2) {
          return { value: 'is', done: false };
        }
        else if (step === 3) {
          return { value: 'iterable.', done: false };
        }
        return { value: '', done: true };
      }
    }
  }
}

for (const val of iterableObj) {
  console.log(val);
}

// This
// is
// iterable.
```

제너레이터를 이용하면 다음과 같은 코드로 작성 가능합니다.

```js
function * iterableObj() {
  yield 'This';
  yield 'is';
  yield 'iterable.';
}

for (const val of iterableObj()) {
  console.log(val);
}

// This
// is
// Iterable.
```

두가지 버전을 비교해보세요. 사실 약간 억지 느낌이 있는 것은 사실이지만, 다음과 같은 사실들을 살펴볼 수 있습니다.

- `Symbol.iterator`를 생각할 필요가 없습니다.
- `next()`를 구현할 필요가 없습니다.
- `next()` 내부에 쓰이는 `{ value: 'This', done: false }`와 같은 반환 오브젝트에 대해 작성할 필요도 없습니다.
- 상태를 저장할 필요도 없습니다. iterator 예제에서는, 상태가 `step`이라는 변수에 저장되었습니다. `step`은 iterable로부터 무엇이 결과물로 나왔는지를 정의하는 값이었습니다. 제너레이터에서는 이러한 귀찮은 작업들이 필요 없습니다.

### 더 나은 비동기 함수성

promise와 콜백을 사용한 코드가 가능합니다.

```js
function fetchJson(url) {
  return fetch(url)
  .then(request => request.text())
  .then(text => {
    return JSON.parse(text);
  })
  .catch(error => {
    console.log(`ERROR: ${error.stack}`);
  });
}
```

위 코드는 다음과 같이 쓰여질 수 있습니다. ([co.js](https://github.com/tj/co?source=post_page---------------------------)라는 라이브러리의 도움을 조금 받습니다.)

```js
const fetchJson = co.wrap(function * (url) {
  try {
    let request = yield function(url);
    let text = yield request.text();
    return JSON.parse(text);
  }catch (error) {
    console.log(`ERROR: ${error.stack}`);
  }
});
```

몇몇 독자들은 위의 코드를 보고 "`async/await`의 사용을 나열해놓은 것 같은데?" 라고 생각할 수도 있습니다. 사실 틀린 생각이 아닙니다. `async/await`은 비슷한 전략을 따라갑니다. `async/await`은 promise가 있고, `yield`를 `await`으로 교체합니다. `async/await`은 generator를 바탕으로 할 수 있습니다. 더 많은 정보를 얻기 위해서는 [이 코멘트](https://medium.com/@pawk/hey-great-article-few-remarks-here-ae93d827e47b)를 보시면 도움이 될 것입니다.

### 끊이지 않는 데이터 스트림

영원히 끝나지 않는 제너레이터를 만드는 것도 가능합니다. 다음 예제를 봅시다.

```js
function * naturalNumbers() {
  let num = 1;
  while (true) {
    yield num;
    num = num + 1;
  }
}

const numbers = naturalNumbers();

console.log(numbers.next().value);
console.log(numbers.next().value);

// 1
// 2
```

우리는 `naturalNumbers`라는 generator를 만들었습니다. 함수 내부에서, 우리는 무한한 `while` 루프를 갖습니다. 그 루프 내부에서, 우리는 `num`을 `yield`합니다. generator가 yield할 때, 함수는 잠시 정지(suspended)됩니다. 우리가 `next()`를 다시 호출할 때, generator는 다시 깨어나고 멈췄던 부분부터 다시 동작합니다.(정확히 말하자면 `yield`다음부터 입니다.) 그리고 또 다른 `yield`를 만날 때까지 계속 실행합니다. 만일 또 다른 `yield`를 만나지 못한다면, generator를 종료합니다. 이번 경우에, 다음 문장은 `num = num + 1`이기 때문에, `num`을 업데이트 합니다. 그 후에, while loop의 맨 위로 갑니다. 조건이 여전히 `true`여서 계속 실행됩니다. 이 함수는 또 다음 줄의 `yield num`을 만납니다. 그 이후에 업데이트된 `num`을 `yield`하고, 정지합니다. 여러분이 원하는만큼 이러한 동작을 반복합니다. 

### Observer(관찰자)로서의 Generator

Generator는 `next(val)`함수를 사용하여 값을 받을 수 있습니다. generator가 새로운 값을 받을 때, 깨어나기 때문에, generator는 observer로도 불립니다. 이러한 동작은 값을 *지켜보다(observing)* 가 generator가 값을 가졌을 때, generator가 동작한다고 생각될 수 있습니다. [여기](http://exploringjs.com/es6/ch_generators.html?source=post_page---------------------------#sec_generators-as-observers)에서 이러한 패턴에 대해 조금 더 알아볼 수 있습니다.

## Generator의 장점

### Lazy Evaluation (게으른 계산)

**끊이지 않는 데이터 스트림** 예제에서 보았듯이, 그와 같은 행동은 Lazy Evaluation이라는 특성 때문에 가능합니다. Lazy Evaluation은 값이 필요로 될 때까지, 표현식의 Evaluation을 미루는 Evaluation 모델입니다. 우리가 만일 값이 필요 없다면, Evaluation이 일어나지 않습니다. 우리가 필요한 때에 값은 계산됩니다. 다음 예제를 봅시다.

```js
function * powerSeries(number, power) {
  let base = number;
  while (true) {
    yield Math.pow(base, power);
    base++;
  }
}
```

`powerSeries`는 power에 여러가지 number값을 줍니다. 예를 들면, `powerSeries`에 3을 준다면 3의 제곱부터 순서대로, **9, 16, 25, 36, 49** 를 `yield` 하게 될 것입니다. 우리가 `const powersOf2 = powerSeries(3, 2);`를 소스코드에 칠 때, 우리는 그냥 generator 오브젝트를 하나 만들 뿐입니다. 아직 아무런 값도 계산되지 않았습니다. 이제, 우리는 `next()`를 호출합니다. 9가 계산되고 반환됩니다.

### 메모리 효율

Lazy Evaluation은 즉각적으로 우리의 generator가 메모리 효율을 고려할 수 있다는 것을 알려줍니다. 우리는 필요로되는 값만 생성합니다. 일반적인 함수로 값을 구한다면, 우리는 모든 값을 미리 계산하여 구해놔야 합니다. 그리고 우리가 나중에 쓸 일을 대비해서 그 값을 가지고 있어야 합니다. 하지만, generator를 사용하여, 우리는 우리가 필요할 때까지 우리는 계산을 미룰 수 있습니다.

우리는 generator에서 작동할 Combinator 함수를 만들 수 있습니다. Combinator는 새로운 값들을 만들기 위해 존재하는 iterable을 함께 사용하는 함수입니다. 다음은 Combinator함수인 `take`입니다. 이 함수는 iterable의 첫 `n` 엘리먼트를 가져가서 그에 따른 연산을 합니다. 다음 구현을 참조하세요.

```js
function * take(n, iter) {
  let index = 0;
  for (const val of iter) {
    if (index >= n) {
      return;
    }
    index = index + 1;
    yield val;
  }
}
```

다음은 `take`의 사용 예제입니다.

```js
take(3, ['a', 'b', 'c', 'd', 'e']);

// a b c

take(7, naturalNumbers());

// 1 2 3 4 5 6 7

take(5, powerSeries(3, 2));

// 9 16 25 36 49
```

다음은 [cycled](https://github.com/sindresorhus/cycled?source=post_page---------------------------) 라이브러리의 구현입니다. (함수의 성질 자체를 뒤집지 않고 만듭니다.)

```js
function * cycled(iter) {
  const arrOfValues = [...iter];
  while (true) {
    for (const val of arrOfValues) {
      yield val;
    }
  }
}

console.log(...take(10, cycled(take(3, naturalNumbers()))));

// 1 2 3 1 2 3 1 2 3 1
```

### 경고(Caveats)

generator를 이용하여 프로그래밍하는 중에 반드시 잊지 말아야 할 것이 있습니다.

- **generator 오브젝트는 오직 한 번만 접근 가능하다.** 여러분이 만일 모든 값을 사용했다면, 소진된 generator는 다시는 반복을 수행할 수 없습니다. 다시 값을 생성해내려면, 다시 generator 오브젝트를 생성해야 합니다.

```js
const numbers = naturalNumbers();

console.log(...take(10, numbers)) // 1 2 3 4 5 6 7 8 9 10
console.log(...take(10, numbers)) // 데이터를 얻을 수 없습니다.
```

- generator 오브젝트는 배열을 이용한 값의 랜덤한 접근을 허락하지 않습니다. 왜냐하면 generator는 오직 한 번에 한 개의 값만 만들어냅니다. 랜덤한 값에 접근하는 행위는 그 엘리먼트에 도달할 때까지, 값을 계속 계산하게 할 것입니다. 결국 랜덤한 접근이 아니게 됩니다.

## 결론

generator 에 대한 많은 것들이 아직 다뤄지지 않았습니다. `yield *`라던지, `return()`, `throw()`와 같은 것들이 다루어지지 않았습니다. generator는 또한 [coroutine](https://en.wikipedia.org/wiki/Coroutine?source=post_page---------------------------)도 가능하게 만듭니다. 아래에 generator에 대한 추가적인 이해를 돕기 위한 몇가지 참고자료들을 나열해놓았습니다.

파이썬의 [itertools] 페이지를 둘러보는 것도 좋은 방법입니다. 그리고 iterator와 generator를 이용하여 어떤 것을 구현할 수 있는지 구경해보세요. 연습문제로, 여러분은 여러분 스스로 그 활용 예제들을 코딩해볼 수 있을 것입니다.

### References -

- [PEP 255](https://www.python.org/dev/peps/pep-0255/?source=post_page---------------------------) — It’s the proposal for generators in Python. But the rationale is applicable to JavaScript as well.
- [Mozilla Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators?source=post_page---------------------------)
- An in-depth four-part series by Kyle Simpson on generators and co-routines. Read [here.](https://davidwalsh.name/es6-generators?source=post_page---------------------------)
- An in-depth review of generators by Axel Rauschmayer. Read it [here.](http://exploringjs.com/es6/ch_generators.html?source=post_page---------------------------)
- [Python’s itertools](https://docs.python.org/2/library/itertools.html?source=post_page---------------------------#itertools.chain) — It’s an builtin library in Python that has lots of utilities for working with generators and iterators.
