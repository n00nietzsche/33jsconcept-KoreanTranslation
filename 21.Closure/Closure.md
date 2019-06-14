# 들어가기 전에

- 이 포스팅은 https://javascript.info/closure 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://javascript.info/closure If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #21 자바스크립트 : 클로져

# 클로져

자바스크립트는 매우 함수지향적인 언어입니다. 자바스크립트는 우리에게 많은 자유를 줍니다. 함수는 동적으로 만들어질 수 있습니다. 다른 변수에 복사될 수도 있고, 다른 함수에 인자로 넘겨질 수도 있고 완전히 다른 장소에서 나중에 호출될 수도 있습니다.

우리는 함수가 함수 외부에 있는 변수에 접근할 수 있다는 것을 알고 있습니다. 그리고 이러한 특성은 자주 사용됩니다.

하지만 외부의 변수가 변경될 때, 무슨 일이 일어날까요? 함수는 가장 최근 값을 갖을까요? 또는 함수가 만들어질 때 존재했던 값을 가질까요?

또, 코드에서 함수가 다른 위치로 이동할 때, 그리고 거기에서 호출됐을 때, 무슨 일이 일어날까요? 함수는 새로운 위치의 외부 변수에 접근 가능할까요?

다른 언어들은 다르게 동작합니다. 이 챕터에서 우리는 자바스크립트는 어떻게 동작하는지에 대해 다뤄보겠습니다.

## 몇가지 질문

두가지 상황을 고려해보는 것으로 시작해봅시다. 그리고 여러분이 다음 질문에 대답하고 미래에는 더 어려운 질문에도 대답할 수 있도록 내부 동작을 하나하나 공부해봅시다.

1. 함수 `sayHi`는 외부 변수 `name`을 사용합니다. 함수가 동작할 때, 어떤 값을 사용할까요?

```js
let name = "John";

function sayHi() {
  alert("Hi, " + name);
}

name = "Pete";

sayHi(); // what will it show: "John" or "Pete"?
```

이러한 상황은 브라우저단 그리고 서버단 개발에서 흔히 있는 상황입니다. 함수는 먼저 생성된 뒤에 나중에 실행되도록 예정되어있습니다. 예를 들면 유저의 액션 또는 네트워크 요청 이후입니다.

그래서, 문제는 : 이 함수가 최신에 변경된 값을 가져올까요?

2. 함수 `makeWorker`는 다른 함수를 만들어내고 반환합니다. 새로운 함수는 다른 어딘가에서 불려질 수 있습니다. 이 함수는 어떤 외부 변수에 대한 접근 권한을 갖고 있을까요. 이 함수가 만들어 진 곳? 또는 함수가 호출된 곳? 또는 두 곳 모두?

## 어휘적인(Lexical) 환경

어떤 일이 일어나는지 이해하기 위해서, "변수"란 것이 무엇인지에 대해 먼저 논의해봅시다.

자바스크립트에서, 모든 동작하는 함수 코드블럭 `{...}`과 스크립트 전체는 내부적인 (숨겨진) 연관된 오브젝트를 *어휘적인(Lexical) 환경*으로서 갖고 있습니다.

어휘적 환경 오브젝트는 두가지 부분을 갖고 있습니다 :

1. *환경 기록(Environment Record)* - 모든 지역 변수를 프로퍼티로 갖고 있는 오브젝트 (그리고 `this`의 값과 같은 다른 정보들도)
2. *외부 어휘 환경(outer lexical environment)* 에 대한 참조, 외부 코드에 관련된 것

**그래서 "변수"는 특별한 내부 오브젝트(환경 기록, Environment Record)의 프로퍼티입니다. "변수를 가져오거나 변경하는 것"은 "그 오브젝트의 프로퍼티를 가져오거나 변경하는 것"을 의미합니다.**

예를 들면, 이 간단한 예제 코드에서는, 단 하나의 어휘적 환경만 지닙니다:

