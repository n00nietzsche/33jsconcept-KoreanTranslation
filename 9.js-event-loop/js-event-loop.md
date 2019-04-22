## 들어가기 전에
- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 포스팅들을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://dev.to/promhize/what-you-need-to-know-about-javascripts-implicit-coercion-e23 If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #9 자바스크립트 이벤트 루프

"어떻게 자바스크립트는 싱글스레드이면서 비동기인 걸까?"에 대한 짧은 답은 자바스크립트 언어는 싱글스레드이고 비동기 행위들은 엄밀히 말하면 자바스크립트 언어 그 자체의 일부는 아닙니다. 오히려 비동기 행위는 브라우저 내부(혹은 프로그래밍 환경)에 존재하는 자바스크립트 언어의 핵심(Core) 단에 만들어져있죠. 그리고 브라우저의 API를 통해 접근합니다.

# 기본 아키텍쳐

