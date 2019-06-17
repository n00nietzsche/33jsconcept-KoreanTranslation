## 들어가기 전에

- 이 포스팅은 https://blog.bitsrc.io/understanding-higher-order-functions-in-javascript-75461803bad 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://blog.bitsrc.io/understanding-higher-order-functions-in-javascript-75461803bad If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #22 자바스크립트 : 자바스크립트에서 고차 함수(Higher-Order Function) 이해하기

고차 함수(Higher-Order Function)가 무엇인지 배워보고 자바스크립트에서 고차함수를 어떻게 사용하는지 배워봅시다.

여러분이 자바스크립트를 배우는 중이라면, *고차 함수(Higher-Order Function)* 라는 용어를 얼핏 본 적이 있을 것입니다. 용어 자체는 매우 복잡하게 생겼을지 몰라도, 사실 그렇지 않습니다.

자바스크립트를 함수형 프로그래밍에 알맞은 언어로 만들어주는 특성이 바로 자바스크립트가 고차 함수 개념을 받아들인다는 것입니다.

고차 함수는 자바스크립트에서 광범위하게 사용됩니다. 만일 여러분이 자바스크립트에서 프로그래밍을 한지 꽤 됐다면, 여러분은 심지어 고차 함수가 무엇인지 모를지라도, 고차함수를 이용하여 프로그래밍 해왔을 것입니다.

고차 함수의 개념을 완전히 이해하기 위해서는 *함수형 프로그래밍(Functional Programming)* 이 무엇인지와 *퍼스트 클래스 함수(First-Class Functions)* 의 개념을 이해하는 것이 먼저입니다.

