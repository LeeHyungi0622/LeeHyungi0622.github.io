---
title: 210617 Angular (작성중...)
date: 2021-06-17 12:34:00
tags:
  - Angular
categories:
  - Angular
---

<div align="center">
  <img src="/images/post_images/210611_angular.png" alt="Angular">
</div>

이미 MERN(MongoDB,Express, React (+ Redux), NodeJS)의 구성으로 프로젝트를 진행해본 경험이 있기 때문에 Angular의 사용에 익숙해지면 좀 수월해질 것 같다.

그럼 Angular에 중점을 두고 MEAN의 구성으로 프로젝트를 만들어가면서 Angular의 사용에 익숙해지도록 해보자.

## <b>MEAN</b>

M - MongoDB
E - ExpressJS
A - Angular
N - NodeJS

Angular에서 반복적으로 자주 사용되는 부분에 대해 복습을 하자.

# <b>컴포넌트의 selector는 snake case로 작성</b>

template에서 커스텀 컴포넌트를 사용하는 경우, 각 컴포넌트에 정의되어있는 selector를 사용한다.
이 selector는 `snake case`로 작성하도록 한다.

# <b>@NgModule - declarations</b>

상위 컴포넌트의 template 내에서 외부에서 작성한 커스텀 컴포넌트를 사용하기 위해서는 `상위 컴포넌트가 정의되어있는 module decorator의 declarations에 정의`해줘야 한다.
component가 아닌 `moudle을 포함시키는 경우에는 imports에 module을 추가`해준다.
exports는 외부에서 특정 컴포넌트를 사용하도록 해줄때 컴포넌트를 정의하는 부분이다. (`확인필요`)

# <b>@input() 데코레이터</b>

상위 컴포넌트 템플릿에서 하위 커스텀 컴포넌트의 속성으로 상위 컴포넌트의 속성값을 넣어주는 경우,(`React에서 부모 컴포넌트가 자식 컴포넌트에 props를 넘겨주는 것과 같은 경우`) 하위 컴포넌트의 로직이 정의된 컴포넌트 클래스 내에 해당 속성의 변수를 정의하고 앞에 @input() 데코레이터를 사용해서 정의해준다.

# <b>@output() 데코레이터</b>

자식 컴포넌트에서의 이벤트를 통해 부모 컴포넌트의 클래스에 정의된 속성값을 업데이트하는 경우,(`React에서 자식 컴포넌트가 부모 컴포넌트로부터 이벤트 props를 전달받아 이벤트를 통해 부모 컴포넌트의 상태 데이터를 업데이트 하는 것과 같은 경우`) React와 같이 부모 컴포넌트에서 속성에 대한 업데이트를 위한 함수를 자식 컴포넌트에 emitter를 통해 전달을 하고 있다. 자식 컴포넌트 클래스에서는 EventEmitter 인스턴스 변수를 @Output 데코레이터와 함께 선언하고 EventEmitter 인스턴스가 사용되는 함수를 정의한다.

  <!-- more -->

`자식 컴포넌트 클래스`

```javascript
export class AddTodoComponent implements OnInit {
  // 부모 컴포넌트에 있는 todos 리스트에 event emitter를 통해 추가시키기 위해서
  // 아래와 같이 이벤트를 정의한다.(@Output)
  // EventEmitter의 타입은 string 제네릭 타입이다. (전달되는 값이 string newText)
  @Output() onTodoAdded = new EventEmitter<string>();
  newText: string;

  constructor() {
    this.newText = '';
  }

  ngOnInit() {}

  addTodo(newText: string) {
    this.onTodoAdded.emit(newText);
    this.newText = '';
  }
}
```

`부모 컴포넌트 클래스`
부모 컴포넌트 클래스에서는 부모 컴포넌트의 템플릿의 자식 컴포넌트에 전달된 emitter 속성값인 함수를 부모 컴포넌트 클래스 내부에서 같은 이름의 함수로 정의하여 클래스 내부에서 이벤트가 발생하도록 한다.

```javascript
export class TodosComponent implements OnInit {
  newText = '';
  todos: Todo[];
  today: Date = new Date();

  constructor() {
    this.todos = [
      { done: false, text: '운동하기' },
      { done: true, text: '공부하기' }
    ];
  }

  ngOnInit(): void {}

  toggleTodo(todo: Todo) {
    todo.done = !todo.done;
  }

  addTodo(text: string) {
    this.todos.push({
      done: false,
      text: text
    });
  }
}
```

