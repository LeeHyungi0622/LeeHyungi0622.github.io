---
title: 220415 AWS 서비스-1
date: 2022-04-15 10:57:00
tags:
  - AWS
categories:
  - AWS
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220415_aws_service.webp" alt="AWS Services">
</div>

<br/>
<br/>
이번 포스팅에서는 AWS의 가장 기초적인 자격증인 Practitioner 자격증 공부를 하면서, 기록할 필요가 있다고 생각되는 내용을 정리해보려고 한다.
현재 데이터 파이프라인 구축관련 공부를 하면서 AWS 서비스를 활용하여 실습을 하고 있는데, Storage와 Database 부분이 중요하다고 생각되기 때문에 우선적으로 이 부분에 대한 정리를 시작으로 다른 중요한 AWS 서비스들도 정리를 해나가보려고 한다.

## <ins><b>Amazon EBS(Amazon Elastic Block Storage)</b></ins>

<div align="center">
  <img src="/images/post_images/220415_amazon_ebs.png" alt="Amazon EBS">
</div>

데이터 파이프라인을 실습하면서 복수 개의 EC2 인스턴스를 생성하고, 계속 구동시켜놓으면 비용이 발생하기 때문에 사용하지 않을때에는 잠시 중지상태로 전환시켜두었다. 그런데 다시 구동시켰더니 내부에 저장해두었던 리소스들이 사라져있었다.
그 이유는 바로 EC2 인스턴스 가상 서버로, 내부 리소스는 `Instance store volume`이라는 블록 저장 공간에 저장을 하게 되는데, 중지 후 다시 재구동을 하게 되면 가상 서버는 베이스가 되는 부분이 재 생성되면서 기존에 사용되었던 `instance store`가 삭제되게 된다. (`EC2의 임시 블록 수준 스토리지`)
이러한 이유로 EC2 인스턴스를 중지후 재구동을 하여도 지속적으로 남기게 하기 위해, Amaozn EBS라는 블록 스토리지 솔루션이 등장했다.
Amazon EBS는 EC2 인스턴스를 중지한 뒤에 다시 실행하여도 내부에서 저장하였던 리소스를 보존시켜준다.
EC2 인스턴스에 EBS를 연결하려면 동일한 가용 영역(AZ)상에 있어야 한다. 저장은 하지만, 2GB EBS 볼륨을 프로비저닝해서 가득 채우게 되면, 볼륨이 자동으로 확장되지 않는다.

Snapshot을 지원해는데, 나중에 Snapshot에서 data를 복구할 수도 있다.
(최대 16TiB, SSD, HDD 모두 제공)
`cf.SI 접두어 방식으로 하면, TB(테라바이트)인데, 이를 이진 접두어 방식으로 하면, TiB(테비바이트)가 된다.`

<!-- more -->

## <ins><b>Amazon S3(Amazon Simple Storage Solution)</b></ins>

<div align="center">
  <img src="/images/post_images/220415_amazon_s3.png" alt="Amazon S3">
</div>

이전에 데이터 파이프라인 실습하면서 간단하게 정리했지만, Bucket이라는 디렉토리 개념의 공간에 객체라는 파일 개념의 형태로 데이터를 저장하는 곳을 Amazon S3라고 했다. 최대 업로드 객체 크기는 `5TB`이며, `버전관리도 지원`하기 때문에 실수로 삭제되는 일도 사전에 예방할 수 있다. 그리고 `접근 권한 설정`도 할 수 있어서 객체에 액세스할 수 있는 사람을 제한하는 설정도 별도로 할 수 있다.
S3의 종류에는 `S3 Standard`가 있으며, 높은 내구성을 제공한다. 그리고 HTML 및 Assets을 업로드하면 정적 웹 사이트를 호스팅 할 수 있다.
그 외에도 액세스 빈도가 낮지만 필요에 따라 빠르게 액세스를 해야되는 데이터의 경우에 사용되는 `S3-IA(Infrequent Access)`가 있다. 백업, 재해 복구 파일 또는 장기 보관이 필요한 모든 객체에 적합하다.
`(무한대의 스토리지 제공, 개별 객체크기 5,000GB, WORM에 특화)`

