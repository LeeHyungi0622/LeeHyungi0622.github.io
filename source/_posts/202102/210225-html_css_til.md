---
title: 210225 HTML/CSS TIL - CSS 구체성, 구조 선택자, CSS 유효성 검사, 레이아웃 설계, 접근성 지침서(WCAG, KWCAG), 로그인 폼 설계 및 구현, explicit outline과 implicit outline, DTD(S,T,F), 웹 관련 용어 부분 레이아웃 설계 및 구현
date: 2021-02-25 19:56:42
tags:
  - HTML
  - CSS
  - TIL
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/html_css_logo.png" alt="HTML/CSS"/>
</div>

## <ins><b>HTML/CSS 네번째 수업</b></ins>

오늘은 HTML/CSS 네번째 수업날이었다. 오늘은 혼자서 레이아웃을 설계도 해보고, 직접 생각해서 레이아웃을 구성해보는 연습도 하였다.
연습하면서 느낀점은 역시 많은 연습이 필요하다는 것이다. 계속해서 끊임없이 노력하자.

## <ins><b>CSS 구체성</b></ins>

오늘은 수업의 첫 시작은 CSS의 구체성에 대해서 시작하였다. 이 CSS의 구체성이란 특정 태그에 스타일을 적용시킬때 태그의 어느 요소(id, class(가상클래스 포함), element(태그))를 가지고 얼마나 구체적으로 명시해서 스타일을 적용했느냐를 말한다. 표면적으로는 같은 태그의 스타일을 적용했지만, 선택자를 좀 더 구체적으로 명시한 쪽의 스타일이 적용되는 것은 선택자로 사용한 요소(id, class, element)에 각 각 우선순위 점수가 있기 때문에 점수가 높은 (선택자를 좀 더 구체적으로 지정한) 스타일이 최종적으로 적용되는 스타일이 되는 것이다.

  <!-- more -->

<table border="1">
  <tr align="center">
    <th>id(100)</th>
    <th>class(10)</th>
    <th>element(1)</th>
  </tr>
  <tr align="center">
    <td>0</td>
    <td>2</td>
    <td>1</td>
  </tr>
  <tr align="center">
    <td>0</td>
    <td>1</td>
    <td>1</td>
  </tr>
</table>

```css
/* 점수표에서 첫 번째 행(21) */
.member li::before {
  content: '|';
}
/* 점수표에서 두 번째 행(11) */
.first::before {
  content: ' ';
}
```

아래와 같이 p태그의 스타일을 적용한다고 했을때, 구체성 점수에 의해 `p.test`의 스타일이 적용된다.

```css
p {
  color: red;
} /* 1점 */
p.test {
  color: blue;
} /* 11점 */
.note {
  color: green;
} /* 10점 */
.test {
  color: puple;
} /* 10점 */
```

결과적으로 구체성 점수가 높은 첫 번째 스타일이 적용된다.
`개당 배점이 높은 순은 id > class > element이다.`
`id의 우선순위는 점수상 id:1 == class: 10의 관계를 갖기 때문에 CSS 스타일링을 줄때 id를 사용하지 않고 class를 사용한다.`

하지만 `!important` 를 사용하면 구체성 점수와 상관없이 !important가 붙은 스타일이 최우선으로 적용된다.

```css
p {
  color: red !important;
}
```

## <ins><b>구조 선택자</b></ins>

