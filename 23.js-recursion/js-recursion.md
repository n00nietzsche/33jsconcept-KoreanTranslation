## 들어가기 전에

- 이 포스팅은 https://codeburst.io/learn-and-understand-recursion-in-javascript-b588218e87ea 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://codeburst.io/learn-and-understand-recursion-in-javascript-b588218e87ea If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #23 자바스크립트 : 자바스크립트 재귀(Recursion) 이해하기

## 재귀(Recursion)이 뭘까요?

재귀를 간단하게 정의하면 한 함수가 자기 자신을 호출하는 순간입니다.

재귀가 무엇인지 더 자세히 알아봅시다. 일단은 가장 유명한 재귀 예제를 한번 봅시다. 이 예제는 제공된 정수의 팩토리얼을 반환합니다.

```js
function factorial(x) {
  if (x<0) return;
  if (x===0) return 1;
  return x * factorial(x-1);
}

factorial(3);
// 6
```

**위의 예제가 잘 이해가 되지 않더라도 괜찮습니다.** 중요한 부분은 4번째 라인에서 일어납니다. `return x * factorial(x - 1);`. 여러분도 보고 있듯, 함수가 자기 자신을 다시 호출하고 있습니다. (`factorial(x-1)`), 하지만, 처음에 호출했던 값보다 1 작아진 파라미터와 함께 다시 호출됩니다. 4번째 라인과 같은 구문이 이 함수를 재귀함수로 만들어줍니다.

