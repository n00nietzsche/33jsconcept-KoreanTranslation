## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://dev.to/promhize/what-you-need-to-know-about-javascripts-implicit-coercion-e23 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #9 자바스크립트 이벤트 루프

"어떻게 자바스크립트는 싱글스레드이면서 비동기인 걸까?"에 대한 짧은 답은 자바스크립트 언어는 싱글스레드이고 비동기 행위들은 엄밀히 말하면 자바스크립트 언어 그 자체의 일부는 아닙니다. 오히려 비동기 행위는 브라우저 내부(혹은 프로그래밍 환경)에 존재하는 자바스크립트 언어의 핵심(Core) 단의 상위에 만들어져있죠. 그리고 브라우저의 API를 통해 접근합니다.

# 기본 아키텍쳐

![js architecture.png](https://images.velog.io/post-images/jakeseo_me/b51d4c60-64c2-11e9-9b04-61e622359bb6/js-architecture.png)
> 브라우저 내부의 주요 컴포넌트들에 대한 개요

- **힙 영역** : 객체는 메모리에서 대규모이면서 대부분 구조화 되지 않은 메모리 영역인 힙영역 내부에 할당됩니다.

- **스택 영역** : 자바스크립트 코드 실행을 위해 제공된 싱글 스레드를 나타냅니다. 함수 호출은 frame의 스택을 구성합니다.

- **브라우저 또는 웹 API** : 브라우저와 웹 API는 웹 브라우저 내부에 구성되며 브라우저로 혹은 주변 컴퓨터 환경으로부터 데이터를 노출시킬 수 있고 이것들을 통해 유용하지만 복잡한 것들도 할 수 있습니다. `사실 이것들은 자바스크립트 언어 그 자체는 아니고 오히려 자바스크립트 언어 코어 단의 상위에 만들어져, 우리가 자바스크립트 코드 사용 시에 추가적인 초능력(superpowers)을 제공합니다`. 예를 들면 [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)는 지역 데이터를 가져오기 위한 간단한 자바스크립트 구조를 제공합니다. 그래서 구글맵에 위치를 표기할 수 있습니다. 백그라운드에서 브라우저는 디바이스의 GPS 하드웨어와 통신하기 위해 (또는 위치 데이터를 결정할 수 있는 무엇이든) 사실 C++ 같은 복잡한 로우레벨 코드를 사용하는 중입니다. 위치 데이터를 불러오고 코드에서 사용할 수 있도록 브라우저 환경에 이것을 반환합니다. 하지만 이러한 복잡성은 다시 API에 의해 추상화되어 있습니다.

# 코드 1: 사고방식 깨우기

```js
function main() {
  console.log('A');
  setTimeout(
    function display() { console.log('B'); }
  , 0);
  console.log('C');
}

main();
// 출력
// A
// C
// B
```

우리는 'A'와 'C'를 콘솔에 로깅하는 2개의 `console.log`를 가진 main 함수를 갖고 있습니다. 그리고 그 사이에 'B'를 콘솔에 로깅하는 0ms의 딜레이를 가진 setTimeout 함수를 호출합니다.

![js eventloop.png](https://images.velog.io/post-images/jakeseo_me/880f3bc0-64c7-11e9-b2ea-0394058a2650/js-eventloop.png)
> 코드 실행 중 내부에서 일어나는 일들

1. 메인 함수에 대한 호출이 먼저 프레임으로 stack에 push 됩니다. 그 후에 브라우저가 메인 함수 내부의 첫번째 statement(`console.log('A')`)를 stack에 넣습니다. 이 statement가 실행되고 완료되자마자 해당 프레임은 스택에서 pop 됩니다. 알파벳 A가 콘솔에 표기됩니다.

2. 다음 statement (콜백 `exec()`과 함께 0ms의 딜레이를 가진 `setTimeout()`)가 콜스택으로 push되고 실행됩니다. setTimeout 함수는 제공된 콜백을 딜레이하기 위해 브라우저 API를 사용합니다. 일단 타이머를 돌리기 위해 콜백이 브라우저로 넘어가면 `setTimeout()`을 가진 프레임은 pop됩니다.

3. 브라우저에서 `exec()` 실행을 위한 타이머가 돌아가는 도중에 console.log('C')가 콜스택에 push됩니다. 이러한 경우에는 제공된 딜레이는 0ms 였기 때문에, 콜백은 브라우저가 콜백을 받자마자 메시지 큐에 바로 추가됩니다. (이상적인 경우)

4. 메인 함수에서 마지막 statement의 실행 후에 main() 프레임은 콜스택밖으로 pop됩니다. 그러면서 콜스택은 빈(empty) 상태가 됩니다. 브라우저가 어떤 메시지를 큐에서 콜스택으로 push하기 위해서는 먼저 콜스택을 반드시 비워야 합니다. 이게 `setTimeout()`의 딜레이가 0초였음에도 불구하고, `exec()`으로의 콜백이 콜스택에 존재했던 모든 프레임이 실행될 때까지 기다려야 했던 이유입니다.

5. 이제야 콜백 exec()이 콜스택에 푸시되고 실행됩니다. 그 다음 알파벳 C가 콘솔에 나타납니다. 이게 자바스크립트의 이벤트 루프입니다.

> 그래서 setTimeout(function, delayTime)에 들어가는 delay 파라미터는 어떤 함수가 실행된 뒤의 정확한 시간 딜레이를 말하는 것이 아닙니다. delay 파라미터는 함수가 실행됐을 때의 어떤 지점이후의 최소 대기시간을 의미하는 것입니다.

# 코드 2: 더 깊은 이해

```js
function main() {
  console.log('A');
  setTimeout(
    function exec() { console.log('B'); }
  , 0);
  runWhileLoopForNSeconds(3);
  console.log('C');
}

main();

function runWhileLoopForNSeconds(sec) {
  let start = Date.now(), now = start;
  while (now - start < (sec*1000)) {
    now = Date.now();  
  }
}

// Output
// A
// C
// B
```

![js eventloop2.png](https://images.velog.io/post-images/jakeseo_me/f8d80f00-65a1-11e9-891e-093443103677/js-eventloop2.png)

- 함수 `runWhileLoopForNSeconds()`는 정확히 이름과 같은 일을 합니다. 함수가 호출된 시간에서 경과된 시간을 계속 측정하여 경과된 시간이 함수의 인자로 받은 시간과 일치하는지 계속해서 검증합니다. 기억해야할 메인포인트는 while 반복문이 콜스택에서 상주하면서 브라우저 API를 사용하지 못하게 하는 blocking statement라는 것입니다. 이 함수는 실행이 끝날 때까지 뒤에 오는 모든 statements들이 실행되지 못하게 막습니다.
- 그래서 위의 코드에서, 비록 setTmeout은 0초의 딜레이를 가지고 while 반복문은 3초간 실행되더라도, exec() 콜백은 메시지 큐에 갇혀있습니다. while 반복문은 3초가 지날 때까지 콜스택 위에서 계속 실행됩니다. (js의 엔진은 싱글스레드니까요) 3초가 지나 콜스택이 비게 되는 순간에 exec() 콜백은 콜스택으로 들어와서 실행됩니다.
- setTimeout()의 딜레이 인자는 실행이 시작되는 타이밍을 보장해주는 것이 아닙니다. 최소한 얼마정도는 있다가 실행되라 정도의 의미로 보는 것이 옳습니다.