> **팁 :** **[Bit (GitHub)](https://github.com/teambit/bit)** 을 사용해보세요. 앱에서 컴포넌트를 공유하고 재사용하기 쉽게 해줍니다. 어떤 JS 코드와도 작동합니다. 그러니 한번 시도해보세요.

> 역자 주 : 이 글의 출처는 위에 소개된 Bit이라는 회사의 테크 섹션입니다. 리액트 컴포넌트가 필요하시다면 한번 방문해보세요.

## 함수형 프로그래밍이란 무엇일까요?

가장 간결한 용어, 함수형 프로그래밍은 함수를 다른 함수의 파라미터로 넘길 수도 있고 반환(return) 값으로 함수를 받을 수도 있는 프로그래밍 형태를 말합니다. 함수형 프로그래밍에서, 우리는 함수라는 용어 하에서 생각하고 코딩하게 됩니다.

자바스크립트, Haskell, Clojure, Scala, Erlang은 전부 함수형 프로그래밍을 구현한 언어입니다.

## 퍼스트클래스(First-Class) 함수

만일 여러분이 자바스크립트를 배워왔다면, 자바스크립트가 함수를 일급 시민(first-class citizen)으로 대해준다는 것을 들어봤을 겁니다. 왜냐하면 자바스크립트 또는 다른 함수형 프로그래밍 언어 함수들은 전부 객체(objects)이기 때문입니다.

자바스크립트에서, 함수는 객체의 특별한 타입입니다. 함수는 `Function` 객체입니다. 

예를 들자면 :

```js
function greeting() {
  console.log('Hello World');
}

// 함수 호출하기
greeting(); // prints 'Hello World'
```

자바스크립트에서 함수가 오브젝트인 것을 증명하기 위해서, 우리는 다음과 같은 코드를 작성할 수 있습니다.

```js
// 우리는 오브젝트에 프로퍼티를 추가하듯 함수에 프로퍼티를 추가할 수 있습니다. 
greeting.lang = 'English';

// Prints 'English'
console.log(greeting.lang);
```

**알아둘 것ㅡ** 위 문법은 자바스크립트에서 완전히 유효한 문법인 반면에, 위와 같이 코드를 작성하는 것은 매우 위험하게 여겨지고 있습니다. 함수 오브젝트에 랜덤한 프로퍼티를 추가하지 않는 것이 좋습니다. 만일 그래야만 한다면 오브젝트를 사용하세요.

자바스크립트에서, object, string, number와 같은 타입으로 할 수 있는 것은, 함수로 할 수 있습니다. 여러분은 함수를 파라미터로 다른 함수에 넘길 수도 있고(콜백), 함수를 다른 변수에 할당하거나 다른 곳으로 넘길 수 있습니다. 이러한 특성 때문에 자바스크립트에 존재하는 함수들이 퍼스트 클래스 함수라 불리는 것입니다.

### 함수를 변수에 할당하기

우리는 자바스크립트에서 함수를 변수에 할당할 수 있습니다. 

예를 들면:

```js
const square = function(x) {
  return x * x;
}

// prints 25
square(5);
```

우리는 또한 함수를 여러 곳에 넘길 수도 있습니다. 

예를 들면:

```js
const foo = square;

// prints 36
foo(6);
```

### 함수를 파라미터로 넘기기

우리는 함수를 여러 곳에 파라미터로 넘길 수 있습니다.

예를 들면 : 

```js
function formalGreeting() {
  console.log('How are you?');
}

function casualGreeting() {
  console.log("What's up?");
}

function greet(type, greetFormal, greetCasual) {
  if(type === 'formal') {
    greetFormal();
  } else if(type === 'casual') {
    greetCasual();
  }
}

// prints "What's up?"
greet('casual', formalGreeting, casualGreeting);
```

이제 우리는 퍼스트 클래스 함수가 무엇인지 알았습니다. 이제 자바스크립트 고차 함수(Higher-Order Function)에 대해서 자세히 알아봅시다.

## 고차 함수(Higher-Order Function)

고차 함수는 함수를 인자로 받거나 또는 함수를 반환함으로써 작동 하는 함수를 말합니다. 간단히 말하자면, 고차 함수는 함수를 인자로 받거나 함수를 출력(output)으로 반환하는(return) 함수를 말합니다.

예를 들면, `Array.prototype.map`, `Array.prototype.filter` 그리고 `Array.prototype.reduce`가 언어 내부에 포함된 (built-in) 고차함수입니다.

### 고차 함수의 동작

먼저, 내부적(built-in) 고차 함수의 예제부터 보고난 뒤에 고차함수를 사용하지 않았을 때의 솔루션과 비교해봅시다.

### Array.prototype.map

`map()` 메소드는 입력으로 들어온 배열 내 모든 엘리먼트를 인자로 제공받는 콜백 함수를 호출함으로써 새로운 배열을 만들어냅니다. `map()` 메소드는 콜백 함수에서 모든 반환된 값을 가져올 것입니다. 그리고 그 값들을 이용한 새로운 배열 하나를 만들어냅니다.

`map` 메소드로 전해진 콜백 함수는 3가지 인자를 받습니다: `element`, `index`, 그리고 `array`

예제를 봅시다.

#### Example #1

우리가 숫자가 들어있는 배열을 가지고 있고 각각의 숫자 값이 2배가 된 배열을 만들길 원한다고 해봅시다. 고차 함수(Higher-Order function)가 없을 때와 있을 때, 각각 우리가 문제를 어떻게 해결할 수 있는지 봅시다.

##### 고차 함수가 아닌 함수로 작성

```js
const arr1 = [1, 2, 3];
const arr2 = [];

for(let i=0; i<arr1.length; i++) {
  arr2.push(arr1[i] * 2);
}

// prints [2, 4, 6]
console.log(arr2);
```

##### 고차 함수로 작성

```js
const arr1 = [1, 2, 3];

const arr2 = arr1.map(function(item) {
  return item * 2;
});

console.log(arr2);
```

화살표 함수 문법을 이용하면 훨씬 짧게 작성 가능합니다.

```js
const arr1 = [1, 2, 3];
const arr2 = arr1.map(item => item * 2);
console.log(arr2);
```

#### Example #2

우리가 사람들의 생일을 가지고 있는 배열을 가지고 있고 우리는 그 배열을 이용하여 그들의 나이를 계산하고 싶습니다.

##### 고차 함수가 아닌 함수로 작성

```js
const birthYear = [1975, 1997, 2002, 1995, 1985];
const ages = [];

for(let i=0; i<birthYear.length; i++){
  let age = 2018 - birthYear[i];
  ages.push(age);
}

// prints [43, 21, 16, 23, 33]
console.log(ages);
```

##### 고차 함수로 작성

```js
const birthYear = [1975, 1997, 2002, 1995, 1985];
const ages = birthYear.map(year => 2018 - year);

//prints [43, 21, 16, 23, 33]
console.log(ages);
```

### Array.prototype.filter

`filter()` 메소드는 콜백 함수에 의해 제공된 테스트를 통과한 모든 엘리먼트를 가진 새로운 배열을 만들어냅니다. `filter()` 메소드로 넘겨진 콜백 함수는 3가지 인자를 받습니다: `element`, `index`,`array`를 받습니다.

몇가지 예제를 봅시다.

#### Example #1

우리가 이름과 나이 프로퍼티를 가진 오브젝트를 가지고 있다고 해봅시다. 우리는 18살 이상의 사람만 필터링된 새로운 배열을 만들고 싶습니다.

##### 고차 함수가 아닌 함수로 작성

```js
const persons = [
  { name: 'Peter', age: 16 },
  { name: 'Mark', age: 18 },
  { name: 'John', age: 27 },
  { name: 'Jane', age: 14 },
  { name: 'Tony', age: 24},
];
const fullAge = [];
for(let i = 0; i < persons.length; i++) {
  if(persons[i].age >= 18) {
    fullAge.push(persons[i]);
  }
}
console.log(fullAge);
```

##### 고차 함수로 작성

```js
const persons = [
  { name: 'Peter', age: 16 },
  { name: 'Mark', age: 18 },
  { name: 'John', age: 27 },
  { name: 'Jane', age: 14 },
  { name: 'Tony', age: 24},
];
const fullAge = persons.filter(person => person.age >= 18);
console.log(fullAge);
```

### Array.prototype.reduce

`reduce` 메소드는 호출하는 배열의 각각의 멤버에 대해서 콜백 함수를 실행하고 하나의 결과 값만 내보냅니다. `reduce` 메소드는 2가지 파라미터를 받습니다. 1) 리듀서 함수 (콜백), 2) 초기값(`initialValue`) 옵션

