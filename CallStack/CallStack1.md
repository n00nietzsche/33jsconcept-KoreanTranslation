## 출처

- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec 글을 제 멋대로 번역한 것입니다. 오역나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec If the original author requests deletion, it will be deleted immediately.

## 자바스크립트 함수 실행에 대한 이해
### 콜 스택, 이벤트 루프, Task 그리고 그 외 등등...
 웹 개발자 또는 프론트 엔드 개발자라 불리는 사람들은 요즘 브라우저 내에서 상호작용하는 액션부터 컴퓨터 게임, 데스크탑 위젯, 크로스 플랫폼 모바일 어플리케이션, DB와 연결하는 서버 사이드 코딩 등 모든 것들을 스크립팅 언어로 구현해버립니다. 이렇게 많은 일을 하는 자바스크립트를 더욱 효율적으로 사용할 수 있도록 자바스크립트의 내부 동작을 아는 것이 이 포스팅의 목적입니다.
 
 자바스크립트 환경(ecosystem)은 그 어느 때보다 복잡해졌고 앞으로 더욱 복잡해질 것입니다. 현대적인 모바일 웹앱을 만들기 위해 요구되는 것은 너무나 많습니다. 웹팩, 바벨, ESLint, Mocha, Karma, Grunt ... etc... 우리는 무엇을 사용해야 하고 이 많은 툴들은 무슨 일을 하는 걸까요? 저는 현대 웹 개발자들이 겪고있는 어려움을 완벽히 그려낸 이 웹툰을 찾았습니다.
 
![modernwebdeveloperwebcomic.png](https://images.velog.io/post-images/jakeseo_me/631a6750-44cd-11e9-a7a4-4f4044b4f0ac/modernwebdeveloperwebcomic.png)

 각각의 인물의 대화는 주황색 옷 A, 초록색 옷 B로 표현하겠습니다
 (원시인들의 말이라 번역했을 때 약간 어색합니다.)
 
  A: OG는 고기를 먹고 싶다. 그냥 입에 넣으면 되지. 그치?
 
  B: 아니! 불을 사용해! 고기를 요리해!
  A: 불 뜨거워! 어떻게 손을 안 데이지?
  
  B: 뾰족한 집게를 사용하거나! 뜨거운 돌을 사용하거나! 박스에 불을 담아봐! 나도 잘 몰라.
  A: 선택지가 너무 많아! 어떻게 고르지?
  
  A: 그래! 고기 위에 불을 지르면 되겠다. 이제 먹어도 되지?
  B: 안돼! 기다려! 요리하고 소금을 좀 얹고 향신료를 좀 더해봐!
  
  A: 니 말은 고기를 먹기 위해서 내가 불과 막대와 기다림과 돌과 나뭇잎이 필요하다는 거야?
  	 난 그냥 생으로 먹을 거야 멍청아
     꺼ㅡ억
  
  개발자: 이게 바로 현대 자바스크립트가 너무 복잡한 이유야. 