## <ins><b>Amazon S3 Gracier</b></ins>

<div align="center">
  <img src="/images/post_images/220415_amazon_s3_glacier.jpeg" alt="Amazon S3 Gracier">
</div>

이전에 데이터 파이프라인 실습을 하면서 이 Gracier라는 서비스에 대해서 언급이 되었었는데, 데이터베이스의 데이터를 Archieving하는데 사용된된다고 배웠다.
이 Amazon S3 Gracier는 감사 데이터와 같이 수년간 데이터를 저장하고, 그리 자주 검색이 되지도 않는 경우, 이 Amazon S3 Gracier를 사용해서 데이터를 보관하게 된다.
Gracier를 사용하는 방법은 `데이터를 Gracier로 옮기거나 저장소를 만든 다음에 Archive로 채우면 된다.`
유지 기간 같은 데이터 유지 관련 규정 준수 요구 사항이 있다면, S3 Gracier 저장소 잠금 정책을 적용하여 저장소를 잠글 수도 있으며, WORM(Write One, Read Many) 제어정책을 지정해서 잠글 수도 있다.(`한 번 잠긴 정책은 변경 불가`)

또한 S3의 수명 주기 정책을 사용하여 데이터를 여러 계층으로 자동으로 이동하게 할 수 있게 할 수 있다.
`ex.객체를 S3 Standard에서 90일 동안 유지 -> S3-IA에서 30일 동안 유지 -> (120일 후) -> 객체를 S3 Glacier로 이전 `

## <ins><b>Object storage(S3) VS Block storage(EBS)</b></ins>

Object storage의 경우에는 업로드되는 모든 파일들을 개별 객체로 취급하기 때문에 파일을 수정한 다음에 업로드하게 되면, 수정된 객체 파일 전체가 다시 업로드된다.
반면에 Block stoarge의 경우에는 일부 파일이 수정이 되면, 수정된 파일의 수정된 일부만 다시 업로드된다.
이처럼 완성된 객체를 사용하거나 변경 횟수가 적다면 S3를 사용하는 것이 좋으며, 복잡한 읽기, 쓰기, 변경 기능을 수행한다면 EBS가 적합하다.
상황에 따라 적합한 서비스를 선택할 수 있어야한다.

## <ins><b>Amazon EFS(Elastic File System)</b></ins>

<div align="center">
  <img src="/images/post_images/220415_amazon_efs.png" alt="Amazon EFS">
</div>
Amazon EFS는 관리형 파일 시스템으로, 여러 인스턴스 동시 읽기 및 쓰기가 가능하며, EBS와 같이 쓰기 작업을 할 수 있는 빈 하드 드라이브 개념이 아니다. Linux를 위한 진정한 파일 시스템으로, Region resource이다. Region의 모든 EC2 인스턴스를 EFS 파일 시스템에 쓸 수 있다는 의미이다.
EFS는 EBS와 달리 추가 데이터를 쓰게 되면, 자동으로 공간이 확장되며, 별도로 추가 볼륨을 프로비저닝할 필요가 없다.

## <ins><b>Block storage(EBS) VS File storage(EFS)</b></ins>

파일 스토리지에서는 여러 클라이언트가 공유 파일 폴더에 저장된 데이터에 엑세스할 수 있다.
이 접근 방식은 Storage server가 Block storage를 Local file system과 함께 사용하여 파일을 구성한다.
클라이언트는 파일 경로를 통해 데이터에 접근한다.

Block storage 및 Object storage와 비교하면, File storage는 많은 수의 서비스 및 리소스가 동시에 동일한 데이터에 접근해야 되는 경우에 이상적이다.
AWS 클라우드 서비스 + On-Premise 리소스를 함께 사용되는 확장 가능한 파일 시스템으로, 어플리케이션을 중단하지 않고, On-demand로 페타바이트 규모로 확장할 수 있다.
