---
title: 220603 AWS Certified Solutions Architect Associate Certificates (SAA-C02) (작성중...)
date: 2022-06-03 14:58:00
tags:
  - Certificates
  - AWS
categories:
  - AWS
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220509_aws_saa_c02_training.jpeg" alt="AWS Architect Certification Training">
</div>

<br/>
<br/>

이번 포스팅에서는 본격적으로 AWS SAA(Solutions Architect Associate) level에 맞는 파트에 대해서 학습을 시작할 것이다.

그 첫 시작으로 Private, Public, Elastic IP의 비교에 대한 내용을 학습할 것이다. 이전에 EC2 인스턴스를 생성한 뒤에 속성으로 간략하게 위의 3가지 종류의 IP에 대해서 살펴보았는데, `EC2인스턴스를 중지 후 재 실행하게 되면, Public IP 주소가 새롭게 생성되기 때문에 고정 IP로 사용하려면 Elastic IP를 사용하면 되고, Elastic IP의 경우에는 실제 사용이 될 때가 아닌, 사용이 되지 않을때 요금이 부가된다는 내용`이 기억에 남는다.

그럼 좀 더 세부적으로 이론을 배워보고 실습해봐야겠다.

## <ins><b>Private & Public & Elastic IP</b></ins>

- Elastic IPs는 인스턴스의 고정된 Public IP를 위해 필요하다.
- Elastic IP Address는 가지고 있는데, 사용하지 않으면 과금이 된다.
- 빠르게 계정 내 다른 인스턴스로 주소를 매핑함으로써 인스턴스나 소프트웨어의 실패를 마스킹 할 수 있게 도와준다.
- 계정당 5개의 Elastic IP를 가질 수 있다. (필요에 따라 AWS 요청하여 갯수 확장 가능)
- 종합적으로 살펴보면, `Elastic IP 주소를 사용하는 것은 권장되지 않는다.`
  -> Elastic IP 주소를 사용하는 Architecture는 안좋은 구조적 결점으로써 언급되기도 한다.(`안좋은 Architecture`)
- 대신 `random public IP를 사용하고, 해당 IP에 DNS 이름을 지정해서 사용`하도록 한다. (DNS -> Route 53 - 훨씬 더 많은 제어가 가능하고, 확장 가능성도 크다)
- 또는 `Load Balancer를 사용하고, public IP를 사용하지 않도록 하는 방법`도 있다. (`AWS에서 취할 수 있는 최상의 Pattern`이다)

## <ins><b>Private IP and Public IP in AWS EC2</b></ins>

EC2인스턴스에 SSH 연결을 하는 경우, 같은 네트워크에 있는 것이 아니기 때문에 Private IP를 사용할 수 없다. (단 VPN을 사용하는 경우 가능)
Private IP는 AWS의 내부 네트워크 통신을 위해 사용되며, 인터넷 연결을 위해서는 Public IP가 사용된다.
오직 Public IP를 사용해서 EC2 인스턴스에 SSH 연결을 할 수 있다.

<!-- more -->

## <ins><b>EC2 배치 그룹</b></ins>

Ec2 인스턴스의 배치그룹은 인스턴스가 AWS 인프라에 배치ㅐ되는 방식을 제어하고자 할 때 사용된다. EC2 인스턴스 배치 전략을 통해 다양한 case에 따라 EC2 인스턴스를 사용할 수 있다.

- EC2 instance Placement group은 AWS H/W와 직접적인 상호작용을 하지 않는다.
- 배치 그룹 전략에는 `Cluster`, `Spread`, `Partition`이 있다.
  - `Cluster` : 하나의 AZ에서 low-latency group으로써 EC2 인스턴스를 배치한다. (`EC2 인스턴스의 네트워킹, 컴퓨팅 성능이 많이 요구되는 경우 사용되는 배치 전략`) 단, 같은 AZ와 Rack(H/W) 내에 구성되어있기 때문에 실패를 하게 되면, 모든 인스턴스가 동시에 실패하게 된다.
  - `Spread` : AZ 당 최대 7개의 인스턴스 배치가 가능하며, 각기 다른 분산된 AZ에 EC2 인스턴스를 할당한다.
  - `Partition` : AZ내의 각기 다른 렉(rack)에 의존하며, 그룹당 수백개의 EC2인스턴스를 통해 확장할 수도 있고, 이를 통해 Hadoop, Cassandra, Kafka와 같은 어플리케이션을 실행할 수 있다. (Partition을 구분할 수 있는 어플리케이션)
    Partition은 AWS의 각 각의 Rack을 나타내며, 물리적으로 각 각의 instance들이 격리되어 Rack의 실패로 인해 모든 instance가 실패하는 경우를 예방할 수 있다. 그리고 각 파티션 정보는 메타 데이터를 통해 접근/확인이 가능하다.

## <ins><b>ENI(Elastic Network Interface)</b></ins>

## <ins><b>EC2 Hibernate mode</b></ins>

## <ins><b>EC2 고급 개념(Nitro, vCPU, 용량 예약)</b></ins>

## <ins><b>첫 번째 EC2 SAA Level Test</b></ins>
