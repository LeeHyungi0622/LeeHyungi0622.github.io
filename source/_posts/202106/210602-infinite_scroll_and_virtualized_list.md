---
title: 210602 Infinite scrolling과 Virtualized list
date: 2021-06-02 08:00:00
tags:
  - faker
  - dummy-data
  - self-development
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210602_infinite_scroll_virtualized_list.png" alt="infinite scroll과 virtualized list"/>
</div>

## <ins><b>Infinite scrolling 구현</b></ins>

Infinite scrolling을 구현하기 위해 스크롤이 가장 최 하단으로 이동했을때 이벤트를 발생시켜야 한다.
실제 실무에서는 이벤트 발생시점이 최하단이 아닌 하단에서 일정 px 떨어진 이전 시점에 미리 로드할 데이터를 로딩시켜 사용자가 스크롤링을 했을때 끊기는 느낌이 들지 않도록 한다.

```javascript
useEffect(() => {
  // 현재 스크롤 위치
  function onScroll() {
    // 스크롤된 높이를 구하는데 아래 세가지 함수가 많이 쓰인다.
    console.log(
      // (1) 얼마나 내렸는지
      // 가장 최하단으로 스크롤을 내리면, 최 하단으로 내려간 페이지의 최 상단이 마지막 내려간 지점이 된다.
      window.scrollY,

      // (2) 화면에 보이는 길이(최상단에서 하단 스크롤 위까지 길이)
      document.documentElement.clientHeight,

      // (3) 총 길이 (스크롤의 제일 위부터 아래까지 총 길이)
      // (=) window.scrollY + document.documentElement.clientHeight
      document.documentElement.scrollHeight

      // (1)과 (2)의 합이 (3)과 같을때 스크롤이 가장 최 하단으로 내려갔다는 것을 의미한다.
      // 이 시점에 새로 로딩
    );
    // 끝에서 300px 위보다 더 많이 내렸을때
    if (
      window.scrollY + document.documentElement.clientHeight >
      document.documentElement.scrollHeight - 300
    ) {
      // loadPostLoading이 false인 경우에만 새로운 요청을 한다.
      if (hasMorePosts && !loadPostLoading) {
        dispatch({
          type: LOAD_POST_REQUEST,
          data: mainPosts[mainPosts.length - 1].id
        });
      }
    }
  }

  window.addEventListener('scroll', onScroll);

  // 반드시 스크롤했떤 이벤트를 해제시켜줘야 한다.
  // 안그러면 메모리상에 계속 남아있다.
  return () => {
    window.removeEventListener('scroll', onScroll);
  };
}, [hasMorePosts, mainPosts, loadPostLoading]);
```

## <ins><b>throttle과 takelatest</b></ins>

`infinite scrolling을 구현하면서 scroll이벤트가 발생할때마다 매번 비동기 요청이 발생`을 하였는데, 이 문제를 해결하기 위해 throttle과 takelatest를 사용해서 해결해보려고 했다.
`하지만 throttle과 takelatest 모두 요청(Async request)은 차단되지 않고, 응답(Response)만을 차단`하기 때문에 만약에 5s 간격으로 throttle 처리를 하게 되면, 받은 요청에 대해서는 취소되지 않고, 5s 간격으로 지연되서 처리가 되는 것을 확인할 수 있었다.

`본질적으로 해결하고자 하는 것은 scroll이벤트에서 발생되는 요청 자체를 차단`하는 것이기 때문에 이 문제 해결을 위해 redux의 상태 데이터에서 해당 비동기 처리 요청에 대한 loading과 관련된 상태값을 활용하여 해결하였다.

  <!-- more -->

## <ins><b>Virtualized list / React virtualized</b></ins>

`Virtualized list`은 인스타그램에서 사용되고 있는 기술이다. 실제로 서버로부터 가져오는 수천개의 데이터 중에 실제 화면에 보이는 데이터만 유지를 하고, 보이지 않는 데이터들은 메모리에서만 보관하여 실제 DOM에서는 보이지 않고 사라지도록 한다.
실제 개발자 도구를 통해 확인을 해보면 스크롤할때 화면에 보이는 정보만 DOM에 표시되고, 화면에서 사라진 정보에 대해서는 DOM에서 사라지는 것을 확인할 수 있다.

`그럼 왜 위와같은 기술이 생겨난 것일까?`

그 이유는 실제 화면에 표시되는 게시글들 하나 하나가 모두 메모리이기 때문에 모바일에서 대량의 게시글들을 확인하게 되면, 메모리가 터지는 경우가 생긴다.
그래서 위와같은 문제를 해결하기 위해 `Virtualized list 기술`이 등장하였다.

<ins><b>React에서 Virtualized list를 구현하기 위해서 아래 두 개의 링크를 참고하도록 하자.</b></ins>
**[참고] : [https://www.npmjs.com/package/react-virtualized](https://www.npmjs.com/package/react-virtualized)**

**[참고] : [https://web.dev/virtualize-long-lists-react-window/](https://web.dev/virtualize-long-lists-react-window/)**
