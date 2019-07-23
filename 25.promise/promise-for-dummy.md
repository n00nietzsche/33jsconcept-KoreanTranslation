## 들어가기 전에

- 이 포스팅은 https://scotch.io/tutorials/javascript-promises-for-dummies#toc-new-kid-on-the-block-observables 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://scotch.io/tutorials/javascript-promises-for-dummies#toc-new-kid-on-the-block-observables If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #25 자바스크립트 : 바보를 위한 Promise

**Javascript Promise** 는 사실 어렵지 않습니다. 하지만, 처음에는 많은 사람들이 Promise를 이해하기 어렵다고 생각합니다. 그래서, 저자는 어떻게 Promise를 이해했는지 한번 그대로 적어보겠습니다. 바보가 이해했던 그 방법을요.

## Promise 이해하기

Promise는 요약하자면 다음과 같습니다.

"여러분이 **아이**라고 상상해보세요. 여러분의 어머니가 **새로운 스마트폰**을 다음 주에 사주기로 약속합니다."

여러분은 다음 주에 정말로 새로운 스마트폰을 가질 수 있을지 *알 수 없습니다.* 어머니는 진짜로 새로운 스마트폰을 *사줄 수도 있고,* 만일 기분이 좋지 않다면, 약속을 어기고, 스마트폰을 사주는 것을 잠시 *보류할 수도 있습니다.*

이게 바로 **Promise(약속)** 입니다. Promise는 3가지 상태를 가집니다.

1. Pending(미결) : 여러분은 새로운 스마트폰을 가질 수 있을지 *알 수 없습니다.*
2. Fulfilled(이행) : 어머니의 기분이 괜찮아서, 스마트폰을 사줬습니다.
3. Rejected(거절) : 어머니가 기분이 괜찮지만, 스마트폰은 사주지 않기로 했습니다.

