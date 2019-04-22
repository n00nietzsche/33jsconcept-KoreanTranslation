# 들어가기 전에

- 이 포스팅은 https://medium.com/@vvkchandra/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://medium.com/@vvkchandra/essential-javascript-mastering-immediately-invoked-function-expressions-67791338ddc6 If the original author requests deletion, it will be deleted immediately.

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #8 자바스크립트 필수요소 : IIFE 마스터하기

함수를 확실하게 이해하고, 현대적이고 깔끔한 자바스크립트 코드로 작성할 수 있는 방법을 배우는 것은 자바스크립트 닌자가 되기 위한 필수 스킬입니다.

자바스크립트 함수와 함께 자주 사용되는 코딩 패턴 중 하나는 ***Immediately-invoked Function Expression***이라는 멋진 이름을 갖고 있습니다. ***IIFE*** 로 잘 알려져 있고 발음할 때는 ***"iffy"*** 처럼 발음합니다.

IIFE가 무엇인지 왜 필요한지 이해하기 전에, 자바스크립트 함수에 관한 핵심적인 몇가지 개념들을 빠르게 다시 짚고 넘어갈 필요가 있습니다.

# 자연스러운 함수 정의

자바스크립트를 처음 접하는 개발자들은 함수를 다룰 때 다음 문법이 편할 것입니다.

```js
function sayHi() {
  alert("Hello, World!");  
}

sayHi();
```

1. 1-3번째 줄은 ***sayHi()*** 라는 이름의 함수를 정의합니다.
2. 5번째 줄에서는 ***"()"*** 문법을 이용해 정의한 함수를 불러옵니다.

이렇게 함수를 생성하는 방식은 "a function definition" 또는 "a function declaration" 또는 "a function statement"로 불립니다. 일반적으로, 다른 인기있는 프로그래밍 언어의 문법과 닮았기 때문에, 자바스크립트를 처음 접하는 개발자도 이 문법을 사용하는데 별다른 문제를 겪지 않습니다.

> 이 함수 정의는 항상 **function** 키워드로 시작합니다. 그리고 뒤에는 함수의 이름이 따라옵니다. 함수의 이름을 생략하면 문법에 어긋나기 때문에 이름을 생략할 수 없습니다.

# 함수 표현식

이제 자바스크립트에 관한 것들이 재밌어질 때입니다. 함수 표현식이 어떻게 생겼는지 살펴봅시다.

```js
var msg = "Hello, World!";
var sayHi = function() {
  alert(msg);  
};

sayHi(); // 브라우저에서 "Hello, World!"라는 alert 메시지를 띄웁니다.
```

이 간단한 예제는 우리 자바스크립트 스킬이 다음 레벨로 넘어가도록 도와줄 수 있습니다.

1. 1번째 줄은 ***msg*** 변수를 선언하고 ***string*** 값을 할당합니다.
2. 2-4번째 줄은 ***sayHi*** 변수를 선언하고 ***function*** 타입의 값을 할당합니다.
3. 6번째 줄은 ***sayHi*** 함수를 호출합니다.

1번째 줄은 이해하기 매우 쉽습니다. 하지만 개발자들이 2-4번째 줄을 처음 볼 때, 자바같은 프로그래밍 언어만 경험한 개발자들에게는 그 코드들은 예상을 벗어납니다.

> 기본적으로, 2-4번째 줄에서는 함수 타입의 값을 ***sayHi*** 라는 변수에 할당했습니다.

> 위의 예제에서, 할당의 right-hand에 있는 함수는 주로 ***"함수 표현식(Function Expression)"*** 이라 불립니다. 자바스크립트 내 어디든 있습니다. 
