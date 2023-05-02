---
title: 220614 Terraform study
date: 2022-06-14 21:09:00
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

이번 포스팅에서는 Terraform에 대해 공부한 내용을 정리해두려고 한다. 지금 Docker나 Kubernetes도 데이터 파이프라인 구축시 별도의 서버를 컨테이너로 띄우고 관리하기 위해서 공부를 하고 있지만, 결국에는 반복을 통해서 익숙해지는 것이 최고이기 때문에 Terraform도 빠르게 개념을 정리하고 어떻게 AWS 인프라를 코드로 작성해서 간편하게 관리할 수 있는지에 주안점을 두고 학습을 해보려고 한다.
Terraform을 학습하게 된 계기는 우선 AWS의 서비스로 데이터 파이프라인을 구축할때 웹 페이지나 AWS CLI 상에서 계속 반복적인 작업을 통해 각 컴포넌트를 생성하는데 있어, 비효율적인 것 같다는 생각에서 비롯되었다.
그러던 중 현재 오프라인으로 DE 현직자에게 AWS 클라우드 환경에서 데이터 파이프라인 구축하는 방법에 대해서 수업을 듣고 있는데, Terraform과 같은 IaC(Infrastructure as Code)로 관리를 하게 되면, 좀 더 수월하게 데이터 파이프라인의 AWS 인프라를 빠르게 구축할 수 있다고 해서 개인적으로 공부를 시작하게 되었다.    

## <ins><b>Terraform ? </b></ins>

  Terraform은 IaC로, 인프라를 코드로 정의함으로써, 손쉽게 인프라 전반을 관리할 수 있도록 해주는 툴이다. 

## <ins><b>Homebrew를 사용해서 Terraform 설치</b></ins>

  package management tool인 homebrew를 사용해서 Terraform을 설치할 수 있다. 

```zsh
$brew install terraform
$terraform -v # Terraform v1.2.2
```

<!-- more -->

## <ins><b>Terraform SETUP in VSCode</b></ins>

  Terraform을 만든 HashiCorp의 VSCode - Terraform extension을 설치한다.

  - 자동완성, 자동 포맷팅 등의 다양한 기능들을 지원한다.

## <ins><b>Terraform 실습</b></ins>

  Terraform 파일은 HashiCorp configuration language로 작성이 되며, 파일 확장자는 *.tf이다.

  Terraform은 다양한 provider를 제공한다. 따라서 terraform과 연동하고자하는 provider의 plug-in을 다운받아서 설치해야한다.
  플러그인 다운은 Terraform configuration 파일에서 선언해서 필요한 provider의 플러그인을 다운받도록 할 수 있다.

  아래 링크는 내가 데이터 파이프라인을 구축할때 AWS 클라우드 플랫폼을 사용할 것이기 때문에 AWS Provider에 대한 Terraform 공식 사이트 documentation이다.

  https://registry.terraform.io/providers/hashicorp/aws/latest/docs

  나는 현재 `Terraform v1.2.2`를 사용하고 있기 때문에 Terraform 0.13 이상 버전과 호환되는 공식문서의 내용을 참고한다.

  `main.tf`

  ```conf
  terraform {
    required_providers {
      aws = {
        source  = "hashicorp/aws"
        version = "~> 3.0"
      }
    }
  }

  # Configure the AWS Provider
  # Seoul - (ap-northeast-2)
  provider "aws" {
    region = "ap-northeast-2"
  }

  # 권장되지 않는 방식
  provider "aws" {
    region = "ap-northeast-2"
    access_key = "[access_key]"
    secret_key = "[secret_key]"
  }

  # 예시) Create a VPC
  resource "aws_vpc" "example" {
    cidr_block = "10.0.0.0/16"
  }
  ```

  - ### <ins><b>Authentication & Configuration</b></ins>

    공식 사이트에 따르면, Provider configuratiopn에서 Hard-coded Credentials 방식은 Terraform configuration에서 권장되지 않는다고 한다.
    `(만약 AWS의 access_key나 secret_key에 대한 정보를 provider "aws"에 함께 기입을 했을 경우에는 반드시 VCS(Version Control System)에 업로드되지 않도록 주의해햐 한다.)`
    CodeBuild나 ECS를 사용하고 있고, IAM Task Role에 대해 정의를 하고 있다면, Terraform에서 해당 컨테이너의 Task Role을 활용할 수 있다고 한다. 또는 `Users/사용자명/.aws` 하위에 conf와 credential 파일이 있는데, 해당 path를 적용해서 하는 방법도 있다.
    그런데 적용을 해봤는데, 적절하지 않는 argument라고 나와서 이 부분도 다시 document를 보고 해봐야겠다.

