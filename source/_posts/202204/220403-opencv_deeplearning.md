---
title: 220403 OpenCV를 이용한 Computer Vision w/Python
date: 2022-04-03 16:20:00
tags:
  - OpenCV
  - ComputerVision
  - TIL
categories:
  - Computer-Vision
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/220403_opencv_computer-vision.png" alt="OpenCV를 이용한 Computer Vision"/>
</div>

이번 포스팅에서는 OpenCV를 이용한 Computer Vision에 대해서 정리해보려고 한다.

## <ins><b>실습환경 구성</b></ins>

다운받은 cvcourse_macos.yml 파일을 이용해서 가상황환경을 만들어, 많은 라이브러리를 다운받는다.

```zsh
$conda env create -f cvcourse_macos.yml
```

구성된 환경을 활성화하기 위해서는 아래의 명령어로 활성화한다.

활성화된 뒤에는 아래의 명령어로 jupiter notebook을 실행한다.

```zsh
$jupyter-lab
```

```zsh
$conda activate python_cvcourse
$conda deactivate
```

## <ins><b>NumPy와 Image 기본</b></ins>

이전 수업시간에 NumPy를 배운적이 있는데, 이때는 배열을 삭제하거나 슬라이싱, 인덱싱하는 방법에 대해서 배웠다.
그런데 넘파이로 이미지를 읽어와서 출력하는 방법도 있다고 한다.

파이썬으로 이미지를 작업할때에는 주로 배열로 나타내기 때문이다.

넘파이 -> OpenCV로 확장하면서 학습

컴퓨터는 이미지 데이터를 배열로 읽는다.

## <ins><b>NumPy ? </b></ins>

## <ins><b> 컴퓨터가 이미지를 어떻게 핸들링하는가?</b></ins>