![lexical-env1.png](https://images.velog.io/post-images/jakeseo_me/3cff1870-8674-11e9-9809-ed6e36f2cf44/lexical-env1.png)

이것은 전역 어휘 환경이라고 불리는 것입니다. 전체 스크립트에 연관되어 있습니다.

위의 사진에서, 사각형은 환경 기록(Environment Record, 변수 저장소)을 의미합니다. 그리고 화살이 의미하는 것은 외부 참조입니다. 전역 어휘 환경은 외부 참조를 갖고 있지 않습니다. 그래서 전역 어휘 환경은 `null`을 가리킵니다.

여기 let이 변경될 때, 어떠한 일이 일어나는지에 대한 더 커다란 그림이 있습니다.

![lexical-environment-global-2.png](https://images.velog.io/post-images/jakeseo_me/a69483b0-8674-11e9-94a9-093dca7124f1/lexical-environment-global-2.png)

오른쪽의 사각형들은 전역 어휘 환경이 실행동안 어떻게 변화하는지에 대한 묘사를 하고 있습니다.

1. 스크립트가 시작할 때, 어휘 환경은 비어있습니다.
2. `let phrase` 정의가 나타납니다. 현재 아무런 값도 할당되어 있지 않습니다. 그래서 `undefined`가 저장됩니다.
3. phrase는 값을 할당받습니다.
4. phrase는 값을 바꿉니다.

현재까지는 모든 것들이 간단해보일겁니다. 그렇지 않나요?

요약하자면:

- 변수는 특별한 내부 오브젝트의 프로퍼티입니다. 특별한 내부 오브젝트는 현재 실행중인 블록/함수/스크립트와 연관되어 있습니다.
- 변수들이 작동하는 것은 실제로 그 오브젝트의 프로퍼티들이 작동하는 것입니다.

### 함수 선언

지금까지, 우리는 변수에 대해서만 알아봤습니다. 이제 함수 선언에 대해 알아봅시다.

**`let` 변수와는 다르게, 함수는 완전히 초기화됩니다. 프로그램이 실행되어 함수에 접근할 때 초기화되는 것이 아니라 그 이전에 어휘적 (Lexical) 환경이 만들어질 때 함수는 초기화됩니다.**

최상위 레벨의 함수들은 스크립트가 시작되는 순간에 초기화됩니다.

이것이 우리가 함수가 정의되기 전에 함수 선언을 호출할 수 있는 이유입니다. 

아래의 코드는 어휘적(Lexical) 환경이 시작부터 비어있는 상태가 아니라는 것(non-empty)을 나타냅니다. 아래에서 어휘적(Lexical) 환경은 함수 `say`를 갖고 있습니다. 왜냐하면 `say`는 함수 선언이기 때문입니다. 그리고 이후에 어휘적 환경은 `let`으로 선언된 `phrase`를 갖게 됩니다.

![lexical-environment-global-3.png](https://images.velog.io/post-images/jakeseo_me/59f849e0-8b1c-11e9-953c-41d6fbbdf251/lexical-environment-global-3.png)

### 내부(Inner)와 외부(outer) 어휘(Lexical) 환경

이제 함수가 외부의 변수에 접근할 때 무슨 일이 일어나는지 살펴봅시다.

함수 호출 동안, `say()`는 외부 변수 `phrase`를 사용합니다. 어떤 일이 일어나는지 상세하게 살펴봅시다.

먼저, 함수가 실행될 때, 새로운 함수 어휘 환경이 자동으로 만들어집니다. 모든 함수에 대한 일반적인 규칙입니다. 어휘적 환경은 지역 변수들과 함수 호출 시의 파라미터를 저장하기 위하여 사용됩니다.

예를 들면 `say("John")`의 경우는, 다음 그림처럼 보입니다. (실행을 아래의 그림에서 화살표로 표기된 곳에서 하고 있습니다.)

![lexical-environment-simple.png](https://images.velog.io/post-images/jakeseo_me/744bb570-8b3f-11e9-bd94-1dd655c28c38/lexical-environment-simple.png)

그래서 함수 호출 동안, 우리는 2개의 어휘 환경을 갖고 있습니다: 함수 호출을 위한 내부 어휘 환경과 전역 변수를 위한 외부 어휘 환경을 갖고 있습니다.

- 내부 어휘 환경은 `say`의 현재 실행에 대응됩니다.
- 내부 어휘 환경은 `name`이라는 하나의 프로퍼티(함수 인자)를 갖고 있습니다. 우리는 `say("John")`을 호출하였고, name의 값은 "John"이 됩니다.
- 외부 어휘 환경은 전역 어휘 환경입니다. 외부 어휘 환경은 `phrase` 변수와 함수 그 자체를 갖고 있습니다.

내부 어휘 환경은 외부 어휘 환경에 대한 참조를 갖고 있습니다. 

**코드에서 변수에 접근할 때, 내부 어휘 환경이 먼저 검색됩니다. 그리고 외부 어휘 환경을 검색합니다. 그리고 더욱 외부에 있는 어휘 환경을 거쳐 전역 어휘 환경까지 도달합니다.**

만일 어디에서도 변수가 발견되지 않는다면, strict mode에서는 에러가 표기됩니다. `use strict`표기 없이는, 이전 버전에 대한 호환성을 위하여 정의되지 않은(undefined) 변수를 새로 할당하여 전역 변수를 만들어낼 것입니다.

우리 예제에서 검색 과정이 어떻게 진행되는지 살펴봅시다.

- `say` 내부의 `alert`가 `name`에 접근하길 원할 때, `say` 함수는 함수 어휘 환경 내부에서 `name`이라는 이름을 즉시 찾아봅니다.
- `say`가 `phrase`에 접근하길 원할 때는, `phrase`는 지역 어휘 환경에는 존재하지 않습니다. 그래서 둘러싸는 (enclosing) 어휘 환경의 참조를 따라가고 외부 어휘 환경에서 `phrase`를 찾아냅니다.

![lexical-environment-simple-lookup.png](https://images.velog.io/post-images/jakeseo_me/1795e870-8b47-11e9-9034-95d7d971847a/lexical-environment-simple-lookup.png)

이제 여러분은 챕터에서 처음 나왔던 질문에 대해 답을 할 수 있습니다.

**함수는 현재 외부 변수에 접근이 가능하다; 그러나 가장 가까운 값을 사용한다.**

이러한 일이 발생하는 원인은 상술된 메커니즘 때문입니다. 오래된 변수 값들은 어디에도 저장되지 않습니다. 함수가 오래된 변수 값들을 참조하길 원할 때, 함수는 자기 자신의 어휘 환경 또는 외부 어휘 환경에서 지금 값을 가져옵니다.

그래서 첫번째 질문의 답은 `"Pete"` 였습니다.

```js
let name = "John";

function sayHi() {
  alert("Hi, " + name);
}

name = "Pete"; // (*)

sayHi(); // Pete
```

위의 코드의 흐름은 다음과 같습니다.

1. 전역 어휘 환경은 `name: "John"`을 갖고 있습니다.
2. `(*)`로 표기된 줄에서, 전역 변수가 변경됩니다. 이젠 전역 어휘 환경은 `name: "Pete"`를 갖습니다.
3. `sayHi()`가 실행되고 `name` 변수 값을 외부에서 가져옵니다. `name` 변수는 전역 어휘 환경에서 가지고 오게 되는데, 전역 어휘 환경의 `name`은 `"Pete"`입니다.

> **하나의 호출에 하나의 어휘 환경 :** 반드시 알아둬야 할 것은 새로운 함수의 어휘 환경은 함수가 실행되는 각각의 시간마다 한번씩 만들어진다는 것입니다. 그리고 만일 함수가 여러번 호출되면, 각 호출이 그 호출을 위한 지역 변수들과 파라미터를 가진 자신만의 어휘 환경을 가질 것입니다.

> **어휘 환경은 상술 객체(Specification Object)이다 :** "어휘 환경(Lexical Environment)"는 상술 객체입니다. 우리는 이 오브젝트를 코드에서 가져올 수도 없고 직접 수정할 수도 없습니다. 자바스크립트 엔진은 아마 이 객체에 대한 최적화를 진행할 것입니다. 메모리를 아끼기 위해 사용되지 않는 변수를 제거하고 다른 내부적인 트릭도 사용할 것입니다. 하지만 보여지는 행위는 기술된대로 행할 것입니다.

## 중첩된 함수들

함수가 다른 함수 내부에서 생성됐을 때, 우리는 이 함수를 "중첩된" 함수라고 부릅니다.

중첩된 함수를 자바스크립트에서는 매우 쉽게 구현할 수 있습니다.

우리는 우리의 코드를 정리하기 위해 중첩된 함수를 사용합니다. 다음과 같이요.

```js
function sayHiBye(firstName, lastName) {  
  // helper nested function to use below
  function getFullName() {
    return firstName + " " + lastName;
  }
  
  alert( "Hello, " + getFullName() );
  alert( "Bye, " + getFullName() );
}
```

여기에 *중첩된* 함수 `getFullName()`은 편의를 위해 만들어졌습니다. 이 함수는 외부의 변수에 접근할 수 있습니다. 그래서 fullName을 반환할 수 있습니다. 중첩된 함수는 자바스크립트에서는 꽤 일반적인 코드입니다.

더욱 흥미로운 것은, 중첩된 함수가 반환될 수 있다는 것입니다: 하나의 새로운 오브젝트의 새로운 프로퍼티 (만일 외부 함수가 메소드와 함께 오브젝트를 만든다면) 또는 그 자체의 결과로 리턴 가능합니다. 위치는 상관없이, 중첩된 함수는 여전히 같은 외부 변수들에 대한 참조자를 가지고 있습니다.

예를 들면, [생성자 함수](https://javascript.info/constructor-new)에 의해 중첩된 함수가 새로운 오브젝트에 할당되는 코드가 있습니다.

```js
// constructor function returns a new object
function User(name) {

  // the object method is created as a nested function
  this.sayHi = function() {
    alert(name);
  };
}

let user = new User("John");
user.sayHi(); // the method "sayHi" code has access to the outer "name"
```

그리고 여기에서 우리는 "counting" 함수를 만들고 반환합니다.

```js
function makeCounter() {
  let counter = 0;
  
  return function() {
    return count++;
  };
}

let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2
```

`makeCounter` 예제에 대해 자세히 짚어볼까요? `makeCounter`는 매 호출 시에 다음 숫자를 반환하는 "counter" 함수를 만듭니다. 간단함에도 불구하고, 그 코드에서 약간의 수정된 변형들이 실용적인 사용 용례들을 갖고 있습니다. 예를 들면, 의사 난수 생성기와 다른 용도로서도 가능합니다. 

어떻게 counter가 내부적으로 작동할까요?

내부 함수가 작동할 때, `count++` 안에 있는 변수는 안에서 밖으로 찾아집니다. 예를 들면 위의 예제에서 순서는 다음과 같습니다.

![lexical-search-order.png](https://images.velog.io/post-images/jakeseo_me/de532820-8b5a-11e9-aa43-c9541045e28f/lexical-search-order.png)

1. 중첩된 함수의 지역 어휘 환경에 접근합니다.
2. 외부 함수의 변수들에 접근합니다.
3. 전역 변수에 닿을 때까지 계속하여 반복합니다.

이 예제에서 `count`는 `2`번째 단계에서 발견됩니다. 외부 변수가 수정됐을 때, 외부 변수는 발견된 곳에서 변경됩니다. 그래서 `count++`는 이 코드가 속한 어휘 환경 내부로 부터 외부 변수를 찾고 외부 변수를 증가시킵니다. 만일 우리가 `let count = 1`을 갖고 있었다면,

여기 2가지 고려할 질문 사항이 있습니다.

1. 우리가 `makeCounter`에 속하지 않은 코드로부터 카운터 `count`를 초기화 시킬 수 있을까요? 예를 들면, 위의 코드에서는 `alert` 호출 이후에 말입니다.
2. 우리가 `makeCounter()`를 여러번 호출한다면, `makeCounter()`는 많은 counter 함수를 만들어냅니다. 그들은 독립적일까요 아니면 같은 `count`를 공유할까요?

한번 여러분의 답을 작성해보세요...

....

모두 작성 하셨나요?

정답은 다음과 같습니다.

1. 방법이 존재하지 않습니다 : `count`는 지역 함수 변수입니다. 우리는 외부로부터 이 변수에 접근할 수 없습니다.
2. `makeCounter()`로의 모든 호출에 대하여, 자체적인 `count`와 함께 새로운 어휘 환경이 만들어집니다. 그래서 결과적인 `counter` 함수는 독립적입니다.

다음은 데모입니다:

```js
function makeCounter() {
  let count = 0;
  return function() {
    return count++;
  };
}

let counter1 = makeCounter();
let counter2 = makeCounter();

alert( counter1() ); // 0
alert( counter1() ); // 1

alert( counter2() ); // 0 (independent)
```

희망적으로 보았을 때, 외부 변수에 대한 상황은 이제 깔끔합니다. 대부분의 상황에서는, 이정도의 이해만 있으면 충분합니다. 간결성을 위해서 생략한 스펙에 대한 몇가지 디테일들이 있습니다. 그래서 다음 섹션에서는 우리는 더욱 디테일한 것들을 커버할 것입니다. 지금까지 배웠던 것들을 까먹지마세요.

## 환경 자세히 보기

여기서 `makeCounter`예제에서 일어나는 일들을 단계단계 살펴봅니다. 디테일하게 알고 싶다면 잘 따라오세요.

추가적인 `[[Environment]]` 프로퍼티가 여기서 다뤄질 것이라는 것을 잘 알아두세요. 이전에는 간략하게만 설명하기 위해서 빼먹었던 부분입니다.

1. 스크립트가 시작됐을 때는, 오직 전역 어휘 환경만이 존재했습니다.

![lexenv-nested-makecounter-1.png](https://images.velog.io/post-images/jakeseo_me/4b1d8390-8bf3-11e9-b149-4faf97263e4b/lexenv-nested-makecounter-1.png)

위 시작점에서는, 오직 `makeCounter` 함수만이 존재합니다. 왜냐하면 이건 함수 선언이기 때문입니다. 아직 실행되지 않았습니다.

**모든 함수들은 "태어나는 순간(on birth)"에 함수 생성의 어휘 환경 참조를 가진 숨겨진 프로퍼티 `[[Environment]]`를 받습니다.** 우리는 아직 이것에 대해 이야기 한 적이 없지만, 이것은 함수가 어디에서 만들어졌는지 아는 이유입니다.

여기, `makeCounter`가 전역 어휘 환경에서 만들어집니다. 그래서 `[[Environment]]`는 전역 어휘 환경에 대한 참조를 갖고 있습니다.

다시 말해서, 함수는 함수가 생겨난 위치의 어휘 환경과 함께 찍혀나온다("imprinted")는 것입니다. 그리고 `[[Environment]]`는 그 참조를 가진 숨겨진 함수 프로퍼티입니다.

2. 코드가 실행되고, 새로운 전역 변수인 `counter`가 선언되고, 값을 위해 `makeCounter()`가 호출됩니다. 여기에 `makeCounter()` 내부의 코드 첫째 줄 실행 순간 스냅샷이 있습니다. 

![lexenv-nested-makecounter-2.png](https://images.velog.io/post-images/jakeseo_me/43c7e650-8c05-11e9-bbd3-37eb8635867b/lexenv-nested-makecounter-2.png)

`makeCounter()`를 호출하는 순간에, 변수와 인자를 보관하기 위한 어휘 환경이 만들어집니다.

모든 어휘 환경처럼, counter는 2가지를 저장합니다.

1. 지역 변수를 저장하는 환경 레코드. 우리의 경우에는, `count`가 유일한 지역 변수입니다. (`let count`가 실행된 줄이 실행될 때를 생각해보면 그렇습니다.)

2. 함수의 `[[Environment]]`로 세팅된 외부 어휘 참조. 여기에 `makeCounter`의 `[[Environment]]`가 전역 어휘 환경을 참조합니다.

그래서 이제 우리는 두 개의 어휘 환경을 갖고 있습니다 : 첫번째 어휘 환경은 전역입니다. 두번째 어휘 환경은 현재의 전역으로의 외부 참조와 함께 `makeCounter` 호출을 위한 것입니다.

3. `makeCounter()`의 실행 동안, 작은 중첩된 함수가 만들어집니다.

함수가 만들어질 때, 함수 선언인지 함수 표현식인지는 중요하지 않습니다. 모든 함수는 그 함수들이 만들어진 어휘적 환경을 참조하는 `[[Environment]]` 프로퍼티를 갖습니다. 그래서 우리의 새로운 작은 중첩된 함수도 `[[Environment]]`를 갖습니다.

우리의 새로운 중첩된 함수에 대해, `[[Environment]]`의 값은 `makeCounter()`의 현재의 어휘 환경 (함수가 만들어진 위치) 입니다.

![lexenv-nested-makecounter-3.png](https://images.velog.io/post-images/jakeseo_me/12e2dd10-8c0a-11e9-bb25-4b9de18de654/lexenv-nested-makecounter-3.png)

이 단계에서 내부 함수가 만들어졌었다는 것을 기억하세요. 하지만 아직 호출되지 않았습니다. `function() { return count ++; }` 내부의 코드는 아직 동작하지 않았습니다.

4. 실행이 진행되며, `makeCounter()`로의 호출은 끝이납니다. 그리고 결과(작고 중첩된 함수)는 전역 변수 `counter`에 할당됩니다.

![lexenv-nested-makecounter-4.png](https://images.velog.io/post-images/jakeseo_me/6c0158e0-8c0a-11e9-bb25-4b9de18de654/lexenv-nested-makecounter-4.png)

그 함수는 오직 하나의 라인만 갖습니다. `return count++`, 우리가 이 함수를 실행시킬 때, 실행될 것입니다.

5. `counter()`가 호출됐을 때, 함수 호출을 위한 "빈(empty)" 어휘 환경이 만들어집니다. 이 어휘 환경은 그 자체로 어떤 지역 변수도 가지고 있지 않습니다. 하지만 `counter`의 `[[Environment]]`는 이 어휘 환경의 외부 참조로서 사용됩니다. 그래서 이 어휘 환경은 이전 `makeCounter()` 호출의 변수에 접근할 수 있습니다.

![lexenv-nested-makecounter-5.png](https://images.velog.io/post-images/jakeseo_me/3c549af0-8cf3-11e9-bbde-c38131c8b066/lexenv-nested-makecounter-5.png)

지금, 만일 이 어휘 환경이 변수에 접근한다면, 처음에는 비어있는 자기 자신의 어휘 환경부터 뒤질 것입니다. 그 후에는 이전의 `makeCounter()` 호출의 어휘 환경을 뒤지고 그 이후에는 전역 어휘 환경을 뒤집니다.

이 어휘 환경이 `count`를 찾을 때, 이 어휘 환경은 가장 가까운 외부 어휘 환경인 `makeCounter`의 변수 사이에서 `counter`를 발견할 것입니다.

여러분들이 알아둬야 할 것은 여기서 메모리 관리가 어떻게 동작하는지 입니다. `makeCounter()` 호출이 얼마 전에 끝났음에도 불구하고, 어휘 환경은 그대로 메모리에 보관되어 있습니다. 왜냐하면 그 어휘 환경을 참조하는 `[[Environment]]`를 가진 중첩 함수가 존재하기 때문입니다.

일반적으로, 하나의 어휘 환경 오브젝트는 어떤 함수가 그 어휘 환경을 사용하는 한 계속하여 살아있습니다. (메모리에서 지워지지 않습니다.) 오직 그 어휘 환경을 사용하는 함수가 존재하지 않을 때에만 메모리에서 지워집니다.

6. `counter()` 호출은 `count`의 값만 반환하는 것이 아니라 증가도 시킵니다. 이런 수정사항이 "그 공간에서" 일어난다는 것을 알아두셔야 합니다. `count`의 값은 정확히 그 변수가 발견된 환경 내부에서 수정됩니다. 

![lexenv-nested-makecounter-6.png](https://images.velog.io/post-images/jakeseo_me/8cc154a0-8cf4-11e9-8967-27a33d637eae/lexenv-nested-makecounter-6.png)

결과적으로 우리는 유일한 변화였던 새로운 `count` 값을 지니고 이전 단계로 돌아옵니다. 뒤에 이어지는 호출들도 모두 같은 일을 합니다.

7. 다음 `counter()`를 호출하고 같은 작업을 합니다.

챕터를 시작하며 물어봤던, 두번째 질문의 답변이 이제는 꽤나 분명합니다.

아래의 코드에서 `work()` 함수는 원래 장소로 부터 외부 어휘 환경 참조를 통하여 `name`을 사용합니다:

![lexenv-nested-work.png](https://images.velog.io/post-images/jakeseo_me/60354210-8cf5-11e9-8967-27a33d637eae/lexenv-nested-work.png)

그래서, 여기서 결과 값은 `"Pete"`가 나오게 됩니다.

`makeWorker()` 함수 내부의 `let name`이 없었다면, 검색 과정에서 바깥 어휘 환경으로 진입하여 우리가 위의 체인에서 볼 수 있는 전역 변수를 들고 왔을 것입니다. 이번 경우에는 그 전역 변수는 `"John"` 입니다.

> **Closures :** 일반적인 프로그래밍 용어 중 "Closure"가 있습니다. 개발자라면 알아두는 것이 좋습니다.

> [closure](https://en.wikipedia.org/wiki/Closure_(computer_programming))는 외부의 변수를 기억하고 접근할 수 있는 함수입니다. 몇몇 언어에서는, 이러한 구현이 불가능합니다. 또한 이러한 일이 일어나게 하기 위해서는 함수를 특별한 방법으로 작성해야 합니다. 하지만 위에 기재된대로, 자바스크립트에서는, 모든 함수가 자연적으로 closure입니다. (다만 하나의 예외는 있는데, 나중에 다뤄질 것이지만 ["new Function" 문법(https://javascript.info/new-function)]이 있습니다.

 >자바스크립트의 함수들은 자신이 생성된 위치를 숨겨진 `[[Environment]]` 프로퍼티를 이용하여 기억합니다. 그리고 그렇게 만들어진 모든 함수들은 외부 변수에 접근이 가능합니다.
인터뷰를 할 때, 한 프론트엔드 개발자가 "closure란 무엇인가요?"라는 질문을 받았습니다. 유효한 정답은 closure의 정의를 설명하고 모든 자바스크립트에서의 함수가 클로져이며, 기술적인 디테일에 대해서 몇가지 추가적인 설명을 더 해주는 것입니다: `[[Environment]]` 프로퍼티와 어떻게 어휘 환경이 작동하는지에 대해서요.

## 코드블럭과 루프, 즉시 실행 함수(IIFE)

위의 예제들은 함수라는 것에 집중했습니다. 하지만 사실 어휘 환경이란 것은 어떤 코드 블럭`{...}`에도 존재합니다.

어휘 환경은 코드 블럭이 실행되고 블럭-지역 변수를 가질 때, 만들어집니다. 여기 몇가지 예제가 있습니다.

### IF

아래의 예제에서, `user` 변수는 오직 `if` 블럭 안에만 존재합니다.

![lexenv-if.png](https://images.velog.io/post-images/jakeseo_me/758bae90-8cf7-11e9-bbde-c38131c8b066/lexenv-if.png)

실행 컨텍스트가 `if` 블럭 안으로 들어갈 때, 블럭을 위한 새로운 "if-only" 어휘 환경이 만들어집니다.

이 어휘 환경은 외부 어휘 환경으로의 참조를 갖고 있습니다. 그래서 `phrase`를 찾을 수 있습니다. 하지만 if문 내부에 선언된 모든 변수와 함수 표현식들은 그 어휘 환경 내부에 존재합니다. 그리고 바깥에서 접근될 수 없습니다.

예를 들면, `if`가 끝난 이후, `alert` 아래에서 `user`에 접근하면, 그래서 에러가 납니다.


### For, while

For 루프 문은 모든 반복이 독립된 어휘 환경을 지닙니다. 만일, 변수가 `for` 내부에서 선언됐다면 그 변수 또한 어휘 환경의 지역 변수입니다.

```js
for (let i=0; i<10; i++) {
  // 각각의 루프가 자신의 어휘 환경을 가짐
  // {i: value}
}

alert(i); // Error, no such variable
```

알아둬야 할 것 : `let i`는 시각적으로는 `{...}` 외부에 있습니다. `for` 생성은 여기서 다소 특별합니다: 각 루프의 반복(iteration)이 `i`를 내부에 지닌 자신의 어휘 환경을 갖는다는 것입니다.

다시, `if`와 비슷하게 루프 이후에는 `i`에 접근이 불가능합니다.

### 코드 블럭들

우리는 변수를 "지역 스코프"로 독립시키기 위해서 코드블럭을 사용할 수도 있습니다.

예를 들면, 웹 브라우저에서, 모든 스크립트 (`type="module"`을 제외한)가 같은 전역 영역을 공유합니다. 그래서 우리가 만일 한 스크립트 내부에서 전역 변수를 만들면, 이 전역 변수는 다른 스코프에서도 접근 가능합니다. 하지만 만일 두 스크립트가 같은 변수 이름을 사용하고 서로를 덮어씌운다면, 전역 변수는 충돌의 원인이 됩니다.

변수 이름이 널리 알려진 단어이고 스크립트 작성자들이 서로에 대해 알지 못한다면 이러한 일이 발생할 수 있습니다.

만일 우리가 이러한 문제를 피하고 싶다면, 전체 스크립트 또는 일부를 독립시키기 위해서 우리는 코드블럭을 사용할 수 있습니다.

```js
{
  // do some job with local variables that should not be seen outside

  let message = "Hello";

  alert(message); // Hello
}

alert(message); // Error: message is not defined
```

블럭 바깥의 코드(또는 다른 스크립트 내부의 코드)는 블럭 내부의 코드를 볼 수 없습니다. 왜냐하면 그 블럭은 자신만의 어휘 환경을 가졌기 때문입니다.

### IIFE

과거에는, 블럭-레벨 어휘 환경이란 것이 자바스크립트에 존재하지 않았습니다.

그래서 프로그래머들은 무언가를 발명해야 했습니다. 그리고 그들이 발명한 것은 "즉시 호출되는 함수 표현식(Immediately-Invoked Function Expression)인 IIFE입니다.

IIFE는 이제 우리가 반드시 써야하는 것은 아닙니다. 하지만 오래된 코드에서 IIFE를 찾아볼 수 있습니다. 그래니 이해하고 넘어가면 좋습니다.

IIFE는 다음과 같은 형태를 띕니다.

```js
(function() {

  let message = "Hello";

  alert(message); // Hello

})();
```

여기 함수 표현식이 만들어졌고 즉시 호출됩니다. 그래서 코드는 즉시 실행되고 자신의 private 변수들을 지닙니다.

함수 표현식은 괄호로 둘러싸여 있습니다. `(function {...})`, 왜냐하면 자바스크립트가 메인 코드 흐름에서 `"function"`을 만났을 때, 자바스크립트는 이것을 함수 선언의 시작으로 이해합니다. 하지만 함수 선언은 이름을 가져야 합니다. 그래서 아래의 코드는 에러를 방출합니다.

```js
// Try to declare and immediately call a function
function() { // <-- Error: Unexpected token (

  let message = "Hello";

  alert(message); // Hello

}();
```

우리가 "괜찮아, 이름을 정의하면 되지" 라고 말한다고 해도, 여전히 작동하지 않습니다. 자바스크립트는 함수 선언이 즉시 호출되는 것을 허락하지 않습니다.

```js
// syntax error because of parentheses below
function go() {

}(); // <-- can't call Function Declaration immediately
```

그래서, 함수 주변에 괄호들을 붙이는 것은 함수가 다른 표현식의 컨텍스트에 만들어지는 것을 자바스크립트에게 보여주기 위한 트릭입니다. 그래서 이것은 함수 표현식이 됩니다. 이 함수 표현식은 이름도 필요 없고 즉시 불려질 수 있습니다.

괄호 말고도 자바스크립트에게 우리가 함수 표현식을 쓰려고 했던 것이라고 말할 수 있는 다른 방법들도 있습니다.

```js
// IIFE 만드는 방법
(function() {
  alert("Parentheses around the function");
})();

(function() {
  alert("Parenthjeses around the whole thing");
})();

!function() {
  alert("Bitwise NOT operator starts the expression");
}();

+function() {
  alert("Unary plus starts the expression");
}();
```

위의 경우들에서, 우리는 함수 표현식을 선언하고 즉시 실행합니다.

## 가비지 컬렉션

주로, 어휘 환경은 함수 실행 이후에 clean-up되고 지워집니다. 예를 들면 :

```js
function f() {
  let value1 = 123;
  let value2 = 456;
}

f();
```

여기 두 값들이 기술적으로 어휘 환경의 프로퍼티입니다. 하지만 `f()` 가 끝나고, 그 어휘 환경이 접근 불가능하게 된 후에, 이 어휘 환경이 메모리에서 지워집니다.

하지만 만일 `f`의 끝에 여전히 접근 가능한 중첩된 함수가 존재한다면, 그 때 이 `[[Environment]]` 참조는 외부 어휘 환경을 다음과 같이 살려놓습니다.

```js
function f() {
  let value = 123;
  function g() { alert(value); }
  
  return g;
}

let g = f(); // g는 f 내부의 value에 접근 가능하여 외부 어휘 환경을 메모리에 유지시킵니다.
```

알아둬야 할 것은 만일 `f()`가 많이 호출됐고, 결과 함수들이 저장됐다면, 그 때 상응하는 어휘 환경 오브젝트들 또한 메모리에 남게될 것입니다. 아래 코드에서 3개 전부에 해당합니다.

```js
function f() {  
  let value = Math.random();
  
  return function() {alert(value);};
}

// 배열 안에 3개의 함수가 있고
// 각각은 각각의 어휘 환경과 연결된다.  
let arr = [f(), f(), f()];
```

하나의 어휘 환경 오브젝트는 접근 불가능할 때(다른 오브젝트들과 같이), 죽습니다. 다른 말로 하면, 하나의 중첩된 함수라도 참조하고 있는 동안에만 존재합니다.

아래의 코드에서, `g`가 접근 불가능하게 된 이후에, 에워싼 어휘 환경이 메모리에서 사라집니다.

```js
function f() {
  let value = 123;
  function g() {alert(value);}
  return g;
}

let g = f(); // g가 살아있는 동안
// 상응하는 어휘 환경이 살아있다.

g = null; // 이젠 메모리에서 사라짐
```

### 현실 최적화

우리가 본 것처럼, 이론적으로 함수가 살아있는 동안에는, 모든 외부 변수들이 유지되고 있습니다.

하지만, 실제로, 자바스크립트 엔진은 그것을 최적화하려 합니다. 자바스크립트 엔진은 변수 사용을 분석하고 만일 외부 변수가 사용되지 않는 것을 쉽게 파악할 수 있다면, 지워버립니다.

**V8 (Chrome, Opera)에서의 중요한 부작용은 이러한 변수를 디버깅할 때, 볼 수 없다는 것입니다.** 

아래의 예제를 크롬에서 개발자 도구를 열어 실행해보세요.

아래의 코드가 멈췄을 때, 콘솔에서 `alert(value)`를 타이핑해보세요.

```js
function f() {
  let value = Math.random();

  function g() {
    debugger; // in console: type alert( value ); No such variable!
  }

  return g;
}

let g = f();
g();
```

여러분도 볼 수 있듯, 그러한 변수가 존재하지 않습니다! 이론상으로는, 접근이 가능해야 하는데 엔진이 최적화를 진행해버렸습니다.

이러한 현상은 재미있는 (만일 그렇게 시간이 소비되지 않는다면) 디버깅 이슈를 초래할 수 있습니다. 그들 중 우리가 예측한 것 대신에 같은 이름의 외부 변수를 볼 수 있습니다.

```js
let value = "Surprise!";

function f() {
  let value = "the closest value";

  function g() {
    debugger; // in console: type alert( value ); Surprise!
  }

  return g;
}

let g = f();
g();
```

> **나중에 봅시다!** 이러한 V8의 특징은 알아놓으면 좋습니다. 만일 여러분이 Chrome/Opera를 이용해 디버깅한다면, 빠르던 늦던 언젠가 알게 될 특성이었습니다. 이것은 디버거의 버그가 아닙니다. 오히려 V8의 특별한 기능입니다. 아마 언젠가 이러한 기능이 수정될 것입니다. 여러분은 언제나 이 페이지의 예제를 돌려보며 체크해 볼 수 있습니다.

# 과제

## 카운터는 독립적일까?
> 중요도 : 5

여기에 우리는 두개의 카운터를 만듭니다: 같은 `makeCounter`함수를 이용한 `counter`와 `counter2` 입니다.

이 두개는 독립적일까요? 두번째 카운터는 무엇을 보여줄까요? `0,1` 또는 `2,3` 또는 다른 것을 보여줄까요?

```js
function makeCounter() {
  let count = 0;
  
  return function() {
    return count++;
  };
}

let counter = makeCounter();
let counter2 = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1

alert( counter2() ); // ?
alert( counter2() ); // ?
```

> 정답은 : **0, 1** 입니다. 함수 `counter` 그리고 `counter2`는 `makeCounter`의 다른 호출에 의해 생성되었습니다. 그래서 그들은 독립적인 외부 어휘 환경을 갖고 있습니다. 각각은 자신의 `count`를 가지고 있습니다.

## 카운터 객체
> 중요도 : 5

여기에 카운터 객체가 생성자 함수의 도움을 통해 만들어져 있습니다.

이게 동작 할까요? 어떻게 보여질까요?

```js
function Counter() {
  let count = 0;
  this.up = function() {
    return ++count;
  };
  this.down = function() {
    return --count;
  };
}

let counter = new Counter();

alert(counter.up()); // ?
alert(counter.up()); // ?
alert(counter.down()); // ?
```

> 정답은 : 당연히 제대로 작동합니다. 두 개의 중첩된 함수들은 같은 외부 어휘 환경 내부에서 만들어졌습니다. 그래서 그들은 같은 `count` 변수를 공유합니다. 1, 2, 1을 차례로 alert합니다.

## if 내부에 들어있는 함수

코드를 보세요. 마지막 줄의 호출 결과는 무엇이 될까요?

```js
let phrase = "Hello";

if (true) {
  let user = "John";
  
  function sayHi() {
    alert(`${phrase}, ${user}`);
  }
}

sayHi() // 마지막 줄
```

> 정답은 : **에러** 입니다. 함수 `sayHi`는 `if` 내부에 선언되어 있습니다. 그래서 블럭 내부에서만 살아있습니다. 외부에는 `sayHi`가 존재하지 않습니다.

## 클로져가 있는 Sum
> 중요도: 4

`sum(a)(b) = a+b`와 같은 결과를 반환하는 `sum`을 작성하세요.

네, 두 개의 괄호를 이용해보세요. (오타가 아닙니다)

예를 들면
```
sum(1)(2) = 3;
sum(5)(-1) = 4;
```

> 정답은 : 

```js
function sum(a) {
  return function sum2(b) {
    return a+b;
  }
}
```

입니다. 

## 함수로 필터링하기
> 중요도: 5

우리는 배열을 위한 `arr.filter(f)` 빌트인 메소드를 갖고 있습니다. 이 메소드는 모든 요소를 함수 `f`를 이용하여 필터링합니다. 만일 이 메소드가 `true`를 반환하면, 그 엘리먼트는 결과 배열에 포함됩니다.

"사용할 준비가 된" 필터의 셋을 만듭시다:

- `inBetween(a, b)` - `a`와 `b` 사이 또는 두 개의 값과 같은 경우.
- `inArray([...])` - 주어진 배열 내부.

사용 용례는 다음과 같습니다.

- `arr.filter(inBetween(3,6))` - 3과 6사이의 값만 선택
- `arr.filter(inArray([1, 2, 3]))` - `[1, 2, 3]`의 멤버 중 매칭되는 것만 선택

예를 들면: 

```js
/* 여기에 inBetween과 inArray를 작성하세요 */

let arr = [1, 2, 3, 4, 5, 6, 7];

alert( arr.filter(inBetween(3, 6)) ); // 3, 4, 5, 6
alert( arr.filter(inArray([1, 2, 10])) ); // 1, 2
```

> 정답은 :

> inBetween

```js
function inBetween(a, b) {
  return function(x) {
    return x >= a && x <= b;
  };
}

let arr = [1, 2, 3, 4, 5, 6, 7];
alert( arr.filter(inBetween(3, 6)) ); // 3,4,5,6
```

> inArray

```js
function inArray(arr) {
  return function(x) {
    return arr.includes(x);
  };
}

let arr = [1, 2, 3, 4, 5, 6, 7];
alert( arr.filter(inArray([1, 2, 10])) ); // 1,2
```

> 참고) 번역자 작성 버전 (화살표 함수 사용)

> inBetween

```js
let inBetween = (a, b) => ((e) => (e >= a) && (e <= b));
```

> inArray

```js
let inArray = (arr) => ((e) => (arr.indexOf(e) !== -1));
```

