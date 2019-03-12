## 출처

- 이 포스팅은 https://github.com/leonardomso/33-js-concepts 에 있는 https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec 글을 제 멋대로 번역한 것입니다. 오역나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec If the original author requests deletion, it will be deleted immediately.

## 자바스크립트 함수 실행에 대한 이해
### 콜 스택, 이벤트 루프, Task 그리고 그 외 등등...
 웹 개발자 또는 프론트 엔드 개발자라 불리는 사람들은 요즘 브라우저 내에서 상호작용하는 액션부터 컴퓨터 게임, 데스크탑 위젯, 크로스 플랫폼 모바일 어플리케이션, DB와 연결하는 서버 사이드 코딩 등 모든 것들을 스크립팅 언어로 구현해버립니다. 
 
