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

