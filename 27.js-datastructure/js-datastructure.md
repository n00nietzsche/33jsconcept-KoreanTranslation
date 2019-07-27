## 들어가기 전에

- 이 포스팅은 https://medium.com/siliconwat/data-structures-in-javascript-1b9aed0ea17c 에 있는 포스팅을 번역한 것입니다. 오역이나 의역이 있을 수 있습니다. 지적해주시면 확인 후 바로 정정하겠습니다.

- original source of this posting is from https://medium.com/siliconwat/data-structures-in-javascript-1b9aed0ea17c If the original author requests deletion, it will be deleted immediately.

- Translated by Jake Seo (서진규)

	- https://velog.io/@jakeseo_me
	- https://github.com/n00nietzsche

> 자바스크립트 개발자라면 알아야 할 33가지 개념 #27 자바스크립트 : 자바스크립트 자료구조 

## 소개

비즈니스 로직이 백단에서부터 프론트단으로 더욱 더 많이 넘어오고 있는 추세입니다. 프론트 개발자에게 전공지식이 점점 더 중요해지고 있습니다. 프론트엔드 엔지니어로서, 더욱 생산적이 되기 위해서 우리는 **리액트** 와 같은 뷰단 라이브러리들에 의존하게 됩니다. 또 뷰 라이브러리는 데이터를 관리하기 위해 상태 라이브러리인 **리덕스**와 같은 라이브러리에 의존하게 됩니다. 이런 사실들과 함께, 리액트와 리덕스는 *반응형 프로그래밍* 패러다임을 구독하게 됩니다. 리액트의 데이터가 변경되면 바로 UI를 업데이트합니다. 점점 더, 백엔드는 API서버와 함께 간단한 일들만 하게 됩니다. 백엔드는 그저 *데이터베이스* 를 프론트엔드로 그저 "앞단으로" 보냅니다. 프론트엔지니어가 모든 컨트롤러 로직을 처리할 것이라고 기대하면서 말이죠. *마이크로서비스* 의 점차 높아지는 인기와 **GraphQL**은 이러한 트렌드가 사실임을 증명합니다.

이제는, HTML과 CSS의 예술적인 이해를 하는 것과 더불어 프론트 엔지니어가 자바스크립트도 마스터했길 기대합니다. 클라이언트 사이드의 데이터 저장소는 이제 서버 데이터의 "복제본(Replica)"이 되어가고 있습니다. 관용적인 자료구조 지식에 대해 친숙한 것은 이제 꽤나 중요합니다. 실제로, 엔지니어의 경험의 수준은 그들이 *언제* *왜* 어떤 특정한 자료구조를 써야 하는지 구분하는 능력으로 추론할 수 있습니다.

> 나쁜 프로그래머는 코드에 대해 고민하고, 좋은 프로그래머는 자료구조와 그 관계에 대해 고민한다.
> Linus Torvals, Creator of Linux and Git

높은 수준에서 자주 쓰이는 3가지 자료구조가 있습니다. **스택** 그리고 **큐** 는 배열과 같은 구조입니다. 다만, 아이템이 어떻게 삽입되고 삭제되는지가 다릅니다. **링크드 리스트**, **트리**, 그리고 **그래프** 는 다른 노드를 참조하는 *노드*와 관련된 자료구조입니다. **해시테이블** 은 해시 함수에 의존하여, 데이터를 배치하고 저장합니다.

복잡도의 측면에서 봤을 때, `스택`과 `큐`는 가장 간단합니다 그리고 `링크드리스트`에서 구현도 가능합니다. `트리`와 `그래프`는 가장 복잡합니다. 왜냐하면 두 자료구조는 `링크드 리스트`에서 확장된 개념이기 때문입니다. `해시테이블`은 이 자료구조들을 활용하여 서로 상호보완적으로 잘 작동하게 하기 위해서 필요합니다. 효율성의 측면에서 봤을 때, **링크드 리스트** 는 데이터를 기록하고 저장하기에 가장 최적화되어 있습니다. **해시테이블은** 반면에 데이터를 찾고 가져오는데에 가장 최적화되어 있습니다.

*왜* 그리고 *언제* 구현할지에 대해 설명하기 위해, 이 아티클은 이러한 의존성의 순서를 따를 것입니다. 이제 시작해봅시다.

