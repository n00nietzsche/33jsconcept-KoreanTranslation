## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

## 자바스크립트에서 값(Value)과 참조(Reference)
> 컴퓨터 메모리를 잠시 들여다 보면 그 안에 해답이 있습니다.

![메모리 이미지.jpeg](https://images.velog.io/post-images/jakeseo_me/ddd4f260-51bd-11e9-a806-8f3a20f1c19e/메모리-이미지.jpeg)

> 참고 : 이 글은 [Step Up Your JS: A Comprehensive Guide to Intermediate JavaScript](https://www.educative.io/collection/5679346740101120/5707702298738688?authorName=Arnav%20Aggarwal) 이 링크에 존재하는 저자의 강의 중 일부를 저자가 재구성하여 작성한 글입니다.. [Quiz Section](https://www.educative.io/collection/page/5679346740101120/5707702298738688/5685265389584384) 이 링크에서 저자가 만든 인터렉티브한 퀴즈를 풀 수 있습니다.

자바스크립트는 ***값에 의한 전달(passed by value)*** 이 일어나는 5가지의 데이터타입(`Boolean`, `Null`, `Undefined`, `String`, `Number`)을 가지고 있습니다. 우리는 이러한 데이터 타입을 ***원시 타입(Primitive Types)*** 이라고 부릅니다. 

또 자바스크립트는 ***참조에 의한 전달(passed by reference)*** 이 일어나는 3가지의 데이터 타입(`Array`, `Function`, `Object`)도 가지고 있습니다. 사실 이 3가지는 크게 보자면 전부 `객체(Objects)`로 볼 수 있습니다.

## 원시타입(Primitives)
어떠한 원시타입이 변수에 할당된다면, 우리는 그 변수를 원시타입을 가진 변수라고 생각할 수 있습니다.

```javascript
var x = 10;
var y = 'abc';
var z = null;
```

이 경우에는 `x`는 `10`이란 값을 가지고 있고, `y`는 `abc`란 값을 가지고 있습니다. 이러한 것들을 이해했다면 메모리 상에 존재하는 변수들을 다음과 같은 이미지로 나타낼 수 있을 것입니다.


![메모리상 이미지.png](https://images.velog.io/post-images/jakeseo_me/2b321030-51c1-11e9-87aa-df2c53a2fd5f/메모리상-이미지.png)

우리가 이 변수들을 다른 변수에 `=`이라는 키워드를 이용하여 할당할 때, 우리는 새로운 변수에 값을 **복사(Copy)** 하게 됩니다. 이 변수들은 값에 의해 복사되죠.

```javascript
var x = 10;
var y = 'abc';

var a = x;
var b = y;

console.log(x, y, a, b); // -> 10, 'abc', 10, 'abc'
```

`a`와 `x`는 둘 다 지금 `10`이란 값을 갖고 있습니다. `b`와 `y`는 현재 둘 다 `'abc'`라는 값을 갖고 있구요. 하지만 이들은 분리되어 있습니다. 값들이 복사되었기 때문이죠.

![메모리상 이미지2.png](https://images.velog.io/post-images/jakeseo_me/0432ada0-5414-11e9-9723-eb39f385be66/메모리상-이미지2.png)

같은 값을 가진 변수 하나를 바꾸더라도 다른 변수에는 아무런 영향이 없습니다. 각각의 변수들이 아무런 관계도 없다고 생각해도 무방하겠죠.

## 객체(Objects)
이번에 배울 내용은 어렵게 느껴질 수 있습니다. 하지만 천천히 읽어나가고 이해한 뒤에는 아마 쉽다고 느껴질 거에요.

원시 타입이 아닌 값(non-primitive value)이 할당된 변수들은 그 값으로 향하는 *참조(reference)* 를 갖게 됩니다. *참조(reference)* 는 메모리에서의 객체의 위치를 가리키고 있습니다. 변수는 실제로 값을 가지고 있지 않습니다.

객체는 우리의 컴퓨터 메모리 어딘가에 생성됩니다. 우리가 `arr = []` 를 작성했을 때, 우리는 메모리 내부에 배열을 만든 것입니다. 변수 `arr`이 갖는 것은 그 배열이 위치한 주소입니다.

앞으로 아래와 같은 그림을 볼 때는 `address`가 원시타입과 같이 값에 의한 전달(passed by value)을 하는 새로운 종류의 데이터 타입이라고 가정해봅시다. `address`는 참조에 의한 전달을 하는 위치를 가리키게 될 것입니다. 문자열이 `' '` 또는 `" "` 사이에 표기되듯 `address`는 `< >` 사이에 표기될 것입니다.

우리가 참조 타입(reference-type)의 변수를 할당하고 사용할 때, 우리가 쓰고 보는 것은 다음과 같은 코드입니다.

```javascript
1) var arr = [];
2) arr.push(1);
```

메모리 안에서 1과 2의 표기는 다음과 같습니다.

1.
![arr1.png](https://images.velog.io/post-images/jakeseo_me/95025c90-5469-11e9-ab21-e18f506a09b0/arr1.png)

2.
![arr2.png](https://images.velog.io/post-images/jakeseo_me/97722410-5469-11e9-ab21-e18f506a09b0/arr2.png)

`arr`이 가진 변수의 값(주소)은 정적임을 인지해야 합니다. 변수의 값이 바뀌는 것이 아닌 메모리 속의 배열 값만 바뀌는 것입니다. 우리가 무언가 하려고 `arr`을 사용할 때, 이를테면 값의 push같은 일을 할 때, 자바스크립트 엔진은 메모리 속의 `arr`의 위치로 갑니다. 그리고 거기에 저장된 정보를 이용하여 작업을 수행하죠.

### 참조로 할당하기
객체와 같은 참조 타입의 값이 `=`과 같은 키워드를 이용하여 다른 변수로 복사될 때, 그 값의 주소는 실제로 복사됩니다. 마치 원시타입의 할당처럼요. 객체는 값 대신 참조로 복사됩니다.

```javascript
var reference = [1];
var refCopy = reference;
```

위와 같은 코드는 메모리상에서는 아래와 같이 표기됩니다.

![arr3.png](https://images.velog.io/post-images/jakeseo_me/a6522a60-546a-11e9-ab21-e18f506a09b0/arr3.png)

각각의 변수는 이제 같은 배열로 향하는 레퍼런스를 갖습니다. 이 말은 우리가 `reference`나 `refCopy`를 건드려보면 다음과 같은 결과를 얻게 됨을 의미합니다.

```javascript
reference.push(2);
console.log(reference, refCopy); // -> [1, 2], [1, 2]
```

우리는 `2`를 메모리 속에 있는 배열에 `push` 했습니다. 우리가 `reference`와 `refCopy`를 사용할 때 우리는 같은 배열을 가리키게 됩니다.

### 참조 재할당하기
참조 값을 재할당 하는 것은 오래된 참조를 대체합니다.

```javascript
var obj = { first: 'reference' };
```

메모리 상태 :

![arr4.png](https://images.velog.io/post-images/jakeseo_me/fbb45a20-546d-11e9-ab21-e18f506a09b0/arr4.png)

우리가 두번째 줄을 입력한다면:

```javascript
var obj = { first: 'reference' };
obj = { second: 'ref2' }
```

`obj`안에 저장됐던 주소 값은 변경됩니다. 첫번째 객체는 아직 메모리 상에 표기가 되긴 합니다. 다음과 같이 말이죠.

![arr5.png](https://images.velog.io/post-images/jakeseo_me/449e94d0-546e-11e9-a095-d742f66aa765/arr5.png)

남아있는 객체를 가리키는 참조가 남아있지 않을 때, 위의 사진에 보이는 주소 값 `#234`이 보이는 것처럼, 자바스크립트 엔진은 `가비지 컬렉션(garbage collection)`을 동작시킬 수 있습니다. 이것은 프로그래머가 모든 참조를 날리고 객체를 더이상 사용할 수 없게 된 뒤 자바스크립트 엔진은 그 주소로 가 사용되지 않는 객체를 메모리로부터 안전하게 지워버리는 것을 의미합니다. 이 경우에는 객체 `{ first: 'reference' }`가 더이상 접근 불가능하고 가비지 콜렉션 될 수 있습니다.

### ==와 ===
동등함을 비교하는 연산자 `==`와 `===`는 참조 타입의 변수를 비교할 때 사용됩니다. 이 연산자들은 참조를 체크합니다. 만일 변수가 같은 item에 대한 참조를 갖고 있다면, 비교 결과는 `true`가 나올 것입니다.

```javascript
var arrRef = ['Hi!'];
var arrRef2 = arrRef;

console.log(arrRef === arrRef2); // -> true
```

우리가 만일 2개의 구분 가능한 객체들을 갖고 있고 그들의 프로퍼티가 동일한지 보고 싶다면, 가장 쉬운 방법은 그 둘을 문자열로 바꾸고 문자열을 비교하는 것입니다. 동등 연산자가 원시 타입을 비교할 때는, 그저 값이 같은지만 확인합니다.

```javascript
var arr1str = JSON.stringify(arr1);
var arr2str = JSON.stringify(arr2);

console.log(arr1str === arr2str); // true
```

또 다른 선택지로는 객체를 이용해 재귀적으로 반복을 도는 것입니다. 그리고 각각의 프로퍼티가 동일한지 확인할 수 있겠죠.

### 함수를 통한 파라미터의 전달
우리가 원시 값들을 함수로 전달할 때, 함수는 값들을 복사하여 파라미터로 전달합니다. 이것은 `=`연산자를 이용하는 것과 같습니다.

```javascript
var hundred = 100;
var two = 2;

function multiply(x, y) {
	// PAUSE
	return x * y;
}

var twoHundred = multiply(hundred, two);
```

위의 예제에서 우리는 `hundred`라는 변수에 `100`이라는 값을 주었습니다. 우리가 이 값을 `multiply`로 넘겼을 때,변수 `x`는 그 값(`100`)을 갖게 됩니다. 값은 우리가 `=`연산자를 써서 할당한 것 처럼 복사됩니다. 또, 인자로 넘겨진 `hundred`라는 변수에는 아무런 영향도 미치지 않습니다. 위의 소스 코드 중에 PAUSE 상태에서 메모리가 어떻게 구성되는지에 대한 스냅샷은 아래와 같습니다.

![arr6.png](https://images.velog.io/post-images/jakeseo_me/aa4a57e0-5470-11e9-a095-d742f66aa765/arr6.png)

### 순수 함수(Pure Functions)
함수 중에 함수 바깥 스코프에 아무런 영향도 미치지 않는 함수를 우리는 *순수 함수(pure functions)* 라고 합니다. 함수가 오직 원시 값들만을 파라미터로 이용하고 주변 스코프에서 어떠한 함수도 이용하지 않는다면, 그 함수는 자연스레 순수함수가 됩니다. 당연히 바깥 스코프에는 아무런 영향도 끼칠 수 없고요. 안에서 만들어진 모든 변수들은 함수에서 반환(return)이 실행되는 즉시 가비지 콜렉션 처리가 됩니다.

객체를 받는 함수는 주변 스코프들의 상태를 변화시킬 수 있습니다. 만일 함수가 배열 참조값을 가진 변수를 받고 그 변수가 가리키는 배열에 push를 수행하면, 그 주변 스코프에 존재하는 변수들과 그 참조(reference)와 그 배열이 변하는 것을 볼 수 있습니다. 함수 리턴 후에, 변화된 것들은 바깥 스코프에 여전히 남아있겠죠. 이런 현상은 우리가 원하지 않는 방향으로 부작용(side-effect)을 줄 수 있습니다. 디버깅 하려해도 찾아내기도 힘들죠.

`Array.map`과 `Array.filter`를 포함한 많은 네이티브 배열 함수들은 그래서 순수 함수로 작성되어 있습니다. 배열 참조를 받아서 내부적으로 배열을 복사하고 원본 대신 복사된 배열로 작업합니다. 그래서 원본도 건드리지 않고 바깥 스코프에 영향도 미치지 않고 새로운 배열의 참조를 반환하게 되죠.

여기서 순수 함수와 순수함수가 아닌 것을 비교해봅시다.

