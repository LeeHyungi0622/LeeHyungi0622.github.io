---
title: 210611 Angular - Angular 프로젝트의 실행흐름 이해하기, Module, Component, Template, 단방향 바인딩과 양방향 바인딩(React와 Angular 비교), Component의 스타일링, Pipe 사용
date: 2021-06-11 10:59:00
tags:
  - Angular
categories:
  - Angular
---

`2021/06/12 단방향 바인딩과 양방향 바인딩 ~ Update`

<div align="center">
  <img src="/images/post_images/210611_angular.png" alt="Angular">
</div>

## <b>Angular 프로젝트의 전체적인 실행흐름 이해하기</b>

Angular는 정해진 틀 안에서 구조화되어있기 때문에 사용하기 위해서는 어떤 흐름으로 컴포넌트가 정의가 되고 실행되는지 이해가 필요하다.
ReactJS는 라이브러리로 개발자가 폴더를 만들어서 직접 구조화시켜줘야 되지만, Angular는 플랫폼으로 이미 구조화된 틀이 있다는 차이가 있다. 하지만 React와 Angular 모두 컴포넌트 기반의 개발을 한다는 점에서 같다.

- <ins><b>src/index.html</b></ins>
  React와 같이 Angular의 src 폴더에 정의된 index.html 파일을 보면, 별다른 script와 style을 로드하는 부분이 없고 webpack을 통해 번들링해서 index.html에 정의된 <app-root> 부분에 주입해서 보여준다.

- <ins><b>src/main.ts</b></ins>
  프로젝트를 실행하게 되면 가장 먼저 호출되는 script 파일이 main.ts 이다.
  이 파일에서 중요한 부분은 AppModule(root module)을 실행하는 부분인데, platformBrowserDynamic 메서드와 bootstrapModule 메서드가 호출되고 있음을 알 수 있다.

  paltformBrowserDynamic : Angular가 작성한 코드를 컴파일해서 실행할 수 있는 JS 코드로 만들어주는 메서드이다. (브라우저에서 동적으로 컴파일한다)
  `Angular는 서버나 브라우저 등 다양한 환경에서 실행될 수 있도록 rendering과 compile 프로세스가 분리되어있다.`

  –aot(ahead of time compilation) 옵션을 사용해서 실행을 하면, 메서드를 다르게 해서 실행할 수 있다.

- <ins><b>src/app/app.module.ts</b></ins>

  app.module.ts 파일은 앞서 살펴 본 main.ts에서 실행된 root module인 AppModule을 정의한 파일이다.
  class에 @NgModule 데코레이터로만 정의가 되어있는데, 이는 Angular module로 정의하는 데코레이터이다.

  데코레이터 내에 정의된 속성 중에 bootstrap을 살펴보면 root component인 AppComponent가 정의되어있음을 알 수 있다.

- <ins><b>src/app/app.component.ts</b></ins>
  app.module.ts에서 bootstrap에 정의된 컴포넌트는 index.html에 주입해주는 컴포넌트이다.
  컴포넌트 데코레이터를 보면 selector 속성에 `app-root`값으로 설정이 되어있다. 이는 index.html에 정의된 \<app-root>에 주입되는 컴포넌트라는 것을 의미한다.

  <!-- more -->

  AppComponent 클래스의 내부에 정의된 title은 `templateURL` 속성에 정의된 ./app.component.html 내에 binding된다.

## <b>Module</b>

Module이란 세부 구현이 숨겨지고 공개 API(method)를 이용해서 다른 코드에서 재사용 가능한 코드를 말한다.
ex) 리모콘이라는 모듈이 있다고 가정했을때 각 각의 버튼이 공개된 API이고, 볼륨이나 채널 변경을 할 수 있다.
ES6 module은 앞서 살펴 본 모듈의 정의 + 변수 scope이 모듈내로 제한되는 개념이다.
이전에 JS에서는 즉각호출 패턴으로 모듈화를 했지만, ES6에서는 자체적으로 모듈화를 지원하여 각각의 파일들이 하나의 모듈이 되고, 각 각의 모듈로써 정의된 파일내에서 사용된 변수들의 scope는 파일 내에서 보장된다.

Angular에서는 class를 export하는 방식으로 작성이 되며, module간의 종속관계를 읽고 하나의 파일로 번들링해주는 하는 식으로 Angular CLI에서 webpack을 사용해서 처리해준다. (번들링된 하나의 파일만 호스팅하는 식으로 실행)
main.ts 파일을 통해서 import되는 module들을 번들링해준다.