`부모 컴포넌트의 템플릿`
자식 컴포넌트의 속성으로 (자식 컴포넌트에서 정의된 @Output() emitter variable name) = "부모 컴포넌트 클래스에서 이벤트 메서드"의 형태로 정의한다.
`자식 컴포넌트 클래스와 부모 컴포넌트 클래스의 이벤트 메서드의 이름이 같게 작성을 해서 헷갈리지만, 부모 컴포넌트의 템플릿에 정의된 자식 컴포넌트의 속성으로 들어간 emitter에 대한 이벤트 메서드는 부모 컴포넌트 클래스에 정의된 이벤트 메서드이다.`

```javascript
<div class="title">
    <h1>나의 하루</h1>
    <h2>{{ today | date:"M월 d일" }}</h2>
</div>
<div>
    <div *ngFor="let todo of todos" (click)="toggleTodo(todo)">
        <app-todo [todo]="todo"></app-todo>
    </div>
</div>
<div>
    <!-- $event는 자식 컴포넌트로부터 방출된 데이터로, newText 데이터이다. -->
    <app-add-todo (onTodoAdded)="addTodo($event)"></app-add-todo>
</div>
{{ todos | json }}
```

# <b>재사용되는 객체의 타입정의 interface</b>

재사용되는 객체의 타입정의 interface 파일의 경우에는 별도의 폴더를 만들어서 관리하도록 한다.

`todo.model.ts`

```javascript
export interface Todo {
  done: boolean;
  text: string;
}
```

# <b>단방향 바인딩과 양방향 바인딩 활용</b>

단방향 바인딩 방식으로 textarea에서 입력한 값을 버튼 클릭 이벤트 메서드에 전달해서 처리하기 위해서는 HTML 태그의 속성에 value와 #[id]에 대한 정의가 필요하다.

`post-create.component.html`

```html
<textarea rows="6" [value]="newPost" #postInput></textarea>
<hr />
<button (click)="onAddPost(postInput)">Save Post</button>
<p>{{ newPost }}</p>
```

`post-create.component.ts`

#[id]로 전달된 textarea HTML 요소는 아래와 같이 버튼의 클릭 이벤트 메서드를 통해 newPost의 값을 재정의한다.

```javascript
export class PostCreateComponent {
  newPost = 'NO CONTENT';

  onAddPost(postInput: HTMLTextAreaElement) {
    this.newPost = postInput.value;
  }
}
```

위의 예시에서는 단방향으로 value와 hashtag+id를 통해 사용자로부터 입력받은 값을 초기화했다면, value와 hashtag+id를 정의하지 않고
양방향 바인딩 방식으로 사용자 입력 데이터를 바인딩해줄 수 있다.

**양방향 데이터 바인딩을 하기 위해서는 `@angular/forms`로부터 제공되는 FormsModule을 @NgModule의 메타 데이터 객체의 imports에 정의를 해줘야 된다.**

```javascript
<textarea rows="6" [(ngModel)]="enteredValue"></textarea>
```

> 이처럼 Angular는 template binding 특징을 가지고 있으며, 이는 event binding, property binding, string interpolation, one-way/two-way binding을 통해 구현할 수 있다.

# <b>Angular Material</b>

