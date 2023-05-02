---
title: 220528 데이터 파이프라인 구축 오프라인 수업 수강 전 준비
date: 2022-05-28 20:49:00
tags:
  - AWS
  - Data-Pipeline
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220528_preparation.jpeg" alt="준비">
</div>

<br/>
<br/>

## <ins><b>좀 더 많은 것을 얻어가기 위해서</b></ins>

앞선 블로그 포스팅에서 이미 언급했듯이, 내일 드디어 데이터 파이프라인 구축과 관련된 오프라인 수업을 들으러 간다.
여지까지 인터넷으로도 그렇고 수업을 들으면서 느낀거지만, 같은 시간, 같은 수업을 통해서 더 많은 것을 얻어가기 위해서는 사전에 이 수업을 통해서 무엇을 얻고자 하는지, 전체적인 수업 커리큘럼에 대한 이해가 필요하다. 아직 구체적인 수업내용에 대해서는 전달받지 못했지만, 그래도 표면적으로라도 어떤 것들을 배울 것인지에 대해 대략적인 정리를 하고 수업에 임한다면 좀 더 많은 것을 얻을 수 있을 것이라고 생각한다.

## <ins><b>수업 사전 준비사항</b></ins>

수업전에 ssh 접속을 위한 터미널 프로그램을 설치해오라는 사전공지가 올라왔다.
나는 기존에 그냥 Mac에서 기본으로 제공해주는 터미널을 통해 ssh 접속을 했었는데, 이번기회에 iTerm2를 사용해보려고 한다.

<!-- more -->

## <ins><b>iTerm2 설치 및 커스텀</b></ins>

iTerm2는 터미널과 같은 기능을 하지만, 더 다양한 기능을 제공해주는 친구이다.
처음 딱 설치를 해봤더니 기본으로 bash shell로 설정이 되어있다. 그래서 bash의 확장된 shell인 zsh을 설치해보려고 한다.

- ### **homebrew를 사용한 zsh 설치**

우선 homebrew를 설치한다.

```zsh
$/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- ### **Oh-my-zsh 이게 뭔데?**

구글링을 하던 도중에 zsh 설치와 함께 Oh-my-zsh을 설치해서 사용하면 좋다는 글들을 보았다. 찾아보니 Oh-my-zsh은 `Zsh 환경설정을 관리하기 위한 프레임워크`라고 한다.
이런 shell도 프레임워크를 통해 관리를 하는구나.. 처음 알게 되었다.
oh-my-zsh에는 많은 플러그인과 테마가 있어서 zsh을 좀 더 편하게 사용할 수 있게 해준다고 한다.
아무튼 그냥 일반 터미널만 사용해온 나 같은 사용자에게 아주 개쩌는 사용경험을 선서해 줄 그런 친구라는 말이다.
여지까지 개발관련 일을 했음에도 oh-my-zsh의 존재를 이제 알게 되었구나. 반성한다.

**$ oh my zsh 공식 홈페이지** [https://ohmyz.sh/](https://ohmyz.sh/)

우선 설치한 homebrew를 사용해서 zsh과 oh-my-zsh을 설치한다.

```zsh
$brew install zsh

$sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

<div align="center">
  <img src="/images/post_images/220528_oh_my_zsh.png" alt="oh my zsh">
</div>
oh my zsh을 설치하면 위와같이 알록달록한 텍스트들이 출력된다.

**색상 Theme 설정하기**