- ## Angular module ?

  컴포넌트, 파이프, 서비스 등과 같은 앵귤러 어플리케이션의 주요 부분을 `기능단위로 그룹핑`하게 해준다.

  - Angular 어플리케이션은 하나의 Root module을 가지며, 여러 Feature module을 가질 수 있다. 그리고 재사용할 수 있는 기능을 외부의 다른 파일에서 사용하기 위해서 사용되기도 한다.

  ```zsh
  # app/todo/todo.module.ts 생성
  $ ng generate module todo
  # app/todo/
  # g: generate, c: component
  # todo내에 todos라는 컴포넌트 생성
  # --module 옵션으로 특정 module을 지정
  # --export 옵션으로 외부 다른 모듈에서 사용 가능하도록 설정
  $ ng g c todo/todos --module todo/todo.module.ts --export
  ```

  todo.module.ts 파일의 @NgModule 데코레이터의 declaration 속성에 정의된 컴포넌트들은 template에서 사용할 수 있고, export에 정의된 컴포넌트는 외부 다른 파일에서 사용될 수 있는 컴포넌트들이다.
  providers 속성은 작성한 서비스 관련한 파일을 정의한다.

  ```javascript
  @NgModule({
    declarations: [TodosComponent],
    imports: [CommonModule],
    providers: [],
    exports: [TodosComponent]
  })
  export class TodoModule {}
  ```

  새롭게 정의한 모듈은 root module인 app.modules.ts 파일의 @NgMudule 데코레이터의 imports 속성에 정의해줘야 사용할 수 있다.

  이처럼 Angular에서는 하나의 root module이 필요하고, 이 root module에 template에서 사용할 module들을 정의해서 사용한다.
  module들간에 import해서 사용할 수 있다.

## <b>Component</b>

Angular에서는 여러 컴포넌트들로 구성이 된다.
최상위 \<app-root>를 기준으로 하위로 가지치기 하며 여러 하위 컴포넌트로 구성된다.
HTML 요소들의 그룹이며, 뷰와 로직으로 구성이 된다.