## <ins><b>실습1) Terraform으로 AWS EC2 인스턴스 생성하기</b></ins>
 
  EC2 인스턴스를 생성하기 위해서 resource 뒤에 생성할 resource 이름을 적게 되는데, 항상 위에 첨부한 공식 사이트에서 검색을 통해서 참고하도록 한다.

  `ec2_instance.tf`
  ```tf
  terraform {
  required_providers {
      aws = {
        source  = "hashicorp/aws"
        version = "~> 3.0"
      }
    }
  } 

  # Configure the AWS Provider
  # Seoul - ap-northeast-2
  provider "aws" {
    region = "ap-northeast-2"
    access_key = "[access_key]"
    secret_key = "[secret_key]"
  }

  # [참고] provider의 resource 생성하기
  resource "<provider>_<resource_type>" "name" {
    config options......
    key = value"
    key2 = "another value"
  }

  resource "aws_instance" "my-first-terraform-server" {
    # ami의 id는 EC2 인스턴스를 생성할때 ami-* 로 시작하는 부분을 복사해서 넣어주면 된다.
    ami           = "ami-058165de3b7202099"
    instance_type = "t3.micro"
  }
  ```
  우선 위에서 작성한 *.tf 파일을 실행하기 전에 가장 먼저 `$terraform init`을 해줘야 한다.
  terraform init 명령을 통해 현재 위치한 디렉토리 내의 *.tf파일의 설정을 스캔하고, *.tf 파일의 내용에서 정의한 provider에 대한 내용 또한 스캔한다.
  이를 통해 정의한 aws과 관련되서 필요한 플러그인을 aws api와의 상호작용을 통해 다운로드받는다.

  ```zsh
  $terraform init
  # Initializing provider plugins...
  # - Finding hashicorp/aws versions matching "~> 3.0"...
  # - Installing hashicorp/aws v3.75.2...
  # - Installed hashicorp/aws v3.75.2 (signed by HashiCorp)
  ```

  그 다음으로 살펴 볼 명령은 `$terraform plan`인데, 이 명령은 선택사항인데, 내가 code로 작성한 infrastructure를 실제로 적용하기 전에 변경사항을 체크해볼 수 있는 명령이다.
  terraform plan 명령을 하기 전에 terraform init 명령이 선행되어야 한다. 초록색으로 표기(+)되는 리소스는 새로 추가되는 리소스이며, 빨간색으로 표기(-)되는 리소스는 제거된 리소스, 주황색으로 표기(~)되는 리소스는 수정된 리소스로 표기된다.

  ```zsh
  $terraform plan
  ``` 

  이제 최종적으로 진행될 작업을 확인한 뒤에는 `$terraform apply`명령을 통해 실행을 시킨다.

  ```zsh
  $terraform apply --auto-approve
  ```

  최종적으로 AWS EC2 Instance Dashboard를 통해서 인스턴스가 생성된 것을 확인할 수 있었다. 인스턴스 세부 옵션을 넣는 부분도 확인해봐야겠다.
  만약 작성한 *.tf파일을 수정없이 다시 terraform apply를 하게 되면, 실제 AWS Infrastructure와 적용하고자 하는 *.tf파일의 내용을 비교해서 변화가 없으면 실행을 하지 않는다.

  이렇게 간단하게 코드로 작성해서 EC2 인스턴스를 생성하니, AWS 웹 페이지에서 번거롭게 마우스로 클릭하는 일을 하지 않아도 되서 좋은 것 같다.






