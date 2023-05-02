---
title: 210206 What is difference between Framework and Library?
date: 2021-02-06 10:42:42
tags:
  - Self-Development
  - Framework
  - Library
categories:
  - ReactJS
---

![](/images/post_images/210206_difference_between_framework_and_library.webp)

## **What is difference between Framework and Library?**

이전에 개발을 하면서 내가 사용하고 있는 것이 프레임워크인지 라이브러리인지 헷갈렸던 경험이 있었기 때문에, 나같은 사람이 없을 수도 있겠지만, 혹시 나와 같은 의문을 가진 사람들을 위해 프레임워크와 라이브러리의 차이에 대해서 정리해보려고 한다.

우선 메인으로 첨부한 그림을 보면 대략적으로 알 수 있듯이 **Framework는** 내가 작성한 Application을 감싸안고 포함고 있는 개념이라면, **Library는** 내가 작성한 Application과 아래의 우주선과 같이 도킹(docking)되어 있는 것이라고 정의할 수 있겠다.
따지고 보면 국제 `우주정거장 == Application`이 되겠고, `크루드래곤 == Library` 라고 생각하면 될 것이다.
<br/>

![](/images/post_images/210206_spacex_crew-dragon_docking.jpg)
**<ins>위 사진은 작년에 2020년 5월 31일 스페이스 X의 크루드래곤이 국제 우주정거장에 도킹을 성공하는 실제 장면이다.</ins>**

이제 프레임워크와 라이브러리의 기본적인 형태에 대해서 이해가 되었으니, 내부 동작에 있어서는 어떤 차이가 있는지 살펴보자.

  <!-- more -->

![](/images/post_images/210206_library_vs_framework.jpg)

위에 첨부한 그림에서 알 수 있듯이 `우선 Libary는 Framework에 포함된 개념`이다.
Framework자체를 개발에 필요한 여러 Library의 집합으로 정의를 할 수도 있습니다.
Libray를 사용하기 위해서는 코드에서 호출을 해야하지만 Framework는 애플리케이션의 골격으로, 해당 골격과 맞지 않으면 Framework에서 애플리케이션 코드로 호출을 해서 알려주게 됩니다.

이상, 간단하게 Library와 Framework의 컨셉과 차이점에 대해서 정리해보았습니다.
