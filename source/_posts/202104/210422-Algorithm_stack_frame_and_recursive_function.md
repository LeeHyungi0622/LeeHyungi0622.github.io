---
title: 210422 스택 프레임(Stack frame)과 재귀함수(recursive function)
date: 2021-04-22 13:17:42
tags:
  - Algorithm
  - Stack-frame
  - Recursive-function
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210422_stackframe_and_recursive_function.png" alt="스택 프레임(Stack frame)">
</div>

## <ins><b>재귀함수의 동작을 스택 프레임과 연관지어 이해</b></ins>

아래에 작성한 간단한 재귀함수의 동작방식을 스택 프레임과 연관지어 설명해보려고 한다.
이미 재귀함수는 문제풀이나 기본적인 개념이해는 되었지만, 스택과 연관지어 기본적인 동작방식을 설명해본적은 없기 때문에 이번 포스팅을 계기로 한 번 정리를 해보려고 한다.

  <!-- more -->

`sample code 1`

```javascript
function sampleRecursiveFunc(n) {
  function DFS(L) {
    if (L === 0) return;
    else {
      console.log(L);
      DFS(L - 1);
    }
  }
  DFS(n);
}

sampleRecursiveFunc(3);
// output:
/*
3
2
1
*/
```

`sample code 2`

```javascript
function sampleRecursiveFunc(n) {
  function DFS(L) {
    if (L === 0) return;
    else {
      DFS(L - 1);
      console.log(L);
    }
  }
  DFS(n);
}

sampleRecursiveFunc(3);
// output:
/*
1
2
3
*/
```

자 위에 작성한 `sample code 1`과 `sample code 2`의 코드를 살펴보자.
매우 간단한 코드이기 때문에 쉽게 결과를 예측할 수 있을 것이다.
그렇다면 위의 코드가 실행되었을때 무슨일이 일어날까?
바로 함수가 실행되면 실행된 해당 함수에 대한 `스택 프레임(Stack frame)이 생성`이 되고 `LIFO Stack에 적재`가 된다. 이 스택 프레임은 매개변수(Parameters)와 지역변수(Local variables) 그리고 복기주소(Return address)로 구성이 되어있는데, 이 복기 주소(Return address)가 스택의 상위 스택 프레임이 실행되고 나서 돌아갈 주소를 담고 있다.

자 그럼 기본적으로 함수가 실행되면 스택 프레임이 스택에 적재되는 과정을 이해했으니, `sample code2`의 코드를 이 기본적인 개념에 비춰 설명해보도록 하겠다.

**(1)** 우선적으로 sampleRecursiveFunc 함수가 number 타입의 인자 3이 호출이 되고, 이 함수 실행에 대한 스택 프레임이 스택에 적재가 된다. 그리고 sampleRecursiveFunc 함수의 내부에 선언된 매개변수 3을 가진 DFS함수를 호출하게 된다. (DFS(3) 스택 프레임 적재)

**(2)** number 타입의 인자 3을 매개변수로 갖는 함수는 실행이 되어 함수 몸체에 정의한 코드를 순차적으로 실행하게 된다. 초기 인자값은 3이기 때문에 조건 분기문에서 else 문이 실행되어 else 문 내부의 DFS(3-1) 함수가 호출이 된다. (재귀호출)

**(3)** 이때 DFS(n-1) 이후의 코드는 실행이 되지 않으며, 스택 프레임 DFS(3)은 `waiting` 상태가 된다. 그리고 DFS(n-1)인 DFS(2)의 스택 프레임이 스택에 적재되게 된다.

**(4)** DFS(2) 함수실행의 경우도 DFS(3)과 동일한 조건분기 처리를 거쳐 `waiting` 상태가 되고, DFS(1)에 대한 함수호출과 스택 프레임이 스택에 적재되게 된다.

**(5)** 최종적으로 DFS(0)에 대한 스택 프레임이 스택에 적재되고 함수가 실행되게 되면, 조건 분기문의 if문에서 조건이 만족되어 return; (함수의 가장 마지막 하단으로 이동 및 실행 종료)가 된다. 이후에는 DFS(0)의 스택 프레임은 스택으로부터 pop되어 스택 프레임의 복기 주소(Return address)에 저장되어있는 곳으로 이동한다.

**(6)** DFS(0)의 복기 주소에는 이전 DFS(1) 스택 프레임(waiting 상태)의 주소를 가르키고 있기 때문에 DFS(1)이 `이전에 실행을 하다가 멈춘 시점 (DFS(n-1))의 이후 코드를 순차적으로 실행`하게 된다.

**(7)** DFS(1)의 함수 실행이 완료된 이후에도 순차적으로 각 실행함수의 스택 프레임의 복기 주소를 참조하여 다음, 그 다음에 실행될 함수들을 실행하게 된다.

이 Stack frame에 대한 내용은 `완전탐색, DFS, Back tracking`등의 알고리즘 개념을 이해하기 위한 초석으로 매우 중요한 개념이다.
