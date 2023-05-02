---
title: 220623 Terraform study
date: 2022-06-23 14:23:00
tags:
  - Terraform
categories:
  - Terraform
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220614_terraform.png" alt="Terraform">
</div>

<br/>
<br/>

이번 포스팅에서는 실제 프로젝트에서 AWS Topology를 Terraform 코드로 작성(IaC)해보면서 전체적인 프로젝트의 공통적인 구조를 어떻게 작성을 할 것인가에 대해 방향을 잡아가면서 정리한 내용을 작성해보려고 한다.

## <ins><b>데이터 파이프라인 구축에 있어, AWS network topology 구성</b></ins>
  <div align="center">
    <img src="/images/post_images/220623_basic_aws_network_topology.jpg" alt="기본 AWS 네트워크 토폴로지 구성">
  </div>

  우선 AWS의 서비스를 활용해서 데이터 파이프라인을 구축함에 있어, 전체적인 AWS network topology의 구성은 중요하다. 실습을 했을때는 default VPC, Subnet에 각 각의 AWS 서비스를 활용해서 network topology를 구성을 했지만, 실무에서는 default vpc와 subnet을 사용하지 않는 것을 권장한다고 한다.
  실제 업무에서는 Production에서 각 각의 서비스별로나 네트워크 보안 정책에 따라서 VPC를 나눠서 관리하고, 세팅하는 게 일반적이라고 한다. (`DE 현직자 오프라인 수업을 통해 배웠다.`)
  AWS 서비스 중에서 완전 관리형 서비스가 아닌 서비스들은 VPC를 타지만, 완전 관리형 서비스의 경우에는 VPC가 아닌 별도의 AWS Zone(AWS 전용 네트워크 라인)을 탄다고 한다. 
  그래서 이번 프로젝트 진행에 있어, 각 프로젝트별로 하나의 AWS Network Topology가 나오기 때문에 위의 구성과 같이 전체적으로 구성을 해보려고 한다.





