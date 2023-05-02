---
title: 210627 회사업무 적응 1주차 리뷰
date: 2021-06-27 15:06:00
tags:
  - Work-life-balance
categories:
  - Work
---

<div align="center">
  <img src="/images/post_images/210627_self_improvement.png" alt="퇴근 후 자기개발">
</div>

## <b>새로운 환경과 새로운 습관</b>

뭐 예상했던 부분이지만, 일을 시작하고 나서 매일매일 자기개발을 하던 습관이 시들해지고 있다.
아직 새로운 환경에 적응하는 시기라는 이유로 합리화를 하고는 있지만, 그래도 경험상으로 비춰보면 이러한 자기 합리화가 또 다른 나 자신의 안일함 조장한다고 생각한다.

그래서 고정적으로 한 가지 습관을 만들고자 한다.
바로 `매주 업무를 통해서 배웠고 느꼈던 점`에 대해서 일요일마다 나의 블로그에 포스팅하는 것이다.

업무에 대한 구체적인 내용은 적을 수 없지만, 개괄적으로 어떤 업무를 맡게 되었고, 그 업무를 진행하면서 어떤 점을 배우게 되었는지, 그리고 향후에 그 업무를 처리하기 위해서 어떤 부분에 대한 추가 학습이 필요한지에 대해서 내용을 정리해보고자 한다.

위의 내용들을 정리해두면 평일에 업무가 종료된 후에 새롭게 학습한 내용에 대해서 정리를 할 수 있게 되고, 그 다음 피드백을 할 수 있게 되어 선 순환 구조가 될 수 있을 것이라고 생각한다.

## <b>업무 1주차 : 프로모션 페이지</b>

<div align="center">
  <img src="/images/post_images/210627_promotion.jpeg" alt="프로모션 페이지">
</div>

- ## <b>새롭게 담당하게 된 업무</b>

  입사 후에 처음으로 담당하게 된 업무는 `프로모션 페이지`를 만드는 작업이다.
  모바일이 아닌 데스크탑 기준의 레이아웃 작업만 하면 되기 때문에 별도의 반응형 작업을 할 필요가 없다.
  이후에 모바일에 대해서는 별도의 프로젝트가 진행될 예정이라고 하셨다.

  이번 프로모션 페이지에는 정적인 페이지에서 사용자의 외부입력을 통해 페이지에 동영상이나 음성을 재생하는 기능을 포함하고 있다.

  <!-- more -->

- ## <b>업무를 통해 배운점과 느낀점</b>

  - ### <b>업무에 대한 배경지식의 중요성</b>

    처음 위의 업무를 배당받았을때 구체적으로 기존 서비스에서 어떤 부분을 홍보하기 위한 목적에서의 프로모션 페이지인지 생각하지 않고, 우선 디자이너로부터 받은 디자인 시안에 맞게 페이지를 작성하기 시작했다.
    아마 이 부분이 좀 실수였지 않았나 싶다. 왜냐하면 새롭게 추가되는 기능이 어떤 것인지에 대한 배경지식을 갖은 상태에서 작업을 했더라면, 페이지를 만들면서 디자이너나 다른 개발자 분에게 여러차례 확인을 하면서 작업을 이어나갔을텐데 내가 담당하게 된 업무의 배경지식이 많이 부족했다.
    <br/>

  - ### <b>영상과 음성 리소스</b>

    프로모션 페이지의 작업을 하면서 페이지에 이벤트 처리에 대한 부분이 두 부분 포함되어있다는 것을 확인할 수 있었다. 그리고 디자이너로부터 받았던 Zeplin 디자인 시안 이외에 동영상과 오디오 관련 파일을 확인했다.
    단순히 확인에만 그치지 않고 `용량은 어떻게 되는지, 해당 리소스 파일들을 어떤식으로 호출해서 사용해야 되는지에 대한 고민을 했었어야 했다.`
    영상 및 이미지 파일은 `*.mov, *.webm, *.png`확장자의 파일들이었고, 용량은 로컬에 보관해서 호출하기에는 무리가 있는 사이즈였다.

    - ### <b>왜 다양한 확장자의 영상 파일이 주어졌는가?</b>

      다양한 확장자의 영상 파일이 주어진 이유는 여러 브라우저에서 재생할 수 있는 영상의 확장자가 재각각이기 때문이다.

      <table style="overflow-x: auto; width: 100%; min-width: 500px;">
      <thead>
      <tr>
      <th style="width: 18.4884%; text-align: center;" rowspan="2">브라우저</th>
      <th style="width: 47.093%; text-align: center;" colspan="3">비디오</th>
      <th style="width: 32.7907%; text-align: center;" colspan="2">오디오</th>
      </tr>
      <tr>
      <th style="width: 14.3023%; text-align: center;">mp4</th>
      <th style="width: 16.3953%; text-align: center;">webm</th>
      <th style="width: 16.3953%; text-align: center;">ogv</th>
      <th style="width: 16.3953%; text-align: center;">mp3</th>
      <th style="width: 16.3953%; text-align: center;">ogg</th>
      </tr>
      </thead>
      <tbody>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>인터넷 익스플로러</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (9+)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">O (+9)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>크롬</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (all)</td>
      <td style="text-align: center; width: 16.3953%;">O (25+)</td>
      <td style="text-align: center; width: 16.3953%;">O (all)</td>
      <td style="text-align: center; width: 16.3953%;">O (all)</td>
      <td style="text-align: center; width: 16.3953%;">O (all)</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>파이어폭스</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (35+)</td>
      <td style="text-align: center; width: 16.3953%;">O (28+)</td>
      <td style="text-align: center; width: 16.3953%;">O (3.5+)</td>
      <td style="text-align: center; width: 16.3953%;">O (22+)</td>
      <td style="text-align: center; width: 16.3953%;">O (3.5+)</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>사파리</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (3.2+)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">O (4+)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>오페라</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (25+)</td>
      <td style="text-align: center; width: 16.3953%;">O (16+)</td>
      <td style="text-align: center; width: 16.3953%;">O (11.5+)</td>
      <td style="text-align: center; width: 16.3953%;">O (15+)</td>
      <td style="text-align: center; width: 16.3953%;">O (11.5+)</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>iOS 사파리</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (all)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">O (4.1+)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      </tr>
      <tr>
      <td style="text-align: center; width: 18.4884%;"><span style="color: #5733b1;"><b>안드로이드 브라우저</b></span></td>
      <td style="text-align: center; width: 14.3023%;">O (4.4+)</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">X</td>
      <td style="text-align: center; width: 16.3953%;">O (2.3+)</td>
      <td style="text-align: center; width: 16.3953%;">O (2.3)</td>
      </tr>
      </tbody>
      </table>