기본적으로 클래스에 로직을 작성하며, @Component 데코레이터를 여러 메타 데이터 객체로 정의한다. 각 메타 데이터 속성은  
**selector** : 선택자 (prefix로 #을 붙이면 id, 아무것도 안붙이면 element 이름)
**templateUrl** : View에 대한 정의를 하는 부분이다. 바인딩할 데이터나 사용할 컴포넌트들을 정의할 수 있다.
**styleUrls** : 배열의 형태로 여러 스타일과 관련된 파일을 import할 수 있다.

Component를 생성할때 옵션으로 `--inline-template`과 `--inline-style`을 넣어주게 되면, 컴포넌트 파일 내에서 style과 template 관련 정의를 해준다는 의미이다.
따라서 생성된 파일을 보면 별도로 html과 css파일이 생성되지 않는다.

html에 대해서는 template 속성에 template literal로 마크업을 해준다.

## <b>Template</b>

Angular template은 HTML 코드로서 표현을 하며, template 표현식과 문장을 가진다.
`바인딩되는 대상은 속성, 이벤트, ngModel, class, style`이 있다.

template에 마크업한 HTML의 이벤트 로직에 대한 작성은 class 내에 작성해준다. 작성한 로직의 데이터를 template에 반영하는 방법은 바인딩의 대상인 속성, 이벤트, ngModel, class, style 통해서 한다.

**바인딩**: 데이터와 로직을 담고 있는 컴포넌트와 DOM과의 interaction, communication

## <b><ins>단방향 바인딩과 양방향 바인딩</ins></b>

데이터 바인딩이란 비즈니스 로직인 Model과 View 간에 데이터를 동기화하는 프로세스이다.

`Angular에서는` Model과 View가 동기화되므로 데이터를 변경되면 뷰가 업데이트되고, 뷰를 업데이트하면 데이터가 업데이트 되는 `양방향 데이터 바인딩 구조를 가지고 있다.`

`반면, React에서는 단방향 또는 하향식 데이터 바인딩을 사용`한다.
단방향 데이터 바인딩 구조는 코드를 보다 안정적이게 만들지만, Model과 View를 동기하기 위해서는 추가적인 작업이 필요하다.

<div align="center">
  <img src="/images/post_images/210612_one-way-and-two-way-data-binding.png" alt="단방향 바인딩과 양방향 바인딩">
</div>

- ### **단방향 바인딩**

  모든 컴포넌트와 DOM 속성에 데이터를 바인딩 할 수 있다.
  `Component(class) instance라는 Context내에서 템플릿 문장에 정의한 속성과 이벤트(메서드)를 찾는다.`

  - [속성]="템플릿 문장"
  - (이벤트)="템플릿 문장"
    ```javascript
    <div *ngFor="let todo of todos" (click)="toggleTodo(todo)">
        <input type="checkbox" [checked]="todo.done"> {{ todo.text }}
    </div>
    ```
    `*ngFor를 사용해서 객체에 대한 반복처리를 할 수 있다.`

- ### **양방향 바인딩**

  Angular에서는 Engine model이라는 지시자를 이용하면 양방향 데이터 바인딩을 사용할 수 있다.

  - [(ngModel)]="템플릿 문장"
    속성바인딩 할 때 사용한 [] 대괄호와 이벤트 바인딩을 할 때 사용한 () 중괄호를 사용하면 양방향 바인딩이 가능하다.
    `(ngModel을 사용하기 위해서는 module의 @NgModule의 import 속성에 FormsModule을 넣어줘야 한다)`
    ```javascript
    <div>
        <input type="text" placeholder="할 일 추가" [(ngModel)]="newText">
        <button (click)="addTodo(newText)">Add</button>
    </div>
    ```

  ```javascript
  // todos.component.ts
  import { Component, OnInit } from '@angular/core';

  // 메타데이터는 객체로써 정의한다.
  @Component({
    selector: 'app-todos',
    templateUrl: './todos.component.html',
    styleUrls: ['./todos.component.scss']
  })
  export class TodosComponent implements OnInit {
    newText = '';

    todos: {
      done: boolean,
      text: string
    }[];

    constructor() {
      this.todos = [
        { done: false, text: '운동하기' },
        { done: true, text: '공부하기' }
      ];
    }

    ngOnInit(): void {}

    toggleTodo(todo: { done: boolean, text: string }) {
      todo.done = !todo.done;
    }

    addTodo(newText: string) {
      this.todos.push({
        done: false,
        text: newText
      });
      this.newText = '';
    }
  }
  ```

  <br/>

## <b>Component 간의 커뮤니케이션</b>

- ### 자식 컴포넌트에서 부모 컴포넌트로

  - @Output() 데코레이터와 EventEmitter를 사용해서 부모에게 이벤트를 전달 할 수 있다.
  - 부모 컴포넌트는 $event를 통해 이벤트의 데이터를 전달받는다.
  - 자식이 부모 컴포넌트를 직접 주입받을 수 있지만, 이는 강력한 의존관계가 성립되기 때문에 필요한 경우에만 사용한다.

  ```javascript
  // 부모 컴포넌트
  //(template)
  //이벤트를 전달하는 자식 컴포넌트의 속성에서 정의
  (onTodoAdded) = "addTodo($event)"
  //(component)
  addTodo(text: string){
    this.todos.push({
      done: false,
      text,
    });
  }


  // 자식 컴포넌트
  // event emitter 정의
  @Output() onTodoAdded = new EventEmitter();

  addTodo(newText: string){
    this.onTodoAdded.emit(newText);
    this.newText='';
  }
  ```

- ### 부모 컴포넌트에서 자식 컴포넌트로

  부모 컴포넌트에서 자식 컴포넌트로 데이터를 전달하기 위해서는 자식 컴포넌트의 공개된 속성에 `@Input 데코레이터를 사용`하면 된다. 그러면 부모 컴포넌트 템플릿 상에서 자식 컴포넌트에 속성을 전달해서 사용 할 수 있다.
  그리고 ES6의 setter를 사용해서 데이터를 가공한 뒤에 비공개 속성을 업데이트 할 수 있다.(getter)
  자식 컴포넌트 클래스의 instance reference를 부모 컴포넌트로부터 @ViewChild 데코레이터를 사용해서 넘겨받을 수 있다.

## <b>Model 정의</b>

복수의 컴포넌트에서 공통적으로 사용되는 데이터 객체가 있다면 Model로써 정의를 해서 재사용 할 수 있다.

## <b>Component의 스타일링</b>

Angular에서는 View encapsulation(뷰의 캡슐화)으로 컴포넌트 안에서 정의한 CSS 스타일이 컴포넌트 밖에 영향을 주지 않는다.
(Angular 프로젝트를 컴파일하면 임의의 랜덤한 속성을 DOM요소에 넣어주기 때문에 특정 요소만 선택해서 스타일링하기 때문에 가능한 것이다)

컴포넌트를 스타일링하는 방법에는 아래 세 가지 방법이 있다.

- **별도의 CSS 파일에서 스타일 적용하기**

- **inline-style방식으로 스타일 적용하기**

- **src/styles.css 에서 스타일링하면 전역적인 스타일링이 가능하다.**

## <b>Pipe</b>

Pipe는 템플릿에서 보이는 데이터를 변환하는데 사용된다.
이전의 AngularJS v1.x에서는 필터로써 제공이 되었던 기능이다.

- ### <b>사용법</b>

  - `{{ express | pipeName:paramValue }}`
  - **예시 1)** `{{ today | date }}`
  - **예시 2)** `{{ today | date:"yy/dd/dd" }}` : pip를 사용해서 formatting한 데이터로 변환할 수 있다.
  - **예시 3)** `{{ today | date | uppercase }}` : pip를 사용해서 여러번 체이닝해서 데이터를 변환할 수 있다.

  ```javascript
  <h2>{{ today | date:"M월 d일" }}</h2>
  // todos object를 json형태로 변환이 가능하다.
  // Angular documentation 확인하기
  {{ todos | json }}
  ```