[https://iterm2colorschemes.com/](https://iterm2colorschemes.com/) 페이지로 가면, iTerms에 적용할 수 있는 다양한 색상 테마들이 나오는데, 그 중에서 나는 `synthwave` 테마가 마음에 들어서 선택했다.

```zsh
$brew install curl

$mkdir util && cd util

$curl -LO https://raw.githubusercontent.com/mbadolato/iTerm2-Color-Schemes/master/schemes/synthwave.itermcolors
```

이제 다운받은 iTerm color theme 파일을 iTerm의 preferences 메뉴-Colors에서 import를 해준다.

이후에 `터미널 theme과 Font`를 변경해주었다.

<div align="center">
  <img src="/images/post_images/220528_update_iterm2_theme.png" alt="iTerm2 theme 변경">
</div>

## <ins><b>내가 이 수업을 통해서 얻고자 하는 것</b></ins>

한 달 반 동안 인터넷 강의를 통해 대략적으로 데이터 파이프라인이 무엇이고, AWS를 활용해서 구축을 하게 되면, 어떤 이점이 있는지 그리고 AWS의 어떤 서비스를 이용해서 데이터 수집, 분석 파이프라인을 구성할 수 있는지에 대해서 배웠고, 직접 실습을 하면서 익숙해지고 있는 중이다.
나는 이번 오프라인 수업을 통해서 실제 데이터 엔지니어로써 현업에서 많이 사용되는 기술 스택과 현업에서 겪을 수 있는 문제점 등에 대해서 배우고 싶다. 아직 심화된 내용을 알기에는 지식이 부족하지만, 대략적으로 데이터 파이프라인을 구축에 대해서 학습한 현 상황에서 의문이 드는 부분이 AWS의 각종 서비스를 활용해서 데이터 파이프라인을 만들었는데, 실제 현업에서는 데이터 파이프라인을 구축한 다음에 어떻게 관리를 하고, 유지보수를 하는지 궁금했다.
그리고 이전에 배웠던 데이터 파이프라인은 개괄적인 내용들이어서 오프라인 수업에서는 커리큘럼에 명기되어있듯이, 현업에서 가장 많이 사용되고 있는 `Apache Kafka`와 `ELK 스택(Elasticsearch, Logstash, Kibana)`에 대해서 좀 더 알아보고 싶다.
대략적으로 각 기술 스택들이 어떤 역할을 하는지, 그리고 어떻게 구성되는지에 대해서는 개괄적으로 알고 있지만, 이번 수업을 통해 각 기술 스택들에 대해 알지 못했던 내용과 이미 알고 있던 내용이라도 좀 더 정제된 내용을 알고싶다.

이번 강의가 기대되는 이유중에 하나는 나와 같은 입문자들을 위해 최대한 핵심을 설명하고, 현업에서 겪을 수 있는 문제들에 대해서도 다룬다는 점이다. 단순히 데이터 파이프라인을 구성하는 각 컴포넌트에 대한 설명 뿐만 아니라, 각 설정 값들을 바꿔보면서 하나씩 실습을 해나간다는 점에서 매력적인 것 같다.
그리고 수업이 데이터 엔지니어 뿐 아니라 백엔드 개발자나 DevOps엔지니어 영역에서 사용되는 기술들도 다룬다고 하니, 향후에 가지치기 하면서 학습할때 방향설정에 많은 도움이 될 것 같다.

## <ins><b>수업에서 배우게 되는 기술 스택들</b></ins>

우선 수업을 듣기 전에 수업에서 다루게 될 각종 기술 스택들에 대해서 배경지식을 쌓는 것이 좋을 것 같다. 이미 데이터 파이프라인 구축에 대해서 학습해본 경험으로 미루어보면, 각 종 기술 스택들이 갑자기 밀려들어오면, 나도 모르게 갑자기 혼란스러워지는 타이밍이 온다. 그래서 우선 전체적인 데이터 파이프라인의 flow에 대해서는 이해를 하고 있으니, 각 종 기술 스택을 현재 가지고 있는 지식 수준에서 연결고리를 만들어서 배경지식을 쌓아놓으려고 한다.

- ### **데이터 수집 파이프라인**

`이미 한 번 학습을 한 내용` : logstash, Amazon EC2, Kafka, Amazon Kinesis Data Streams, Amazon Kinesis Data Firehose

`새롭게 학습하게 될 내용` : beats, fluentd, AWS Lambda

- ### **데이터 저장 및 분석 파이프라인**

`이미 한 번 학습을 한 내용` : Amazon S3, Elasticsearch, hadoop

`새롭게 학습하게 될 내용` : Apache Flink, Amazon REDSHIFT
