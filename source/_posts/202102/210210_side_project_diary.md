---
title: 210210 Side Project Diary 3/4/5일차
date: 2021-02-10 11:03:42
tags:
  - Self-Development
  - Side-Project-Diary
categories:
  - Side-Project-Diary
hidden: true
secret: true
---

![](/images/post_images/side_project_diary_img.jpg)

## **Side Project Diary 3/4/5일차**

오늘은 3,4,5일차에 진행되었던 Side Project의 진행 상황에 대해서 정리해보려고 한다. 우선 Side Project의 진척이 빠르게 진행되지 않는 이유는 단위테스트(Unit Test)를 같이 진행하기 위해서이다. **Jest와 RTL(React-Testing-Libary)를 사용한 JavaScript 코드 및 React component, axios mockup 테스트** 관련해서 별도의 공부가 필요하다고 생각되어, 우선적으로 테스트 관련해서 기본적으로 필요한 공부들을 했다.
공부한 내용과 연습용 Repository는 모두 정리해서 블로그에 올렸다.

[(1) 210209 RTL(React Testing Library)를 활용한 Axios Mockup 테스트](https://leehyungi0622.github.io/2021/02/09/202102/210209-React_testing_library_start/)

**[⮕ 실습 Repository link (1)](https://github.com/LeeHyungi0622/react_testing_library_mock_axios_request_practice_repo)**

[(2) 210208 Jest의 개념과 기본사용](https://leehyungi0622.github.io/2021/02/08/202102/210208-JS_Jest_start/)

[(3) 210208 Jest의 다양한 matcher](https://leehyungi0622.github.io/2021/02/08/202102/210208-JS_Jest_matchers/)

  <!-- more -->

**[⮕ 실습 Repository (2,3)](https://github.com/LeeHyungi0622/javascript-jest-test-practice-repo)**

Side Project에서 처음 진행되었던 부분이 Open API를 통해서 데이터를 fetch해서 가져오는 method를 작성하는 것이었는데, Unit Test할때에는 직접 서버로부터 데이터를 가져와서 테스트를 하지 않고, mockup한 데이터를 사용해서 테스트해야 한다.
이 부분을 위해 `Jest와 RTL(React Testing Library)를 활용하여 단위 테스트`를 어떻게 해야 되는지 공부를 하고, 실습도 하면서 많은 도움이 되었던 것 같다.

언어와 개발환경은 다르지만, 이전에 PHP Laravel Framework로 작성된 웹 어플리케이션을 운용보수하며 단위 테스트 코드를 작성한 경험이 있었기 때문에 단위테스트의 중요성과 단위테스트에 대한 전반적인 이해는 쉽게 할 수 있었던 것 같다.

이제 단위 테스트에 대한 기본적인 부분은 공부가 되었으니 혹시 부족한 부분은 중간 중간에 업데이트해가면서 어제 새롭게 업데이트 한 `Issue #8, #9를 진행`해봐야겠다.

공부하면서 개별 테스트가 필요한 내용은 별도의 Repository를 만들어서 실습하고, 내용들을 README 파일에 정리해가면서 해보니 나중에 다시 참고할때에도 좋은 것 같다.
