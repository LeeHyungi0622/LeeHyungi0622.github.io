---
title: 220618 Terraform study
date: 2022-06-18 13:40:00
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

이번 포스팅에서는 Terraform에서 생성한 AWS Resource를 수정하는 부분에 대한 내용부터 시작해보려고 한다.
Terraform은 손쉽게 AWS Infrastructure를 구성할 수 있도록 도와줌과 동시에 코드를 통해 전체적인 AWS Infrastructure의 구조를 파악할 수 있다.

## <ins><b>Terraform의 동작 방식</b></ins>

  만약 실행하고자 하는 *.tf 파일이 이전에 실행을 한 다음에 별도의 수정을 하지 않았다면, `$terraform apply`명령을 실행하여도 아무런 변화가 일어나지 않는다. 그 이유는 terraform은 기본적으로 `$terraform plan`명령을 통해 확인할 수 있는 변화된 내용이 있어야 `$terraform apply`명령을 통해 새롭게 인프라를 구축할 수 있다.

  만약 기존 리소스에서 tag에 대한 정보만 추가한 다음에 다시 실행하면, 기존에 생성되었던 리소스는 유지된 상태에서 resource name (Name)이 추가가 된다.

## <ins><b>Delete resources</b></ins>

  기존에 *.tf파일을 통해 생성하였던 리소스들을 삭제하려면, `$terraform destroy` 명령을 치면 된다. 

  ```zsh
  $terraform destroy
  ```
  만약 기졵의 .tf 코드에서 일부 리소스를 주석처리하게 되면, apply를 했을때 주석처리된 리소스를 제거한다. (`일부 리소스 제거`)


<!-- more -->

## <ins><b>Reference resources</b></ins>

  우선 실습을 위해 VPC를 구성하고, VPC 내에 별도의 서브넷을 구성하도록 한다. VPC는 Virtual Private Cloud의 약어로 격리된 Private Network 영역이다. AWS 사용자가 정의한 가상 네트워크로써 cidr 블록 방식으로 IP대역대를 설정한다. VPC의 대표 구성요소는 아래와 같다.

  `ref.` *.tf 코드에서 선언 순서는 문제가 되지 않는다. 그 이유는 Terraform이 Subnet이 VPC에 속해있다는 것을 파악하고 코드를 실행하기 때문에 순서가 뒤바뀌어도 VPC를 우선 생성하고, Subnet을 생성한다.
  
  - ### **VPC 구성**

    <div align="center">
      <img src="/images/post_images/220618_vpc_subnet_structure.jpg" alt="VPC & Subnet">
    </div>

    - ### **Internet Gateway(IGW)**
      vpc 내부의 public subnet 상의 인스턴스들과 외부 인터넷 간의 통신을 위해 vpc에 연결하는 게이트웨이이다. 

    - ### **NAT Gateway(NGW)**
      nat는 네트워크 주소를 변환하는 장치로, ngw는 vpc 내부의 private subnet의 인스턴스들과 인터넷/AWS 서비스에 연결하는 게이트웨이이다. 실제 `구성은 public subnet에 위치하며, Elastic IP를 할당한 상태로 구성`된다.
      고정 할당된 Elastic IP를 route tableㅇ르 통해 연결시킨다.
      여기서 말하는 Routing Table이란 네트워크 트래픽을 전달할 위치를 결정하는데 사용되는 라우팅 규칙들의 집합이다.
      (`subnet - subnet`, `subnet - gateway`간의 통신을 결정한다) 
    
    - ### **Security Gruop**
      Network ACL과 함께 VPC의 보안 장치 중 하나이며, Security Group은 인스턴스 단위의 보안계층이다. 인스턴스에 대한 인바운드/아웃바운드 트래픽을 제어하는 가상 방화벽 역할을 한다.
      Network ACL과 달리 stateful하다.

    - ### **Network ACL**

      security group과 함께 VPC의 보안 장치 중의 하나로, 1개 이상의 subnet과 외부 트래픽을 제어할 수 있다. 즉, subnet 단위의 보안 계층이며, stateless라는 특징을 가진다.

  - ### **[aws_vpc] 구성**
    
    https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc

    ```tf
    # VPC 내에 Subnet을 구성한다.
    # VPC(Virtual Private Cloud) - Private Isolated Network
    # first-vpc는 지정한 리소스의 이름으로, 해당 리소스를 참조할때 사용된다. (terraform내에서 참조)

    resource "aws_vpc" "first-vpc" {
      cidr_block = "10.0.0.0/16"
      tags = {
        Name = "production"
      }
    }
    ```

  - ### **[aws_subnet]**
    https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet

    ```tf
    # Subnet 생성
    resource "aws_subnet" "subnet-1" {
      # reference the vpc created 
      vpc_id     = aws_vpc.first-vpc.id
      cidr_block = "10.0.1.0/24"

      tags = {
        Name = "prod-subnet"
      }
    }
    ```

  최종적으로 AWS에서 VPC - VPC dashboard를 살펴보면, default VPC와 production VPC 가 생성된 것을 확인할 수 있다.
  그리고 Virtual private cloud의 하위에 Subnets 메뉴를 통해서 VPC와 함께 새로 생성된 prod-subnet도 생성된 것을 확인할 수 있다.

