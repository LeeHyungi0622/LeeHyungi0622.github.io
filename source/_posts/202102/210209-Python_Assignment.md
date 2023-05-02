---
title: 210209 Python Assignment
date: 2021-02-09 15:29:41
tags:
  - Python
  - Assignment
  - Class-design
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

## 과제1) 성수역 지하철 열차 시스템

<img src="/images/post_images/210209_train.png" alt="210209_train">

### 지하철 노선 클래스

```python
# 2호선 내부순환선 (총 43개 역) 시청역 <->시청역 (내부순환)
# 성수지선 (총 5개 역) 성수역 <-> 신설동
# 신정지선 (총 38개 역) 성수역 <->까치산
# class instance를 만들때 각 노선의 최대 정착역의 수를 구한다.
class SubwayLine:
    def __init__(self, num_of_stn):
        # 지하철역의 수 만큼 [0]리스트를 만들어서
        # 역별로 정차하고 있는 지하철의 수를 관리
        self.num_of_stop_train_list = [0]*num_of_stn

    # 해당 index위치의 count값을 1증가 시켜준다.
    def increaseNumOfStopTrain(self, idx):
        if self.num_of_stop_train_list[idx] == 0:
            self.num_of_stop_train_list[idx] += 1
        else:
            #  위험 알람 method를 호출해서 False값을 반환하도록 한다.
            self.setDangerAlert()

    def decreaseNumOfStopTrain(self, idx):
        if self.num_of_stop_train_list[idx] == 1:
            self.num_of_stop_train_list[idx] -= 1

    # 해당 index위치에 정차되어있는 지하철의 수를 반환한다.
    def getNumOfStopTrain(self, idx):
        return self.num_of_stop_train_list[idx]
    # 해당 역 위치에 지하철이 정차되어있는 경우, False값을 반환해서 출발할 수 없도록 한다.

    def setDangerAlert(self):
        return False
```

  <!-- more -->

### 지하철 열차 클래스

```python
class Subway:
    # line : 노선
    # train_num : 열차번호
    # acc_dis : 누적 거리
    # dep_stn : 이전 출발역
    # arr_stn : 도착역
    # speed : 속도 (기본속도 21km/h)
    # direct : 운행방향 (CW:시계 방향, CCW:시계 반대방향)
    # drive_flag : 운행유/무
    # stop_flag : 정차유/무
    def __init__(self, _line, _train_num, _acc_dis, _dep_stn, _arr_stn):
        self.line = _line
        self.train_num = _train_num
        self.acc_dis = acc_dis
        self.dep_stn = dep_stn
        self.arr_stn = arr_stn
        self.speed = 21
        self.direct = 'CW'
        self.drive_flag = False
        self.stop_flag = True
    # 지하철 운행
    def drive(self, distance):
        self.drive_flag = True
        self.stop_flag = False
        self.acc_dis += distance
    # 지하철 정차
    def stop(self):
        self.drive_flag = False
        self.stop_flag = True
    # 방향 재설정
    def setDirection(self):
        if self.direct == 'CW':
            self.direct = 'CCW'
        else:
            self.direct = 'CW'
    # 지하철의 속도 조정
    def setSpeed(self, _speed):
        self.speed = _speed
    # 이전 출발역 업데이트
    def setDepartStation(self, _dep_stn):
        self.dep_stn = _dep_stn
    # 도착역 업데이트
    def setArrStation(self, _arr_stn):
        self.arr_stn = _arr_stn
```

## 과제2) Hacker rank class2 - Find the Torsional Angle

<img src="/images/post_images/210209_hackerrank_find_torsional_angle.png" alt="210209_hackerrank_find_torsional_angle">

```python
import math

class Points(object):
    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z
    def dot(self, no):
        return (self.x*no.x)+(self.y*no.y)+(self.z*no.z)
    def __sub__(self, no):
        return Points(float(self.x - no.x),(self.y - no.y),(self.z - no.z))
    def cross(self, no):
        return Points((self.y*no.z - self.z*no.y),(self.z*no.x - self.x*no.z),(self.x*no.y - self.y*no.x))
    def absolute(self):
        return pow((self.x ** 2 + self.y ** 2 + self.z ** 2), 0.5)
```
