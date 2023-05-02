---
title: 210425 DFS(Depth-First-Search):깊이 우선 탐색
date: 2021-04-25 07:54:00
tags:
  - Algorithm
  - Stack-frame
  - Tree-traversal
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210425_DFS.png" alt="DFS(Depth-First-Search)">
</div>

DFS(Depth-First-Search) 깊이 우선 탐색은 그래프의 모든 정점을 탐색하는 가장 단순한 방법이다.
현재 정점과 인접한 간선들을 하나씩 검사하다가 방문하지 않은 정점으로 향하는 간선이 있다면 해당 간선을 따라가 정점을 검사하고, 더 이상 갈 곳이 없는 막힌 정점에 도달하면 포기하고 마지막에 따라왔던 간선으로 되돌아간다.
거쳐왔던 모든 정점들에 대한 정보를 저장하기 위해서는 `재귀호출`을 이용해서 간단하게 해결할 수 있다. 재귀 호출한 함수가 종료하면 호출한 위치로 다시 돌아가기 때문이다.

<div align="center">
  <img src="/images/post_images/210425_dfs_handwriting_note.png" alt="DFS handwriting note">
</div>

  <!-- more -->

```javascript
// 1부터 n까지의 원소를 갖는 집합의 부분집합 구하기
function solution(n) {
  let answer = [];
  // 지나간 정점을 구분하기 위해서 길이가 n인 check 배열 생성(1부터 n개까지 표시하기 위해서 n+1)
  let ch = Array.from({ length: n + 1 }, () => 0);

  // v = 1 (시작점)
  function DFS(v) {
    // n은 solution의 인자값 3이기 때문에
    // v(vertex)가 4일때 D(4)만 조건문에 만족한다.
    if (v === n + 1) {
      //부분집합 완성할때마
      let tmp = '';
      for (let i = 1; i <= n; i++) {
        if (ch[i] === 1) tmp += i + ' ';
      }
      if (tmp.length > 0) answer.push(tmp.trim());
    } else {
      ch[v] = 1;
      // 정점의 왼쪽 가지치기
      DFS(v + 1);
      // 다시 해당 노드로 돌아왔을때에는 ch[v]의 값을 0으로 초기화하고
      ch[v] = 0;
      // 정점의 오른쪽 가지치기
      DFS(v + 1);
      // 오르쪽 노드에서 다시 정점으로 돌아왔을때에는 모든 코드가 실행 완료되었기 때문에
      // 상위 노드로 이동해서 waiting된 시점의 코드부터 실행한다.
    }
  }
  // v = 1 (시작점)
  DFS(1);
  return answer;
}
console.log(solution(3));
```