## 스택

거의 틀림없이, 자바스크립트에서 가장 중요한 `스택`은 우리가 `함수`를 실행할 때 함수를 함수 **스코프**에 넣습니다(push). 프로그래밍적인 측면에서 봤을 때, 이는 그저 `배열`인데, `push`와 `pop`이라는 연산을 가진 배열일 뿐입니다. **push** 연산은 배열의 가장 상위에 엘리먼트를 추가합니다. 그리고 **pop** 연산은 같은 위치에서 엘리먼트들을 빼옵니다. 다른 말로, **스택**은 "Last In, First Out(가장 나중에 들어온 것이, 가장 먼저 나간다)"의 프로토콜(LIFO)을 따릅니다.

아래의 예제는 `Stack`을 코드에서 구현한 것입니다. 우리가 stack의 순서를 뒤집을 수 있는 것을 기억하세요. 가장 하위가 가장 상위가 되고, 가장 상위가 가장 하위가 됩니다. 이를테면, 우리는 배열의 `push`와 `pop`의 자리에 `shift`와 `unshift`메소드를 사용할 수 있습니다. 반대로도 가능합니다.

```js
class Stack {
	constructor(...items) {
		this.reverse = false;
		this.stack = [...items];
	}

	push(...items) {
		return this.reverse
			? this.stack.unshift(...items)
			: this.stack.push(...items);
	}

	pop() {
		return this.reverse ? this.stack.shift() : this.stack.pop();
	}
}

mocha.setup("bdd");
const { assert } = chai;

describe("Stacks", () => {
	it("Should push items to top of stack", () => {
		const stack = new Stack(4, 5);
		assert.equal(stack.push(1, 2, 3), 5);
		assert.deepEqual(stack.stack, [4, 5, 1, 2, 3]);
	});

	it("Should push items to bottom of stack", () => {
		const stack = new Stack(4, 5);
		stack.reverse = true;
		assert.equal(stack.push(1, 2, 3), 5);
		assert.deepEqual(stack.stack, [1, 2, 3, 4, 5]);
	});

	it("Should pop item from top of stack", () => {
		const stack = new Stack(1, 2, 3);
		assert.equal(stack.pop(), 3);
	});

	it("Should pop item from bottom of stack", () => {
		const stack = new Stack(1, 2, 3);
		stack.reverse = true;
		assert.equal(stack.pop(), 1);
	});
});

mocha.run();
```

[코드펜에서 직접 해보기](https://codepen.io/thonly/pen/GMyLOV)


아이템의 숫자가 증가할수록, `unshift`와 `shift`보다 `push`와 `pop`이 놀랍도록 효율적으로 동작합니다. 왜냐하면 모든 아이템은 `shift`와 `unshift`이후에 재인덱싱이 필요하지만, `push`와 `pop`은 재인덱싱이 필요 없습니다.

## 큐

자바스크립트는 **이벤트 중심**의 프로그래밍 언어입니다. 이러한 특징은 *논 블락킹* 연산을 지원하는 것을 가능하게 합니다. 내부적으로 브라우저는 전체 자바스크립트 코드를 관리하기 위해서 *오직 하나의 스레드* 만 관리합니다. **이벤트 큐**를 사용하며 `리스너`와 등록된 `이벤트`를 큐에 넣습니다. 싱글-스레드 환경에서 비동기성을 지원하기 위해서, (CPU 자원을 아끼고, 웹 UX를 증진시키기 위해서), `리스너 함수`는 *큐에서 빠지고(dequeue)* **콜스택**이 비었을 때만, 실행됩니다. `Promise`는 다른 연산을 방해하지 않는 비동기적인 코드를 "동기화된-스타일"로 실행하는 것을 가능하게 하기 위해 이러한 *이벤트 중심* **아키텍쳐**에 의존합니다. 

프로그래밍적으로, `Queue`는 그냥 두가지 주요한 연산기능이 있는 배열입니다. `unshift`와 `pop` 연산이 있습니다. **Unshift** 연산은 배열의 끝까지 아이템을 넣습니다. **Pop** 은 배열의 시작점부터 아이템을 가져옵니다. 다른 말로 하자면, **큐**는 "먼저 들어간 것이 먼저 나온다. (First In, First Out, FIFO)" 프로토콜을 따릅니다. 