리듀서(콜백) 함수는 4가지 파라미터를 받습니다: `accumulator`, `currentValue`, `currentIndex`, `sourceArray`.

만일 `initialValue`가 제공되었다면, 그 후에 `accumulator`는 `initialValue`와 같아지고 `currentValue`는 배열의 첫번째 값과 동일할 것입니다.

만일 `intialValue`가 제공되지 않았다면, 그 후에 `accumulator`는 배열의 처음 요소와 동일해지고 `currentValue`는 배열의 두번째 요소와 같아질 것입니다.

#### Example #1

숫자 배열의 합을 구하는 예제를 만들어봅시다.

##### 고차 함수로 작성

```js
const arr = [5, 7, 1, 8, 4];

const sum = arr.reduce(function(accumulator, currentValue) {
  return accumulator + currentValue;
});

// prints 25
console.log(sum);
```

배열 내부의 각 값에 대해 리듀서 함수가 호출되는 모든 순간에, `accumulator`는 리듀서 함수로부터 반환된 이전 연산의 결과를 갖고 있습니다. 그리고 `currentValue`는 배열의 현재 값으로 세팅됩니다. 마지막에, 결과 값은 `sum` 변수에 저장됩니다.

우리는 이 함수에 초기 값을 제공하는 것도 가능합니다.

```js
const arr = [5, 7, 1, 8, 4];

const sum = arr.reduce(function(accumulator, currentValue) {
  return accumulator + currentValue;
}, 10);

// prints 35
console.log(sum);
```

##### 고차 함수가 아닌 함수로 작성

```js
const arr = [5, 7, 1, 8, 4];

let sum = 0;

for (let i=0; i<arr.length; i++) {
  sum = sum + arr[i];
}

// prints 25
console.log(sum);
```

여기까지 고차 함수를 이용하여 코드를 좀 더 깔끔하고 간결하게 더럽지 않게 작성하는 방법을 볼 수 있었습니다.

## 우리만의 고차함수 만들기

이 지점까지, 우리는 자바스크립트 내부에 작성되어 있는 다양한 고차 함수를 보았습니다. 이제 우리만의 고차함수를 작성해봅시다.

자바스크립트에 네이티브한 map 메소드가 없었다고 상상해봅시다. 우리는 스스로 만들어낼 수 있습니다. 우리만의 고차 함수를 만들어봅시다.

우리가 문자열의 배열을 가지고 있다고 하고 우리는 이 배열을 정수의 배열로 바꾸고 싶습니다. 여기서 정수는 기존 배열에 존재하던 각각의 문자열의 길이를 표현하고 싶습니다.

```js
const strArray = ['Javascript', 'Python', 'PHP', 'Java', 'C'];

function mapForEach(arr, fn) {
  const newArray = [];
  for(let i=0; i<arr.length; i++){
    newArray.push(
      fn(arr[i])
    );
  }
  return newArray;
}

const lenArray = mapForEach(strArray, function(item) {
  return item.length;
});

// prints [10, 6, 3, 4, 1]
console.log(lenArray);
```

위의 예제에서, 우리는 배열과 콜백함수 `fn`을 받는 고차 함수 `mapForEach`를 만들었습니다. 이 함수는 제공받은 배열을 반복하고 `newArray.push` 함수 내부에서 각각의 반복마다 콜백 함수 `fn`을 호출합니다.

콜백 함수 `fn`은 배열의 현재 요소를 받고 `newArray`의 내부에 저장된 요소의 길이를 반환합니다. for 루프가 끝난 이후에, `newArray`가 반환되고, `lenArray`에 할당됩니다.

## 결론

우리는 고차 함수가 무엇인지와 내장된(built-in) 고차함수에 대해서 배웠습니다. 우리는 또한 우리만의 고차함수를 어떻게 작성하는지 배웠습니다.