좀 더 세밀하게 태그 내의 요소를 지정해주기 위해서는 `구조 선택자`를 사용하면 된다. 구체성에 대해 재미있게 정리해놓은 사이트가 있으니 아래 사이트를 참고하도록 하자.
[http://nthmaster.com](http://nthmaster.com)
nth-child나 nth-of-type을 사용하면 좀 더 세밀하게 태그를 선택할 수 있다.

`nth-child(3n+1): nth-child(even)`는 지정한 태그를 3개씩 묶어서 각 묶음에 있는 짝수번째 자식요소를 선택한다는 의미이다.
`span: nth-of-type(3)`는 span 태그 중에서 세 번째 span 요소를 선택한다는 의미이다.
이러한 구조 선택자를 사용해서 좀 더 세밀하게 태그를 지정해서 스타일링을 해 줄 수 있다.

## <ins><b>CSS 유효성검사</b></ins>

CSS도 유효성 검사를 해 줄 수 있기 때문에 필요에 따라서는 W3C CSS validator를 사용해서 작성한 CSS에는 문제가 없는지 유효성 검사를 하도록 하자.
[https://jigsaw.w3.org/css-validator/](https://jigsaw.w3.org/css-validator/)

## <ins><b>레이아웃을 만들때는 설계부터</b></ins>

화면 구성을 컴포넌트 단위로 나눠서 화면을 설계해야 나중에 유지보수나 코드의 재사용성에 좋다. 따라서 화면을 컴포넌트 단위로 잘 나눠서 설계를 하기 위해서는 레이아웃을 작성할때 무작정 코드를 작성하지 말고, 어떤 태그를 사용하고 어떤 스타일을 적용할 것인지 구체적으로 화면을 설계하는 것이 좋다.

## <ins><b>접근성 지침서 확인하기</b></ins>

웹 접근성 지침 관련해서 미국에 대한 표준과 한국에 대한 표준이 있다.

사소해 보일 수 있지만, "변경약관 여기 클릭하세요"라는 문구에서 보통 **여기**라는 부분에 링크를 걸지만 원래 접근성 측면에서는 **약관**에 링크를 걸어서 처리하는 것이 맞다.

- ### WCAG(Web Content Accessibility Guidelines)
- ### KWCAG(Korean Web Content Accessibility Guidelines)
  WCAG 표준에 따르기 어렵다고 한다. 하지만 우리나라의 대한항공 영문 페이지를 확인하면, 이 WCAG 표준에 잘 따르고 있다고 한다. 아래의 사이트를 참고하도록 하자.
  **대한항공 :** [https://www.koreanair.com/us/en](https://www.koreanair.com/us/en)
  KWCAG(한국 웹 컨텐츠 접근성 가이드라인)에 대한 내용은 아래 웹 페이지에서 확인할 수 있다.
  [https://www.wah.or.kr:444/board/boardView.asp?page=2&brd_sn=4&brd_idx=975](https://www.wah.or.kr:444/board/boardView.asp?page=2&brd_sn=4&brd_idx=975)

## <ins><b>로그인 폼 설계 및 구현하기</b></ins>

우선 로그인 폼의 설계부터 진행해보자. 우선 로그인 폼에서 필요한 항목을 정리해보자. 필요한 항목으로는 id, password, 로그인 버튼, 회원가입/아이디・비밀번호 찾기의 항목이 필요하다. 레이아웃에 대한 wireframe은 아래 첨부한 두번째 노트를 확인하자. 레이아웃 설계중에 가장 까다로운 부분이 이 form 태그 부분이다. 그렇기 때문에 정확한 수치를 지정해서 레이아웃을 설계하도록 하자.

- #### <ins><b>하나의 input 태그에는 하나의 label을 사용하도록 권고</b></ins>

  - \<label>태그내에 `for`속성을 사용해서 해당 label이 어떤 태그를 위한 label인지 알려줘야 한다. (`명시적 레이블링(explicit labeling)`)

    ```html
    <div><label for="userEmail" /><input type="text" id="userEmail" /></div>
    ```

  - 아래와 같이 label 태그로 관련된 태그를 감싸서 표현할 수도 있다. (`암묵적 레이블링(implicit labeling)`)
    **이러한 label처리도 19.레이블 제공이라는 항목으로 검사를 할 수 있다. (사다리 모양-OpenWAX)**

  - 두 번째 노트의 우측 그림을 참고하도록 하자. 이 그림은 한 개의 label 태그에 두 개 이상의 입력 속성이 존재하는 경우를 설명한다.
    예를들어, 주민번호 입력의 경우 "주민번호"라는 label에 주민번호 앞 6자리, 뒤 7자리라는 두 개의 입력 속성이 존재한다. 이러한 경우에는 `input태그에 title 속성(title="주민 앞 6자리", title="주민 뒤 7자리")을 넣어서 구분`해주면 된다.

- #### <ins><b>필수 입력 항목과 필수 입력이 아닌 항목을 `<fieldset>`태그를 사용해서 분류</b></ins>

- #### <ins><b>옵션이지만 `<legend>`태그를 사용해서 분류한 `<fieldset>`에 묶여있는 서식들의 성격을 알려주도록 하자.</b></ins>

  - 예를들어 회원 로그인을 위한 서식들을 묶었다면 `회원 로그인 틀`이라고 태그내에 정의해주자.

- #### input 태그의 name 속성은 DB의 field 이름이다.

## React는 XHTML 문법을 차용한다.

img 태그를 Self-closing tag로 처리한다.

## <ins><b>유명한 서비스라고 완벽한 것은 아니다.</b></ins>

네이버 로그인 페이지를 보면, 아이디와 비밀번호 입력란에 placeholder로 아이디와 비밀번호를 넣어주었고, 각 input 태그에 matching되는 label태그를 숨김컨텐츠로써 처리해서 추가해주었다. 이렇게 처리를 하게되면, 스크린리더가 화면을 읽어줄때, 숨김컨텐츠로써 처리해준 label과 placeholder를 연달아 두 번 읽어주게 된다. 이렇게 화면의 레이아웃을 처리하는 것이 회사의 규정으로 굳어진 경우도 있지만, 소리에 의존하는 시각장애인에게는 불편한 요소가 될 수 있다.
그렇기 때문에 이러한 경우에는 placeholder대신에 input 태그위에 label 태그를 overlap해서 표시했다가 마우스 focus가 입력상자에 갔을때 해당 label의 텍스트 크기를 원래의 텍스트 크기보다 작게 줄여서 input 태그의 위에 표시될 수 있도록 애니메이션 효과를 처리할 수도 있다.
이 부분은 꼭 실습해서 정리해두자.

## <ins><b>explicit outline과 implicit outline</b></ins>

세 번째 노트의 우측에 그려놓은 section 태그를 보면 내부에 heading태그와 ul태그를 묶어주고 있다. 이 section 태그는 생략이 가능하며, 별도로 section 태그로 묶어주지 않아도 암묵적으로 heading태그와 ul태그가 묶인 형태로 인식이 된다.
직접 section태그로 묶어서 레이아웃을 작성하는 것을 명시적(explicit)outline, section태그를 생략해서 레이아웃을 작성하는 것을 암시적(implicit)outline이라고 정의한다.

## <ins><b>다양한 form tag의 submit 버튼 처리</b></ins>

- #### input 태그로 type을 button으로 처리
  role을 "button"으로 넣어주고, 값을 value 속성으로 접근한다.
- #### button 태그로 처리
  값을 노드로 접근한다.

## <ins><b>DTD (S, T, F)</b></ins>

- #### DTD(Document Type Definition)?

  웹 페이지의 html 코드의 최 상단에 \<!DOCTYPE html>이라고 선언되어 있는 것을 확인할 수 있다. 이 부분을 DTD라고 한다. 문서 형식 정의로, SGML 계열을 비롯해서 HTML, XHTML, XML등의 마크업 언어에서 문서 형식을 정의할때 사용되고 있다.
  여기서 말하는 마크업 언어란 태그를 이용하여 문서나 데이터의 구조를 명기하는 언어를 말한다.
  DTD는 해당 문서가 어떤 문서 형식을 따르고 있다고 정의하는 역할을 한다. 이 DTD 선언에 따라 브라우저의 렌더링 모드가 바뀌게 되고, 사용될 수 있는 태그와 속성이 바뀌게 된다.
  이 DTD를 선언하지 않을 경우, 브라우저가 표준모드가 아닌, 비표준모드(Quirks mode)로 렌더링되어 크로스 브라우징 문제를 겪게 된다.

- 문서의 타입으로는 S(trict), T(ransitional), F(rameset)이 있다.

  - **Strict Type은** 엄격한 규격으로 CSS 사용을 장려하기 위해 점차 단계적으로 사라질 표현에 대한 태그와 속성을 배제한 문서 타입이다.
  - **Transitional Type은** 과도기적인 규격으로, 표준이 정립되지 않은 때에 기존에 만들어진 문서들과의 호환성을 위해서 사용된다.
  - **Frameset Type은** 현재는 거의 사용되지 않는 프레임셋(html 안에서 html을 분리하는 것)을 구현하는데 사용된다.

  `HTML5의 경우`에는 SGML(Standard Generalized Language)에 기반을 두지 않아서 DTD 참조가 필요없고, 최손한의 코드 작성이 기본 방향이기 때문에 간단하게 선언할 수 있다. 그래서 DTD 선언은 선택적이지만 하위 호환성을 위해서 `<!DOCTYPE html>`을 선언하는 것이 권장된다.

  <ins><b>DTD 선언 예시</b></ins>

  ```html
  HTML 4.01 Strict
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">

  HTML 4.01 Transitional
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

  HTML 4.01 Frameset
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" http://www.w3.org/TR/html4/frameset.dtd">

  XHTML 1.0 Strict
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

  XHTML 1.0 Transitional
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

  XHTML 1.0 Frameset
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">

  XHTML 1.1
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

  HTML5
  <!DOCTYPE html>
  ```

## <ins><b>의미있게 HTML 태그를 사용하자.</b></ins>

## <ins><b>웹 관련 용어 부분 레이아웃 설계 및 구현하기</b></ins>

설계에 대한 부분은 네 번째 노트필기를 참고하자. 구체적인 구현에 관한 내용은 별도의 블로그 글로 포스팅하였다.
[https://leehyungi0622.github.io/2021/02/26/202102/210226-html_css_layout_practice/](https://leehyungi0622.github.io/2021/02/26/202102/210226-html_css_layout_practice/)

## <ins><b>개발에 유용한 블로그 및 깃허브 추천</b></ins>

**김태곤 프론트엔드 개발자님 블로그**
→ [https://taegon.kim/archives/5770](https://taegon.kim/archives/5770)

**Yamoo9님의 깃허브**
→ [https://github.com/yamoo9](https://github.com/yamoo9)

<hr/>

<ins><b>remind를 위해 수업시간에 필기했던 노트를 첨부한다. </b></ins>

<table>
  <tr>
    <td><img src="/images/post_images/210225_html_css_note1.png" alt="개인공부 노트1"></td>
    <td><img src="/images/post_images/210225_html_css_note2.png" alt="개인공부 노트2"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210225_html_css_note3.png" alt="개인공부 노트3"></td>
    <td><img src="/images/post_images/210225_html_css_note4.png" alt="개인공부 노트3"></td>
  </tr>
  
</table>
