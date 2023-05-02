---
title: Monty Hall Problem
date: 2021-01-26 11:20:42
tags:
  - Algorithm
  - PseudoCode
  - ProbabilityPuzzle
categories:
  - Algorithm-problem-solving
---

## Monty Hall Problem (Pseudo code + Python code)

![](/images/post_images/210126_monty_hall.png)

> 앞서 작성한 Pseudo code와 손코딩 한 부분의 코드상에 논리적 문제를 발견했다. 만약 사용자가 선택한 문과 당첨 문이 일치하지 않는 경우, 진행자는 당첨문과 사용자가 선택한 문을 제외한 범위 내의 문을 열어서 보여줘야 하기 때문에 이 경우에는 당첨 문과 참가자가 선택한 문 둘 다 제거를 해줘야 한다. 만약 사용자가 선택한 문과 당첨 문이 같은 경우에는 사용자가 선택한 문을 제외한 두 개의 문 중에서 하나를 선택해서 문을 열어서 보여주면 된다.

  <!-- more -->

```python
import random

NUM_OF_SIMULATION = 10000
num_of_win = 0
num_of_condition = int(input(
    "Enter the number of condition(1: Keep first choice, 2: Change first choice)"))
for _ in range(NUM_OF_SIMULATION):
    car_door = random.randint(1, 3)
    participant_select_door = random.randint(1, 3)
    door = [i for i in range(1, 3+1)]
    # 당첨되는 문을 door 리스트에서 제거한다.
    if participant_select_door != car_door:
        door.remove(car_door)
        door.remove(participant_select_door)
    else:
        door.remove(car_door)
    open_door = random.choice(door)
    if num_of_condition == 1:
        if participant_select_door == car_door:
            num_of_win += 1
    elif num_of_condition == 2:
        # 참가자가 변심으로 처음 선택한 문을 바꾸는 경우, door 리스트를 다시 초기화한다.
        new_door = [1, 2, 3]
        new_door.remove(open_door)
        new_door.remove(participant_select_door)
        new_select_door = new_door[0]
        if new_select_door == car_door:
            num_of_win += 1
print(f"{(1.0 * num_of_win)/NUM_OF_SIMULATION * 100}%의 확률로 당첨될 수 있습니다.")
"""
위의 코드로 시뮬레이션을 해 본 결과 :
게임 참가자가 처음 선택한 문을 바꾸지 않을 경우(Condition1)에는
당첨될 확률은 32.48%(오차범위 ±2%)이고,
처음 선택한 문을 바꾸는 경우(Condition2)에는 66.24%(오차범위±2%)
의 확률로 당첨이 될 수 있습니다.
따라서 처음 선택한 문을 바꾸는 것이 당첨될 확률이 더 높습니다.
"""
```

<table>
    <tr>
        <td>
            <img src="/images/post_images/210126_Monty_open_door.png" width="800" height="500">
        </td>
        <td rowspan="2">
            <div> 이 Monty Hall 문제에 대해서는 처음 들어봤지만, 이전에 21이라는 영화를 보면서 교수가 학생에게 확률과 관련한 질문을 하였던 것을 기억하는데 이 문제가 바로 Monty Hall문제였다.

이 영화에서 질문을 받은 학생은 "한 번 선택을 하면 33%의 확률이지만, 바꿀 수 있는 한 번의 기회를 더 받았으므로 당첨될 확률이 두 배가 된다"고 대답했던 것 같다.

이 문제가 Monty Hall 문제라는 것은 처음 알고 재미있게 Pseudo code도 적고 손코딩으로 코드도 작성해보았다. </div>

</td>
</tr>
<tr>
<td span="2">
<img src="/images/post_images/210126_movie21.png" width="800" height="300">
</td>
</tr>
</table>
