## 출처

- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec 글을 제 멋대로 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec If the original author requests deletion, it will be deleted immediately.

## 자바스크립트 함수 실행에 대한 이해 (콜 스택, 이벤트 루프, Task 그리고 그 외 등등...)
 웹 개발자 또는 프론트 엔드 개발자라 불리는 사람들은 요즘 브라우저 내에서 상호작용하는 액션부터 컴퓨터 게임, 데스크탑 위젯, 크로스 플랫폼 모바일 어플리케이션, DB와 연결하는 서버 사이드 코딩 등 모든 것들을 스크립팅 언어로 구현해버립니다. 이렇게 많은 일을 하는 자바스크립트를 더욱 효율적으로 사용할 수 있도록 자바스크립트의 내부 동작을 아는 것이 이 포스팅의 목적입니다.
 
 자바스크립트 환경(ecosystem)은 그 어느 때보다 복잡해졌고 앞으로 더욱 복잡해질 것입니다. 현대적인 모바일 웹앱을 만들기 위해 요구되는 것은 너무나 많습니다. 웹팩, 바벨, ESLint, Mocha, Karma, Grunt ... etc... 우리는 무엇을 사용해야 하고 이 많은 툴들은 무슨 일을 하는 걸까요? 저는 현대 웹 개발자들이 겪고있는 어려움을 완벽히 그려낸 이 웹툰을 찾았습니다.
 
![modernwebdeveloperwebcomic.png](https://images.velog.io/post-images/jakeseo_me/631a6750-44cd-11e9-a7a4-4f4044b4f0ac/modernwebdeveloperwebcomic.png)

 각각의 인물의 대화는 주황색 옷 A, 초록색 옷 B로 표현하겠습니다  
 > (원시인들의 말이라 문법이 약간 어색합니다.)
 
  > A: OG는 고기를 먹고 싶다. 그냥 입에 넣으면 되지. 그치?  
  > B: 아니! 불을 사용해! 고기를 요리해!
  
  > A: 불 뜨거워! 어떻게 손을 안 데이지?  
  > B: 뾰족한 집게를 사용하거나! 뜨거운 돌을 사용하거나! 박스에 불을 담아봐! 나도 잘 몰라.
  
  > A: 선택지가 너무 많아! 어떻게 고르지?  
  
  > A: 그래! 고기 위에 불을 지르면 되겠다. 이제 먹어도 되지?  
  > B: 안돼! 기다려! 요리하고 소금을 좀 얹고 향신료를 좀 더해봐!
  
  > A: 니 말은 고기를 먹기 위해서 내가 불과 막대와 기다림과 돌과 나뭇잎이 필요하다는 거야?  
  > 	  난 그냥 생으로 먹을 거야 멍청아
  >    꺼ㅡ억
  
  > 개발자: 이게 바로 현대 자바스크립트가 너무 복잡한 이유야. 
 
 이 모든 것들은 제쳐두고, 모든 자바스크립트 개발자들이 어떤 프레임워크나 라이브러리를 사용하려고 깊이 마음먹기 전에 필요한 것은 루트 레벨에서 이 모든 것들이 어떻게 이루어지는지 그 기초적인 토대에 대해 알아야 한다는 것입니다. 일반적으로 자바스크립트 개발자들은 크롬의 런타임 'V8' 이라는 용어에 대해 들어본 적이 있을 것입니다. 하지만 몇몇 개발자들은 그게 진짜로 의미하는 것과 어떤 일을 하는지에 대해 모릅니다. 저는 직업적인 개발자로서의 커리어의 첫 1년간 이러한 난해한 용어들에 대해 많이 알지 못했고 게다가 첫 1년은 그저 일을 끝내는 것이 우선이었죠. 이런 상황은 자바스크립트는 이 모든 일들을 어떻게 해나가는가에 대한 저의 궁금증을 전혀 해소시켜주지 못했습니다. 저는 더 깊게 파보고 구글링을 더 해보기로 마음먹었습니다. 그리고 Philip Robers(https://twitter.com/philip_roberts), a great talk at JSConf on the event loop(https://www.youtube.com/watch?v=8aGhZQkoFbQ)과 같은 정말 좋은 몇가지 블로그와 사이트들을 발견했고 그 이후 저는 저의 배움을 요약하고 공유하기로 마음먹었습니다. 배울 것들이 많은 관계로 저는 이 포스트를 2개의 파트로 나누기로 결정했습니다. 이 파트에서는 흔히 사용되고 있는 용어들에 대해 소개할 것이고 다음 파트에서는 이 모든 용어들을 연결시켜 보겠습니다.
 
 자바스크립트는 하나의 스레드로 단 1개의 동시성만 다루는 언어입니다. 이것이 의미하는 것은 자바스크립트가 한 번에 1개의 작업만 다룰 수 있다는 얘기죠. 자바스크립트는 힙, 큐와 함께 구성하는 단일 콜스택을 갖습니다. 이것은 V8 내부에 구현되어 있습니다. 여기서 나온 전문용어들을 먼저 살펴봅시다.
 
![javascriptVRModel.png](https://images.velog.io/post-images/jakeseo_me/4575e6d0-456c-11e9-9537-05fa53649e18/javascriptVRModel.png)  
> Visual Representation of JS Model(credits[https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop])
  
### 1. 콜 스택  
 함수의 호출을 기록하는 자료구조입니다. 기본적으로 우리가 프로그램 안에서 위치한 곳이죠. 만약 우리가 어떤 함수를 실행시킨다면, 우리는 스택 위에 무언가를 올리는(push) 행위를 하는 겁니다. 그리고 우리가 함수로 부터 반환을 받을 때, 우리는 스택의 맨 위를 가져오는(pop) 것이죠.

![callstack.gif](https://images.velog.io/post-images/jakeseo_me/fc418e50-456c-11e9-83dd-8359947fc569/callstack.gif)  
> JS Stack Visualization (GIF)

우리가 위의 파일을 실행시키면서 우리가 처음으로 하는 일은 모든 실행이 시작되는 메인 함수를 찾는 일입니다. 위에서는 ```console.log(bar(6));```가 먼저 실행되어 콜스택에 올라가게 되네요. 그 이후에는 ```bar``` 함수가 매개변수들과 같이 스택의 top으로 올라가게 되고 ```foo``` 함수는 스택의 top으로 올라갔다가 곧장 값을 반환하고 빠지게(pop) 됩니다. 그 다음에는 ```bar``` 함수와 ```console.log(bar(6));``` 구문이 차례로 빠지게 됩니다. 그리고 마침내 ```console.log(bar(6));``` 구문은 값을 출력하게 됩니다. 이 모든 것들은 짧은 시간(jiffy time, ms[마이크로 세컨드]) 안에 처리됩니다.

브라우저 콘솔에서 가끔 긴 빨간색 에러 스택들을 본 기억이 있을 것입니다. 