## <ins><b>Terraform project files</b></ins>

  - **.terraform/plugins/...**
  
    .terraform/plugins/... 하위에는 `terraform init`명령을 했을때 작성한 *.tf 파일을 읽고, 설치가 필요한 platform의 plugin을 다운받는다. 다운받음과 동시에 해당 configuration 정보가 하위 폴더에 저장한다.

  -  **terraform.tfstate**(`매우 중요한 파일)`

    모든 Terraform의 상태정보를 담고 있는 파일이다. 우리가 *.tf 파일에 작성했던 AWS의 리소스의 정보 일부를 수정하게 되면, Terraform이 이를 감지해서 apply시 변경사항을 반영해주는데, 이러한 상태정보를 저장해서 담고 있는 파일이 바로 terraform.tfstate 파일이다.  
    *.tf 파일의 내용을 수정하고, tfstate 파일의 내용을 보면, 수정된 내용이 반영된 것을 확인할 수 있다.

## <ins><b>Practice Project</b></ins>
  
  여지까지 배운 내용을 기반으로 간단한 연습 프로젝트를 진행해본다.
  내용은 EC2 인스턴스를 Custom Subnet 영역에 Deploy하고, Public IP를 할당해서 SSH 연결뿐만 아니라 웹 서버를 구동시킬 수 있도록 구성해본다.
  <div align="center">
    <img src="/images/post_images/220618_practice_project_network_topology.jpg" alt="Practice Project AWS Network Topology">
  </div>

  - ### **[STEP1] VPC 생성하기**
    이름이 prod-vpc인 VPC를 생성한다.

    ```tf
    resource "aws_vpc" "prod-vpc" {
      cidr_block = "10.0.0.0/16"
      tags = {
        Name = "production"
      }
    }
    ```
    CIDR(Classless Inter-Domain Routing)은 클래스가 없는 도메인간 라우팅하는 기법이다.
    클래스가 없다는 뜻은 네트워크 구분을 별도의 클래스로 하지 않는 다는 의미로, Class는 사이더가 나오기전 사용했던 네트워크 구분 체계이다. 사이더가 나오면서 Class 체계보다 더 유연하게 IP 주소를 여러 네트워크 영역으로 나눌 수 있게 되었다.
    
    CIDR는 위의 Infra-Domain과 같이 각 네트워크 대역을 구분 짓고, Inter-Domain과 같이 구분된 네트워크간 통신을 위한 주소 체계라고 이해하면 된다. 
  - ### **[STEP2] Internal Gateway 생성하기**
    traffic을 인터넷 밖(outbound)으로 보내고, public ip를 서버에 할당하기 위함이다. 

    `ref.`
    https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/internet_gateway

    ```tf
    # IGW(Internet Gateway) 생성
    # VPC
    resource "aws_internet_gateway" "gw" {
      vpc_id = aws_vpc.prod-vpc.id
    }
    ```
  - ### **[STEP3] Custom Route Table 생성하기**
    VPC 내부에서 외부로 Routing 시켜줄때 참조할 Route table을 생성한다.

    https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table

    ```tf
    # Route table 생성
    resource "aws_route_table" "prod-route-table" {
      # Route table이 적용되는 vpc영역에 대한 지정
      vpc_id = aws_vpc.prod-vpc.id

      # IPv4에 대한 Route table setup
      route {
        # cidr_block address를 IGW로 보내고, 
        cidr_block = "0.0.0.0/0" 
        # 0.0.0.0/0 send all traffic before ip will route to IGW. 
        # 모든 트래픽이 aws IGW로 전송되기 때문에 gaqteway_id에 대한 설정이 필요하다.
        gateway_id = aws_internet_gateway.gw.id
      }

      # IPv6에 대한 Route table setup
      route {
        # 실제로는 IPv6를 사용하지 않지만 All traffic이 동일한 IGW로 routing되도록 설정한다.
        ipv6_cidr_block        = "::/0"
        gateway_id = aws_internet_gateway.gw.id
      }

      tags = {
        Name = "Prod"
      }
    }
    ```

  - ### **[STEP4] Subnet 생성하기**
    Subnet을 생성할때 Subnet이 속한 vpc에 대한 id 지정을 해줘야 한다. 그리고 cidr_block으로 네트워크 영역을 구분하고, AZ에 대한 설정과 tag 정보를 넣어준다.
    ```tf
    # Subnet 생성
    # AZ(Data centre에 대한 setup)
    # 생성한 Subnet을 상단에서 설정한 Route table에 넣어준다.
    resource "aws_subnet" "subnet-1" {
      vpc_id = aws_vpc.prod-vpc.id
      cidr_block = "10.0.1.0/24"
      availability_zone = "ap-northeast-2a"
      tags = {
        Name = "prod-subnet"
      }
    }
    ```
  - ### **[STEP5] Route Table에 Subnet 연관시키기**

    https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table_association

    ```tf
    # Associate subnet with route table
    resource "aws_route_table_association" "a" {
    subnet_id      = aws_subnet.subnet-1.id
    route_table_id = aws_route_table.prod-route-table.id
    }
    ```
    
  - ### **[STEP6] Security group 생성(22, 80, 443 포트 허용)하기**

    일반적으로 네트워크 트래픽은 Ingress와 egress으로 구분된다. Ingress는 외부로부터 서버 내부로 유입되는 네트워크 트래픽(Inbound), egress는 서버 내부에서 외부로 나가는 트래픽(Outbound)를 말한다.
    여기서 Ingress와 egress는 컨테이너 환경에서 과거 네트워크에서 사용되었던 정의가 포함이 되면서 데이터 경로와 컨테이너 환경에서 Ingress/egress라는 용어가 사용되고 있다. 

    Ingress :  클러스터 내 서비스에 대한 외부에서의 접근(일반적으로 HTTP)를 관리하는 API 객체로, 로드 밸런싱, SSL 종료, 이름 기반 가상 호스팅 을 제공할 수 있다.



  https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group

    ```tf
    # create security group 
    resource "aws_security_group" "allow_web" {
      name        = "allow_web_traffic"
      description = "Allow Web traffic"
      vpc_id      = aws_vpc.prod-vpc.id

      # Port의 range setup
      ingress {
        description      = "HTTPS"
        from_port        = 443
        to_port          = 443
        protocol         = "tcp"
        cidr_blocks      = ["0.0.0.0/0"]
      }
      ingress {
        description      = "HTTP"
        from_port        = 80
        to_port          = 80
        protocol         = "tcp"
        cidr_blocks      = ["0.0.0.0/0"]
      }
      ingress {
        description      = "SSH"
        from_port        = 22
        to_port          = 22
        protocol         = "tcp"
        cidr_blocks      = ["0.0.0.0/0"]
      }
      # egrass policy 
      # protocol: -1 (any protocol)
      egress {
        from_port        = 0
        to_port          = 0
        protocol         = "-1"
        cidr_blocks      = ["0.0.0.0/0"]
      }

      tags = {
        Name = "allow_web"
      }
    }

    ```

  - ### **[STEP7] STEP4에서 생성한 Subnet의 IP 주소로 Network Interface 생성하기**
  Network Interface의 정의에서 attachement section은 나중에 EC2 Instance를 provisioning할때 대체해서 처리할 것이다. 

  https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/network_interface

  ```tf
  # Internet interface
  # subnet 내의 ip를 private_ips의 ip로 지정한다.(any)
  # 이제 s
  resource "aws_network_interface" "web-server-nic" {
    subnet_id       = aws_subnet.subnet-1.id
    private_ips     =  ["10.0.1.50"]
    security_groups = [aws_security_group.allow_web.id]

  # EC2 Instance의 provisioning section에서 대체해서 처리한다.
  #   attachment {
  #     instance     = aws_instance.test.id
  #     device_index = 1
  #   }
  }
  ```
  - ### **[STEP8] STEP7에서 생성된 Network Interface에 Elastic IP 할당하기**

  https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip

  ```tf
  # 위에서 생성한 NI에 EIP적용를 적용한다.
  resource "aws_eip" "one" {
    vpc                       = true
    network_interface         = aws_network_interface.web-server-nic.id
    associate_with_private_ip = "10.0.1.50"
  }
  ```

  AWS EIP는 IGW의 deployment에 의존적이다. 따라서 실제 EIP를 deploy하기 위해서는 IGW가 우선적으로 deploy되어야한다. (`선 IGW deploy, 후 EIP deploy`)

  공식 문서를 보면, EIP는 IGW가 associate하기 전에 존재해야된다고 명기하고 있다. 이를 위해 EIP의 `depends_on` 속성을 사용하여, 명시적으로 IGW에 대한 의존성을 명시해줘야 한다. (`전체 객체에 대한 참조이므로 의존성에 대해 명시할때 특정 id가 아닌 객체로써 명시해준다`)
  

  - ### **[STEP9] Ubuntu server 생성 및 Apache2 설치 및 Enable하기**

  `생성되는 EC2 Instance의 AZ와 Instance가 속해있는 Subnet의 AZ는 반드시 일치`시켜줘야 한다. AZ가 같다는 것은 같은 Data Centre라는 것을 의미하기 때문에 반드시 AZ은 일치시켜서 관리하도록 한다.

  모든 EC2 인스턴스를 배포하기 위해서는 NIC에 대한 정의가 필요하다. 앞서 NIC에서 attachement section에 대해 생략한 부분을 Instance를 정의할때 network_interface에 대한 block 부분에 정의하도록 한다.

  ```tf
  # Server
  # device에 접근하기 위해서 key-pair에 대한 이름을 정의해줘야 한다.
  resource "aws_instance" "web-server-instance" {
    ami = "ami-058165de3b7202099"
    instance_type = "t2.micro"
    availability_zone = "ap-northeast-2a"
    key_name = "class-hglee-seoul"
    network_interface {
      device_index = 0
      network_interface_id = aws_network_interface.web-server-nic.id
    }
    user_data = <<-EOF
                #!/bin/bash
                sudo apt update -y
                sudo apt install apache2 -y
                sudo systemctl start apache2
                sudo bash -c 'echo your very first web server > /var/www/html/index.html' 
                EOF
    tags = {
      Name = "web-server"
    }
  }
  ```