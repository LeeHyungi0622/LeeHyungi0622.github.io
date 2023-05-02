---
title: 220531 (실시간 데이터 생성을 위한)라즈베리파이 + 라즈베리파이 Sense HAT
date: 2022-05-31 13:44:00
tags:
  - RaspberryPi
categories:
  - RaspberryPi
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/220531_raspberrypi_and_hat_sensor.jpg" alt="RaspberryPi4와 RaspberryPi Sense HAT">
</div>

<br/>
<br/>

이번 포스팅에서는 개인 프로젝트를 목적으로 구입한 라즈베리파이 4B 모델과 라즈베리파이 SENSE HAT 종합 센서 모듈에 대해 포스팅을 하려고 한다.

우선 라즈베리파이 모듈을 주문한 이유는 첫 번째 `실시간으로 생성되는 데이터가 필요`했다. 기존 CSV 포멧의 데이터와 같이 정적인 데이터의 경우에는 인터넷에서 많이 구할 수 있지만, 실시간 스트리밍 데이터의 경우에는 (물론 log관련 데이터를 뽑아주는 API 서비스가 있긴 하지만) 찾기가 어려웠다. 그래서 고민하던 도중에 `한 번 직접 실시간으로 스트리밍해주는 데이터를 만들어보자고 결심`했고, 이것이 계기가 되어 라즈베리파이와 종합센서 모듈을 구입하게 되었다.
이번 반도체 대란으로 인해, 좀 웃돈을 주고 구입을 하였지만, 뭔가 프로젝트의 일부이긴 하지만 일상의 약간의 변화를 줄 수 있는 것 같아 주문을 하게 되었고, 라즈베리파이의 파이가 파이썬과 연관이 많이 되어있어, 현재 자주 사용하고 있는 파이썬 언어로 코딩도 많이 해 볼 수 있을 것 같았다.

<!-- more -->

## <ins><b>RaspberryPi + RaspberryPi SENSE HAT 셋팅</b></ins>

<div align="center">
  <img src="/images/post_images/220531_raspberrypi_with_hat.jpg" alt="RaspberryPi4와 RaspberryPi Sense HAT 결합">
</div>
