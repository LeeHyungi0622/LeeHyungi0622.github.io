---
title: 210308 React 좋은 테스트의 조건과 효과 그리고 테스트 시나리오 작성법
date: 2021-03-08 18:56:42
tags:
  - React-router-test
  - React-testing-library
  - Unit-testing
  - Jest
  - TDD
categories:
  - React-Test
---

![](/images/post_images/210308_test_scenario_img.png)

<br/>

## <ins><b>좋은 테스트의 조건과 효과</b></ins>

좋은 테스트란 무엇일까? 무작정 테스트 코드를 작성해보려고 했지만 막상 좋은 테스트란 무엇인지 알지 못했다.

우선 **첫 번째, 테스트의 의도가 명확해야 한다.** 코드의 가독성은 중요하다. 좋은 코드는 기계가 아닌 사람이 읽기 쉬워야 한다. 누군가 내가 작성한 테스트 코드를 보았을때 한 눈에 어떤 내용을 테스트하고 있는지 파악할 수 있어야 한다. 테스트 코드가 너무 장황해지거나 불필요하게 복잡해진다면 별도의 함수를 만들어 추상화시켜주는 것이 좋다.

**두 번째, 좋은 테스트란 빠른 피드백을 받을 수 있으며, 개발 속도를 빠르게 할 수 있도록 해야 한다.** 테스트 결과를 보기 위해 오랫동안 기다려야 하는 테스트는 개발 과정에서 좋지 않다.

  <!-- more -->

**세 번째, 내부 구현을 변경했을때, 테스트가 깨지지 않도록 해야한다.** interface 기준으로 테스트를 작성하거나 테스트하는 부분이 다른 테스트 부분과 서로 종속적인 관계가 되어서는 안된다. 또한 너무 작게 테스트 단위를 쪼개서도 안된다. 작은 refactoring에도 작성한 테스트가 깨진다면 신뢰성 있는 테스트가 될 수 없으며 테스트를 수정하는 비용이 발생되어 코드 개선에 오히려 방해가 된다.

**네 번째, 버그를 검출할 수 있어야 한다.** 잘못된 코드를 검증하기 위해 작성한 테스트 코드가 버그를 잡지 못하지 못한다면 이는 치명적이다. 모의(Mock)객체를 너무 과하게 사용하게 되면 의존성이 있는 객체의 동작이 바뀌었을때 버그를 검출할 수 없게 된다. 따라서 테스트 코드를 작성할 때에는 명세는 구체적으로 작성하며, 모의 객체를 생성해서 테스트 하는 것은 최대한 지양하는 것이 좋다.

**다섯 번째, 안정적인 테스트 결과가 나와야 한다.** 오늘성공했던 테스트 코드가 내일은 실패하거나 특정 기기에 한정되서 테스트가 통과된다면 이는 신뢰할 수 없는 테스트 코드이다. 외부 환경의 영향을 최소화해서 언제 어디서든지 동일한 결과를 보장할 수 있도록 해야 한다.

## <ins><b>Test scenario</b></ins>

- BDD(Behavior Driven Development)를 기준으로 테스트 코드 작성

- 테스트 시나리오는 Given(Set up initial state) / When(Perform action(s)) / Then(Check end state) 세 가지 스탭으로 구상한다.

- 각 테스트는 다른 테스트에 의존적이지 않아야 한다.
- API, LocalStorage관련한 테스트는 Mock, Dummy를 사용하여 대체/작성한다.
- 모든 테스트 코드는 명확한 의도가 드러나게 작성해야 한다. 테스트 코드만 보더라도 테스트하고자 하는 Component가 어떤 기능을 담당하고 있는지 파악할 수 있어야 한다.