[https://material.angular.io/](https://material.angular.io/)

```zsh
$ ng add @angular/material
```

설치된 dependency를 확인해보면, @angular/material @angular/cdk가 설치된 것을 알 수 있다.
material package는 이 두 가지 패키지로 구성이 되어있다.
(1) @angular/material : component logic + styling
(2) @angular/cdk : component logic

- ## <b>Angular Material의 prebuilt style 적용하기</b>

  angular.json 파일의 styles 속성에 설치한 material의 prebuilt style을 적용할 수 있다.

  ```json
  "styles": [
      "@node_modules/@angular/material/prebuilt-themes/indigo-pink.css"
  ],
  ```

  템플릿에 스타일링을 적용하기 위해서 최상위 모듈의 @NgModule 메타 객체의 imports에 사용하고자하는 material 스타일링 모듈을 포함시켜야 한다.
  (`자세한 내용은 공식문서를 참고`한다)
  → [https://material.angular.io/components/input/overview](https://material.angular.io/components/input/overview)

## <b>구조 디렉티브 ngIf와 ngFor의 사용</b>

템플릿의 HTML 태그의 속성에 ngIf와 ngFor을 넣어 조건과 반복구문 처리를 할 수 있다.

```html
<mat-accordion multi="true" *ngIf="posts.length > 0">
  <mat-expansion-panel *ngFor="let post of posts">
    <mat-expansion-panel-header> {{ post.title }} </mat-expansion-panel-header>
    <p>{{ post.content }}</p>
  </mat-expansion-panel>
</mat-accordion>
<p *ngIf="posts.length <= 0">No posts</p>
```

위와 같이 중첩의 형태로 ngIf와 ngFor를 사용할 수 있지만, 경우에 따라 구조 디렉티브를 사용하기 위해 불필요한 HTML 요소를 추가하는 경우가 있기 때문에
이러한 경우에는 `ng-container`와 `ng-template`을 사용한다.

Angular에서의 `ng-container`는 React에서의 `Fragment (<></>)`와 같다. 실제 DOM에는 추가되지 않고 복수의 HTML 요소를 묶어서 사용할 수 있다.

```html
<div>
  <ng-container * ngIf="true">
    <h1>타이틀</h1>
    <div>컨텐츠</div>
  </ng-container>
</div>
```

ng-template 또한 실제 DOM에는 추가되지 않고 복수의 HTML 요소를 묶을 수 있지만, ng-template으로 묶게 되면 항상 HTML element가 보이는 것이 아니기 때문에 ngIf로 조건처리를 반드시 해줘야 한다.
반면에 ng-container는 그룹화한 내부 HTML 요소를 항상 표현하기 때문에 이 점에서 ng-template과 차이가 있다.

## <b>Angular에서 제공하는 form 요소</b>

기존에 input 컨트롤러에서는 양방향 데이터 바인딩을 통해 컨트롤러의 값을 정의했다.
하지만 form 태그로 각 각의 컨트롤러를 감싸주게 되면, 양방향 바인딩으로 컨트롤러의 값을 정의하지 않고, 아래와 같이 간단하게 처리할 수 있다.
(form 요소가 감지되면, `module에서 정의한 FormsModule이 자동으로 자바스크립트 객체를 내부적으로 생성`해준다. 따라서 양방향 바인딩으로 컨트롤러 값을 정의하지 않아도 아래와 같이 간단하게 input 태그를 컨트롤러로써 등록해서 사용할 수 있다)

컨트롤러의 속성에 ngModel과 name 속성을 정의해주면 된다. 그리고 form 태그가 기본 HTML 태그이기 때문에 내부에서 사용되는 버튼에 별도의 이벤트 바인딩을 하지 않고, submit 타입으로 정의해주면 된다.

```javascript
<mat-card>
    <form (submit)="onAddPost()">
        <mat-form-field>
            <input matInput type="text" name="title" ngModel>
        </mat-form-field>
        <mat-form-field>
            <textarea matInput rows="4" name="content" ngModel></textarea>
        </mat-form-field>
        <button mat-raised-button color="accent" type="submit">Save Post</button>
    </form>
</mat-card>
```

form 태그 내에서 정의된 값들에 접근하기 위해서 태그의 속성에 local reference를 정의한다. local reference만 정의하면 이는 html 요소 객체에 접근하는 것이기 때문에 form 태그 내에 정의된 값에 대한 객체에 접근하기 위해서 `ngForm`을 local reference의 값으로 정의해야 한다.

```javascript
<mat-card>
    <form (submit)="onAddPost(postForm)" #postForm="ngForm">
        <mat-form-field>
            <input matInput type="text" name="title" ngModel>
        </mat-form-field>
        <mat-form-field>
            <textarea matInput rows="4" name="content" ngModel></textarea>
        </mat-form-field>
        <button mat-raised-button color="accent" type="submit">Save Post</button>
    </form>
</mat-card>
```

submit의 이벤트 메서드의 인자로 local reference를 전달해주게 되면, 아래와 같이 컨트롤러에 정의한 name 속성을 통해 값을 참조할 수 있다.

```javascript
export class PostCreateComponent {
    enteredContent = '';
    enteredTitle = '';
    // 외부 참조 app.component.html
    @Output() postCreated = new EventEmitter<Post>();

    onAddPost(form: NgForm) {
        const post: Post = { title: form.value.title, content: form.value.content};
        this.postCreated.emit(post);
    }
}
```

- ## <b>input 태그 입력에러 처리</b>

```html
<form (submit)="onAddPost(postForm)" #postForm="ngForm">
  <mat-form-field>
    <input
      matInput
      type="text"
      name="title"
      ngModel
      required
      minlength="3"
      #title="ngModel"
    />
    <!-- form 태그의 local reference를 통해 title input 컨트롤러에 접근 -->
    <mat-error *ngIf="postForm.getControl('title').invalid"
      >{{getErrorMessage()}}</mat-error
    >
    <!-- input 태그에 title local reference="ngModel"을 넣어주는 경우  -->
    <mat-error *ngIf="title.invalid">{{getErrorMessage()}}</mat-error>
  </mat-form-field>
</form>
```

- ## <b>long chains of property and event binding</b>

만약에 특정 요소를 얻기위한 속성/이벤트 바인딩이 A라는 컴포넌트에서 B 컴포넌트로, B 컴포넌트에서 C 컴포넌트로, C 컴포넌트에서 D 컴포넌트로 연쇄적으로 체이닝된다면, 이는 큰 어플리케이션을 개발할때 문제가 될 소지가 있다.

좀 더 효율적으로 데이터를 전달하기 위해서는 `service class`를 정의해서 사용해야 한다.
새롭게 생성한 service class는 components에 주입해서 사용한다.

이는 속성과 이벤트 바인딩 없이 컴포넌트에 데이터를 전달하는데 용이하다.
(`react의 redux와 같이 전역 storage에서 상태 데이터를 관리하는 것과 비슷하다`)

`posts.service.ts`

```javascript
import { Post } from "./post.model";

export class PostsService {
    private posts: Post[] = [];

    getPosts() {
        // copy posts object
        return [...this.posts];
    }

    addPost(title: string, content: string) {
        const post: Post = { title, content };
        this.posts.push(post);
    }
}
```

Angular는 복잡한 DI(Dependency Injection) 시스템을 가지고 있기 때문에 component class의 생성자의 인자로 앞서 작성한 service를 넣어서 Angular에게 알려주면 Angular는 service instance를 제공해준다.
제공된 service instance는 클래스 내부의 변수에 초기화를 시켜주면 된다.

```javascript
import { Component, OnInit, Input } from '@angular/core';
import { Post } from 'src/app/share/post.model';
import { PostsService } from 'src/app/share/posts.service';

@Component({
  selector: 'app-post-list',
  templateUrl: './post-list.component.html',
  styleUrls: ['./post-list.component.css']
})
export class PostListComponent implements OnInit {
  @Input() posts: Post[];
  postsService: PostsService;

  constructor(postsService: PostsService) {
    this.posts = [];
    this.postsService = postsService;
  }
}
```

하지만 타입스크립트에서 shortcut을 제공하기 때문에 생성자의 인자로 넣은 service 인자를 public 접근제어자로 선언을 해주면, 자동으로 클래스 내부에 속성을 생성해준다.

```javascript
import { Component, OnInit, Input } from '@angular/core';
import { Post } from 'src/app/share/post.model';
import { PostsService } from 'src/app/share/posts.service';

@Component({
  selector: 'app-post-list',
  templateUrl: './post-list.component.html',
  styleUrls: ['./post-list.component.css']
})
export class PostListComponent implements OnInit {
  @Input() posts: Post[];

  constructor(public postsService: PostsService) {
    this.posts = [];
  }
}
```

추가적으로 생성한 service 파일을 Angular가 스캔할 수 있도록 아래의 두 가지 방법 중 한 가지로 처리해줘야 한다.

**method1)** 최상위 module 데코레이터의 providers 메타객체에 작성한 서비스를 넣어준다.

**method2)** 작성한 service 클래스에 @Injectable() 데코레이터를 작성해준다.

