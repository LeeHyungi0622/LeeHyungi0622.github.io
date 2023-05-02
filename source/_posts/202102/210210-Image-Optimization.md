---
title: 210210 Hexo Image Optimization
date: 2021-02-10 17:00:00
tags:
  - TIL
  - Hexo-Image-Optimization
categories:
  - Hexo
---

<div align="center">
  <img src="/images/post_images/210210_image_optimization.png" alt="Image Optimization"/>
</div>

<br/>

Hexo 블로그를 운영하면서 업로드한 이미지들 때문에 웹 페이지의 로딩시간이 이전보다 많이 느려졌다.
그래서 업로드한 이미지 파일들을 모두 compression해서 파일크기를 1/4 사이즈로 만든다음에 업로드하였다. 그런데도 웹 페이지 로딩시간이 생각보다 오래 걸려서 한 번 Googling을 해보았다.

그래서 발견한 것이 `Hexo Image Optimization`이다.

Hexo 내에 존재하는 모든 image파일 확장자를 가진 파일들을 최적화 시켜주고, 별도의 설정으로 post의 thumbnail 이미지도 최적화 시켜준다고 한다.

그래서 친절하게 설명되어 있는 설명을 보며, 최적화 설정을 모두 마쳤다.

  <!-- more -->

```bash
$ hexo generate && hexo img
```

혹시 나와같이 블로그를 운영하다가 같은 문제로 고민이 있는 사람을 위해 참고한 GitHub 주소를 첨부한다.

요즘에 블로그 포스팅을 하면서, 이전에 우연히 보게 된 영상에서 개발자 유튜버가 하신 말씀이 생각난다.
`"개발자는 알고있는 지식을 소유하는 것이 아니라 공유하는 것이다."`

아직은 아는 것이 많이 없지만, 이제부터 새롭게 알게 되거나 유익한 내용들을 블로그를 통해서 많이 공유해야겠다.

<br/>

**[Hexo-img-optimization GitHub URL]**
[https://github.com/vkuznecovas/hexo-img-optimization](https://github.com/vkuznecovas/hexo-img-optimization)

![](/images/post_images/210211_img_optimization.png)
