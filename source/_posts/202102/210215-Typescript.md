---
title: 210215 TypeScript Book TIL
date: 2021-02-15 10:30:42
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - TypeScript
---

![](/images/post_images/typescript_logo.png)

> **이 포스팅은 O'Reilly TypeScript책을 통해 공부한 내용과 실습한 내용을 기반으로 작성하였습니다.**

## **오늘 공부한 내용**

- ### **컴파일과 실행에 대한 이해**

  이번에 읽은 내용에서 가장 유익하다고 느꼈던 내용이다. 실제 코드를 작성하고 실행을 했을때 내부에서 구체적으로 어떤 과정에 의해서 처리가 되는지 잘 몰랐었는데 이번 기회에 제대로 알 수 있었던 것 같다.
  특히 타입스크립트는 자바스크리트나 자바와 같은 주요 언어와는 다른 방식으로 동작하기 때문에 이 부분에 대해서 정리를 해보려고 한다.

  - 일반적으로 프로그래머가 작성한 텍스트(코드)는 컴파일러(compiler)라는 프로그램이 파싱(Parsing)하여, AST(Abstract Syntax Tree, AST)라는 자료구조로 변환된다. 이 AST는 공백, 주석, 탭, 공백 등의 결과를 무시한다.<br/>
    컴파일러는 이 AST 자료구조를 바이트 코드(bytecode)라는 하위 수준의 표현으로 변환을 한다.
    이렇게 만들어진 바이트 코드는 런타임(runtime)이라는 다른 프로그램에 입력해서 평가하고 결과를 얻을 수 있는 것이다.<br/>
    1. `텍스트 코드 => 컴파일러(Compiler) => AST(Abstract Syntax Tree)`<br/>
    2. `AST(Abstract Syntax Tree) => 컴파일러(Compiler) => 바이트 코드(bytecode)`<br/>
    3. `런타임(Runtime) => 바이트 코드(bytecode)`

  즉 일반적으로 <b><ins>프로그램을 실행한다는 것은 컴파일러가 소스코드를 파싱해서 AST로 만들고 이 AST를 바이트코드로 변환한 것을 런타임이 평가하도록 지시하는 일련의 과정을 의미</ins></b>한다.

  <!-- more -->

<br/>

일반적인 프로그램의 실행은 위의 과정을 거친다. 타입스크립트가 다른 언어의 일반적인 실행과 다른 점은 AST가 바이트 코드로 변환되는 것이 아닌, 자바스크립트 코드로 변환된다.

> (TS) 1. 타입스크립트 소스 → 타입스크립트 AST (compiler) `(소스코드의 타입사용 O)` <br/>
> (TS) 2. 타입 검사기(typechecker)가 AST를 검사 (compiler) `(소스코드의 타입사용 O)`<br/>
> (TS) 3. 타입 스크립트 AST → 자바스크립트 소스 `(소스코드의 타입사용 X)`<br/>
> =============================================<br/>
> (JS) 4. 자바스크립트 소스 → 자바스크립트 AST <br/>
> (JS) 5. AST → 바이트코드 <br/>
> (JS) 6. 런타임이 바이트코드를 평가 <br/>

보통 자바스크립트 컴파일러와 런타임은 엔진이라는 하나의 프로그램으로 합쳐진다.
프로그래머는 주로 이 엔진과 상호작용을 한다.
<br/>

- ### **타입시스템에 대한 이해**

  타입 시스템에는 명시적 타입 시스템과 추론적 타입 시스템이 있다.<br/>
  타입 스크립트는 두 시스템의 영향을 받은 언어이다. 그말은 즉슨 개발자는 타입을 명시하거나 별도의 명시없이 타입스크립트가 추론하도록 할 수 있다.

  명시적 타입지정은 `value:type`의 형태로 작성한다. 추론적 타입지정은 별도로 type을 지정하지 않아도 값에 의해서 TypeScript가 추론을 하는 것을 의미한다.

- ### 자바스크립트와 타입스크립트의 차이

  자바스크립트와 타입스크립트의 차이점은 자바스크립트는 동적 타입 결정방식인 반면에 타입스크립트는 정적 타입 결정방식을 가진다. 그리고 자바스크립트의 타입체크와 에러검출은 런타임 단계에서 이루어지는 반면에 타입스크립트는 컴파일 단계에서 이루어진다.

  이러한 차이로 인해, 타입스크립트는 코드를 실행하지 않고도 코드에 에러가 있음을 알 수 있다.

## **오늘 실습한 내용**

> 실습 Repository : [https://github.com/LeeHyungi0622/typescript-oreilly-practice](https://github.com/LeeHyungi0622/typescript-oreilly-practice)

- **TypeScript**

  - TypeScript 프로젝트 생성
  - NPM 프로젝트 초기화
    ```bash
    $ npm init
    ```
  - typescript tslint @types/node 설치
    ```bash
    $ npm i -D typescript tsline @types/node
    ```
  - tsconfig.json 파일생성
    직접 파일을 생성하여 실습하였지만, `./node_modules/.bin/tsc --init` 내장명령을 통해서 자동으로 설정을 해줄 수 있다.

    ```javascript
    {
      "compilerOptions": {
          "lib": ["es2015"],
          "module": "commonjs",
          "outDir": "dist",
          "sourceMap": true,
          "strict": true,
          "target": "es2015"
      },
      "include": [
          "src"
      ]
    }
    ```

  - tslint.json
    탭을 사용할지 공백을 사용할지 등에 대한 코딩스타일을 정하는 파일로, `./node_modules/.bin/tslint --init` 내장명령을 통해서 파일을 생성할 수 있다.

    ```javascript
    {
      "defaultSeverity": "error",
      "extends": [
          "tslint:recommended"
      ],
      "rules": {
          "semicolon": false,
          "trailing-comma": false
      }
    }
    ```

    - src 폴더에 index.ts파일을 생성하여 간단한 코드를 작성한다.

    - TSC로 타입스크립트 파일을 컴파일한다.<br/>
      ts-node를 설치하면 명령 한 번으로 타입스크립트를 컴파일하고 실행할 수 있다.
      ```javascript
      $ ./node_modules/.bin/tsc
      ```
    - NodeJS로 컴파일된 자바스크립트 코드를 실행한다.
      ```javascript
      $ node ./dist/index.js
      ```

    <ins>`typescript-node-starter와 같은 뼈대(Scafolding) 도구를 이용해서 프로젝트 디렉터리 구조를 빠르게 생성할 수 있다.`</ins>
