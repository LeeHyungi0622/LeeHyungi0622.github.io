---
title: 220418 데이터 파이프라인 스터디 8일차
date: 2022-04-18 16:43:00
tags:
  - BigData
  - Data-Pipeline
categories:
  - Data-Pipeline
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/220408_data-pipeline.webp" alt="데이터 파이프라인(Data Pipeline)">
</div>

이번 포스팅에서는 앞으로 하게 될 실습의 전체 구성도를 정리하고, Amazon API Gateway를 구성했던 내용에 대해서 정리해본다. (앞으로 API Gateway를 설정할때 remind를 하기 위해 참고하도록 하자)

## <ins><b>두 번째 데이터 수집 파이프라인 구축 실습 구성도</b></ins>

<div align="center">
  <img src="/images/post_images/220418_pipeline_practice_2.png" alt="두 번째 데이터 파이프라인 실습구성도">
</div>

이번 실습에서는 EC2(외부 데이터 발생 source 부분)를 하나 준비해서 데이터를 발생시키고, 발생시킨 데이터를 API Gateway를 통해서 Amazon Kinesis Stream으로 넘겨주고, Amazon Kinesis Firehose에서 Stream의 데이터를 받아서 Amazon S3의 특정 폴더에 최종적으로 저장을 해주는 flow를 구성한다.

`(Mac/Window환경에서는 curl명령을 사용해서 외부에서 데이터를 발생시킬 수 있다)`

## <ins><b>API Gateway 구성하기</b></ins>

시스템을 구성할때에는 버전관리가 필요하다. 나중에 버전관리에 따른 업그레이드된 버전을 적용해야되는 경우가 생기기 때문이다.
**새로운 API를 생성한 다음에**
리소스 - [작업] - [리소스 생성] - [새 하위 리소스] - 리소스 이름 : v1 `(버전 이름 지정)`

### **[API 메소드 생성]**

외부에서는 오는 이벤트를 받을때에는 POST 방식으로 받는다. 기본적으로 들어온 데이터는 Lambda로 처리하기 때문에 Lambda가 default로 선택이 되어있다. 하지만, 이번 실습에서는 Kinesis Stream에 연결을 해줄 것이기 때문에 `AWS 서비스`를 선택해준다. `(통합 유형)`

- `AWS 리전` : ap-northeast-2
- `AWS 서비스` : Kinesis
- `HTTP 메서드` : 내부 서비스간에 통신하는 부분 정의(POST)
- `작업` : API Gateway에서 Kinesis로 넘길때 약속된 작업 명령어 (대소문자 구분) `PutRecord`
- `실행역할` : IAM `(AWS안에서 실행되는 서비스들도 role을 가지고 있기 때문에 권한 없이 다른 서비스에 명령어를 날리거나, 실행을 요청할 수 없다. 따라서 항상 role을 만들어서 서비스에 권한을 줘야 다른 서비스에 action을 할 수 있다)`

### **[역할 만들기]**

=> `IAM - [역할 만들기] - 역할 이름 : apigatewayToKinesis`
역할이 추가되면, 생성된 역할에서 `[정책 생성] - "정책 연결"을 선택`해서, `"AmazonKinesisFullAccess"를 선택` - `"정책 연결"` - `생성된 역할 페이지에서 ARN 주소를 복사`를 해준다.

<div align="center">
  <img src="/images/post_images/220418_api_gateway_policy_setting.png" alt="API Gateway에 IAM 정책 설정">
</div>

<div align="center">
  <img src="/images/post_images/220418_kinesis_post_method_creation.png" alt="Kinesis Post method 생성">
</div>

**통합요청 부분**이 메시지를 처리하는 부분이고, Kinesis에서 정상처리되면, 200 Response code를 반환해준다.

### **[통합요청 부분 추가 처리]**

통합 요청부분은 `메시지가 왔을 때 어떻게 처리할지 정의하는 부분`으로 추가적인 작업이 필요하다.

**(1)** Type은 JSON으로 정의하도록 한다. `HTTP 헤더에 아래 정보를 추가`해준다.

```
이름 : Content-Type
Mapped from : 'application/x-amz-json-1.1'
```

**(2)** 매핑 템플릿에서 패스스루 방식에서 매핑 템플릿 추가로 `application/json`를 해준다.

**(3)** velocity programming language를 사용해서 템플릿을 생성해준다. 아래의 스크립트를 참고하도록 하자.

```
#set ( $enter = "
")
#set($json = "$input.json('$')$enter")
{
    "Data": "$util.base64Encode("$json")",
    "PartitionKey": "$input.params('X-Amzn-Trace-Id')",
    "StreamName": "class-stream"
}
```

`enter값을 넣는 이유는` 메시지 데이터값이 오면, queue에 담겼다가 오기 때문에 enter가 없으면 다음 row로 넘어가지 않기 때문에 넣어준다.
`한 줄 한 줄씩 받아온 메시지 데이터를 읽어서 Kinesis에 RESTFul 방식으로 넘겨주는 Script`이다.

Data 자체를 base64로 encoding한다. (이유는 우리가 흔히 쓰는 이메일도 base64로 encoding되어있다. web contents는 특수한 기호가 많기 때문에 base64로 encoding하지 않으면 전문이 갈때 특수문자에 의해서 원하지 않은 부분에서 짤리는 상황이 발생한다)

`Partitionkey는` Shard를 3개를 나눈다고 가정할 때 Kinesis가 Shard의 index 순서에 맞게 만들어주게 하기 위해서 정의한다.

`StreamName은` 어느 스트림으로 보낼지에 대한 이름을 정의한다.

**(4)** API 배포 작업
상단에 작업 드롭다운 메뉴에서 API 작업 - API 배포를 클릭해서 생성한 API 메서도를 배포해준다.

배포 스테이지 : [새 스테이지]
스테이지 이름 : PROD

`최종적으로 서비스에 대한 END POINT도 확인`할 수 있다.

**(5)** curl명령어를 사용해서 END POINT로 생성한 데이터를 API Gateway END POINT를 통해 Kinesis Stream으로 넘겨준다.

```
curl -d "{\"value\":\"30\",\"type\":\"Tip 3\"}" -H "Content-Type: application/json" -X POST https://xxxxxxxx.execute-api.ap-northeast-2.amazonaws.com/PROD/v1
```

**(6)** 서비스 모니터링
`서비스를 한 개 만들때마다 모니터링을 해줘야한다.` API Gateway와 같은 경우에는 로그가 CloudWatch에 떨어진다.
Api Gateway에 CloudWatch에 대한 설정을 해줘야한다.

[설정] - CloudWatch 로그 역할 ARN 입력 (IAM에서 CloudWatch 권한이 추가되어있는 정책 ARN 입력)

**(7)** 배포된 API 경우에는 스테이지 항목에 올라가있기 때문에 스테이지에서 API 항목을 클릭한 뒤에 CloudWatch 설정에서 `CloudWatch 로그 활성화`를 체크하고 INFO 수준으로 체크해준다.

현재는 실습단계이기 때문에 CloudWatch에 로그가 쌓으는 것만 확인하고 바로 서비스를 disable한다. (별도 비용발생)
