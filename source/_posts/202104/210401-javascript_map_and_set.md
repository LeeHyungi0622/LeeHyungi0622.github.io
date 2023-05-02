---
title: 210401 JavaScript의 Set()과 Map()에 대한 이야기
date: 2021-04-01 10:39:42
tags:
  - Map
  - Set
categories:
  - JavaScript
---

![](/images/post_images/210401_javascript_map_and_set.png)

알고리즘 문제 풀이를 하면서 중복된 요소를 제거하기 위해서 Set 자료구조를 자주 사용하였다.
그런데 이 Set 자료구조의 사용법에 대해서 자세히 설명하라고 하면 설명할 수 없을 것 같다는 생각에 자바스크립트에서 자주 사용되는 자료구조인 Set과 Map 자료구조의 사용법에 대해 비교해서 정리해가며 공부를 해보려고 한다.

## Set

Set은 value들로 이루어진 집합이다. Array와는 다른점은 같은 value를 두 번 포함할 수 없다는 것이다. 따라서 보통 중복을 제거하고자 할때 이 Set 자료구조를 많이 사용한다.

<!-- more -->

(1) has([value]) : 주어진 Set 내부에 value값이 존재하는지, 존재하지 않는지 확인할 수 있다.(true/false 반환)

(2) add([value]) : 지정한 value 값을 추가할 수 있다.

(2) delete([value]) : 지정한 value 값을 제거한다.

(3) clear() : Set 내부의 모든 데이터를 삭제할 수 있다.

```javascript
let testSet = new Set();
testSet.add('a').add('b');
console.log(testSet.size); // 2
console.log(testSet.has('a')); // true
console.log(testSet.has('z')); // false
testSet.delete('a');
console.log(testSet.has('a')); // false
testSet.clear();
console.log(testSet.size); // 0
```

**union(합집합), intersection(교집합), difference(차집합)**
Set 자료구조를 사용하여 합집합, 교집합, 차집합을 구현해보려고 한다.

```javascript
let setA = new Set([1, 2, 3, 4, 5]);
let setB = new Set([4, 5, 6, 7, 8]);

// 합집합
let unionSet = new Set([...setA, ...setB]);
for (let v of unionSet) {
  console.log(v); // 1, 2, 3, 4, 5, 6, 7, 8
}

// 교집합
let intersectionSet = new Set([...setA].filter((v) => setB.has(v)));
for (let v of intersectionSet) {
  console.log(v); // 4, 5
}

// 차집합
let differenceSet = new Set([...setA].filter((v) => !setB.has(v)));
for (let v of differenceSet) {
  console.log(v); // 1, 2, 3
}

// 대칭차 집합(SymmetricDifference)
let symmetricDifferenceSet = new Set([
  ...[...setA].filter((v) => !setB.has(v)),
  ...[...setB].filter((v) => !setA.has(v))
]);

for (let v of symmetricDifferenceSet) {
  console.log(v); // 1, 2, 6, 7
}
```

## Map

Map은 JavaScript에서 Key와 Value가 한 쌍(pair)을 이루는 Collection이다.
`map.keys()와 map.values()`는 모두 map의 key와 value값들을 iterable한 객체로써 반환한다.

```javascript
let testMap = new Map().set('a', 1).set('b', 2);
console.log([...testMap.keys()]); // ['a', 'b']
console.log([...testMap.values()]); // [1, 2]
```

`map.entries()`를 사용하면, map 안의 모든 entries를 순회할 수 있는 iterable한 객체로 반환해준다.

```javascript
let person = new Map().set('a', 1).set('b', 2);
let iterPerson = person.entries();
console.log(iterPerson.next()); // {value: ['a', 1], done: false}
console.log(iterPerson.next()); // {value: ['b', 2], done: false}
console.log(iterPerson.next()); // {value: undefined, done: true}
```

`for-of와 forEach로 map객체 순환하기`

```javascript
let person = new Map().set('a', 1).set('b', 2);

// for-of로 map 객체 순환하기
for (let [key, value] of person) {
  console.log(key, value);
  // a 1
  // b 2
}

// forEach에서는 key value의 순서가 다르다.
person.forEach((value, key, map) => {
  console.log(key, value, map);
  // a 1 Map { 'a' => 1, 'b' => 2 }
  // b 2 Map { 'a' => 1, 'b' => 2 }
});
```

자바스크립트의 배열 메서드에만 존재하는 `map, filter`method는 Map()에 존재하지 않지만, 아래와 같이 우회 사용이 가능하다.

```javascript
let person = new Map().set('a', 1).set('b', 2);

// value가 1인 entry만 filter
let filteredPerson = new Map([...person].filter(([k, v]) => v === 1));

console.log(filteredPerson.entries()); // { [ 'a', 1 ] }

let appendCharPerson = new Map([...person].map(([k, v]) => [k + 'K', v + 1]));

console.log([...appendCharPerson.entries()]); // [ [ 'aK', 2 ], [ 'bK', 3 ] ]
```