```javascript
import { Injectable } from "@angular/core";
import { Post } from "./post.model";

@Injectable({providedIn: 'root'})
export class PostsService {
    private posts: Post[] = [];

    getPosts() {
        // copy posts object
        return [...this.posts];
    }

    addPost(title: string, content: string) {
        const post: Post = { title, content };
        this.posts.push(post);
    }
}
```

위의 두 가지 방법은 전체 앱에 하나의 service instance를 생성해준다.

- ## <b>constructor와 ngOnInit()</b>

constructor 메소드는 자바스크립트 엔진이 호출하는 주체이다. 따라서 Angular 컴포넌트의 생애주기를 다루기에는 적합하지 않다.

- ### <b>constructor를 사용하는 경우</b>

  앞서 살펴보았듯이 작성한 service 클래스를 `컴포넌트에 전달하는 의존성 주입을 하기 위해서 생성자를 사용`하였다.

- ### <b>ngOnInit을 사용하는 경우</b>
  ngOnInit은 Angular 컴포넌트 초기화가 완료된 시점을 알기 위해 사용된다.

만약에 부모 컴포넌트에서 자식 컴포넌트로 값을 전달하고, 자식 컴포넌트의 클래스에서 @Input() 데코레이터로 값을 바인딩한다고 가정하자.
이 `@Input() 데코레이터로 값을 바인딩한 속성으로의 접근은 ngOnInit()내에서는 가능하지만, 생성자 내에서는 undefined`이다.

따라서 바인딩된 데이터의 값을 읽기 위해서는 ngOnInit 생애주기 훅 내에서 처리한다.
