---
title: JavaScript로 코딩테스트 준비하기 - 입출력에 대한 이야기
date: 2021-03-24 13:00:42
tags:
  - Algorithm
  - JavaScript
  - Sort
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 자바스크립트에서 입력받기

Python으로는 간단하게 input()을 사용해서 키보드의 입력을 받아서 처리할 수 있었다. 하지만 이번에 VSCode에서 JavaScript로 알고리즘 문제를 풀면서 키보드로 받은 입력 값을 처리하려고 했는데 입력 이벤트를 계속 받고는 있지만 입력 이벤트가 끝나지 않았다.
왜 이런지 이해가 되지 않아서 방법을 찾아보던 도중에 해결방법을 찾았다.
<ins>**바로 입력이 끝났다면 `ctrl + D`를 눌러서 입력 종료를 알려주는 것이다.**</ins>

예상하지 못한 JavaScript 입/출력 부분의 문제로 계획하지 않은 입출력 관련된 내용으로 포스팅을 하게 되었다.

JavaScript에서 입출력은 fs(file system) 모듈을 사용한다. fs모듈의 readFileSync() 함수를 사용해서 파일이나 표준 입출력을 입력받게 되는데, 아래 예시 코드에서 0을 입력해주는 이유는 `표준입력(stdin: standard input)`이 파일 설명자로 0이기 때문이다.

<ins><b>nodejs에서 File system에 관한 공식문서 내용은 아래 링크를 참조하도록 하자.</b></ins>
→ [https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_file_system](https://nodejs.org/dist/latest-v14.x/docs/api/fs.html#fs_file_system)

따라서 별도의 파일을 읽지 않고 표준 입력을 받는 경우에는 내부에 0이라는 인수를 넘겨준다. 0과 함께 encoding을 명시해줘야 하는데 <b>별도로 명시하지 않고 표준입력의 설명자 0만을 넘겨준 경우에는 toString()함수를 사용해서 별도로 String 타입으로 변환을 해줘야 한다.</b>
<ins>`(변환을 안해주게 되면 `<Buffer 31 30 0a>`와 같은 raw buffer가 결과값으로 나온다)`</ins>

```javascript
const fs = require('fs');

const inputWithNoEncoding = fs.readFileSync(0).toString().split('\n');

const inputWithEncoding = fs.readFileSync(0, 'utf8').split('\n');

const cvtInputToNumber = fs.readFileSync(0, 'utf8').split('\n');
// ['10', '']와 같은 배열의 형태로 값이 반환되기 때문에 [0]번째 인덱스 값을 가져온다.
console.log(Number(cvtInputToNumber[0]));
```

<ins>`참고로 readFileSync()의 내부에 작성해준 /dev/stdin은 백준 알고리즘 문제 풀이에서 입력 예제를 넣고 그 파일을 읽어 실행하게 만들기 위해서 작성해준 것이다.`</ins>

JavaScript에서 입력받는 방법은 앞서 설명한 fs(File System) 모듈을 사용한 방법과 readline 모듈을 사용한 방법이 있다.
readline 모듈은 process.stdin이나 file stream과 같은 `Readable stream`에서 line by line으로 데이터를 읽어들이기 위한 interface를 제공한다.

```javascript
const readline = require('readline');
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.on('line', (input) => {
  console.log(`received: ${input}`);
});
```

<!-- more -->

위에서 작성한 'line'이벤트는 input stream에서 줄 바꿈관련 입력을 받을때마다 발생한다.(사용자가 Enter키나 Return을 입력한 경우에 발생)
콜백함수는 사용자로부터 입력받은 한 줄의 값을 인자로 받아서 처리한다.

<b>그렇다면 모든 입력이 완료가 되었을때 어떤 방식으로 처리를 해야할까?</b>
바로 `close`관련 이벤트를 발생시키면 된다.

<ins><b>input stream 도중에 close 이벤트를 발생시키기 위해서는</b></ins>

- 앞에서 찾아보았던 `Ctrl + D` (EOT: End Of Transmission)
- input stream이 `end event`를 받은 경우
- `rl.close()`함수가 호출되어 readline.Interface instance가 입출력 스트림의 권한을 포기한 상태인 경우

리스너 함수는 처음에 아무런 인자를 넘겨받지 않은 상태로 호출이 되고, `readline.Interface object`는 `close event`가 발생된 후에 완료된다.

**readline module을 사용하여 복수의 입력을 받아 저장하는 경우**에는 각 line이 입력될때마다 line event를 발생시켜서 해당 입력값을 별도의 변수에 저장하는 형태로 작업을 해줘야 한다.
입력값이 전부 저장이 된 후에는 `close 이벤트`을 발생시켜서 저장된 변수를 사용해서 계산을 진행하게 된다.

```javascript
const readline = require('readline');
const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.on('line', (line) => {
  // 입력받은 값을 별도의 변수에 저장
}).on('close', () => {
  // 입력받은 값을 담고 있는 변수를 사용하여 계산한 뒤에 결과값 출력
});
```

여지까지 JavaScript를 사용해서 입력을 받기 위한 fs와 readline, 두 가지 모듈에 대해서 알아보았다. 상대적으로 fs 모듈을 사용해서 코드를 작성하는 것이 간결한 느낌이 있기 때문에 나는 JavaScript를 사용해서 입력을 받을때에는 fs모듈을 사용하기로 결정했다.

`fs 모듈을 사용한 입력 및 활용 연습(연습예제 : 백준 알고리즘/10828)`

```javascript
const fs = require('fs');
const getInput = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `14
push 1
push 2
top
size
empty
pop
pop
pop
size
empty
pop
push 3
empty
top
`
).split('\n');

const input = (() => {
  let line = 0;
  return () => getInput[line++];
})();

// Stack에 대한 정보를 담기 위한 배열
const stack = [];
// 결과값을 담을 배열
const result = [];
// 첫번째 입력값 테스트 케이스 갯수로 정수형으로 변환
const numOfTestCase = parseInt(input());
const stackAction = {
  push: (value) => stack.push(value),
  pop: () => (stack.length ? result.push(stack.pop()) : result.push(-1)),
  size: () => result.push(stack.length),
  empty: () => result.push(stack.length ? 0 : 1),
  top: () => result.push(stack.length ? stack[stack.length - 1] : -1)
};
for (let i = 0; i < numOfTestCase; i++) {
  const [method, value] = input().split(' ');
  stackAction[method](value);
}
console.log(result.join('\n'));
```

백준 알고리즘에서는 테스트 케이스 수에 대한 입력을 직접 사용자로부터 받고 코드 실행 도중에도 interactive하게 사용자로부터 테스트 케이스 수 만큼 입력을 받고 처리를 하기 때문에 파이썬으로 입력을 받는 것에 비해 자바스크립트로 입력을 받는 것이 어렵게 느껴졌다.
하지만 실제 코딩테스트에서는 모든 입력을 받은 뒤에 계산을 하고 결과값을 출력하기 때문에 코드가 실행되는 도중에 사용자로부터 interactive하게 값을 입력받는 일은 없다고 한다. 따라서 event driven 방식의 복잡한 readline module을 사용할 필요는 없다.
