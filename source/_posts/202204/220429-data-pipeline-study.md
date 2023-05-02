---
title: 220429/30 데이터 파이프라인 스터디 18/19일차(Presto와 Tableau 연동해서 Dashboard 구성)
date: 2022-04-29 17:14:00
tags:
  - BigData
  - Data-Pipeline
  - RDS
  - Tableau
  - Dashboard
  - Visualization
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220429_tableau.png" alt="Tableau">
</div>

이번 포스팅에서는 Presto와 Athena에 이어서 Tableau에 대해서 정리를 해보려고 한다.

최근 학습한 내용에 있어서, 데이터 전처리 및 시각화하는데 사용되는 서비스들이 많이 등장하였는데, 가장 최근에 배웠던 Presto와 Athena, 그리고 Tableau 이 세 개의 기술에 대해서 다시 한 번 전체적으로 개념을 잡고 Tableau를 실습한 내용을 정리하려고 한다.

## <ins><b>Presto / Athena / Tableau</b></ins>

우선 Presto -> Athena -> Tableau 순으로 살펴보면,

`Presto`는 짧은 시간의 임시 데이터 분석에 최적화된 Open source로, `분산 SQL query engine`으로, `이종 데이터간의 JOIN을 지원`한다.

`Athena`는 Presto와 같이 `SQL query를 실행`시킬 수 있고, `Serverless`이다.
단, Presto와는 다르게 `RDS 데이터와 JOIN(이종 데이터간의 JOIN)은 지원하지 않는다.`하지만, 분석할때에는 Presto가 좋다고 하더라도 항상 구동시켜놓기에는 Presto는 부담이 있기 때문에 Serverless인 Athena를 Tableau와 연동해서 시각화를 하는 것도 좋다.

자 앞서 살펴보았던 Presto와 Athena는 둘 다 SQL Query engine으로, 데이터를 SQL Query로 분석할 수 있다는 공통점이 있었지만, 이종 데이터간의 JOIN을 할 수 있기도(`Presto`) 없기도한(Athena) 차이가 있었다.

## <ins><b>Tableau</b></ins>

그럼 Tableau는 무엇인가? Tableau는 BI(Business intelligence) 툴로, 뭔가 어려워 보이는 툴이지만, `사람들이 데이터를 보고 이해할수 있도록 돕는다.`는 경영철학을 가지고 만들어진 소프트웨어이다.
이 BI 소프트웨어는 3명의 스탠포드 대학 출신의 Christian, Chris, Pat이 설립한 Tableau 기업에서 만든 소프트웨어로, Pat과 Chris가 데이터 베이스에 저장되어있는 데이터의 이해를 돕고자 추친된 프로젝트에 참여하여 당시 가장 상용화된 BI 소프트웨어를 사용하였는데, 박사출신인 본인들이 사용하기에도 너무 어렵게 느껴져서 이 부분에 문제점을 자각하게 되면서 만든것이 바로 이 Tableau라는 BI 툴이다.

당시 후발주자로 BI 업계에 들어왔지만, 빠른 속도로 시장을 장악하였으며, 현재 BI 소프트웨어 업계에서 부동의 1위를 차지하고 있다.
`BI(Business Intelligence) : 책, 저널, 문서, 이미지, 메일, 파일, 기타 비즈니스 소스를 포함한 내/외부 시스템에서 많은 양의 비정형 데이터를 수집하고 처리하는 어플리케이션 소프트웨어 형식이다. 주로 쿼리를 통해 정보를 찾기 위해 데이터를 수집하는 방법을 제공한다. 대시보드 및 데이터 시각화를 만들 수 있도록 분석할 데이터를 준비하는데 도움이 된다.`

자 이렇게 Presto와 Athena는 데이터 분석툴이고, Tableau는 BI 툴이라는 정리가 되었다.
이제 본격적으로 Presto와 Tableau를 연동해서 Dashboard를 구성해보는 실습을 해보자.

<!-- more -->

## <ins><b>Tableau</b></ins>

1. Tableau Desktop 14일 평가판을 다운받는다.

2. Tableau driver를 다운받아서 설치한다. 이하 Tableau Desktop에서 AWS Aurora MySQL Connector를 사용하기 위해 필요한 driver를 설치한다.

   https://www.tableau.com/support/drivers

3. Tableau Desktop을 다시 열고 AWS Aurora MySQL Connector로 Database resource를 import한다.

4. Tableau의 연결방식에는 Live와 Extract 방식이 있는데 아래와 같은 차이가 있다.

- Live 방식 : 그때 그때 SQL query를 날려서 데이터를 가져와서 처리한다. `(DBMS/Presto에 부하를 줄 수 있다)`

- Extract 방식 : DBMS의 데이터를 File로 내리고, 내려받은 File의 데이터를 기반으로 시각화(Visualization)을 한다. `(Tableau 전용 서버를 별도로 운영한다면, 부하를 줄 수 있다)`

- Tableau는 Drag&Drop 방식으로 손쉽게 UI상에서 시각화가 가능하다.

(`2022/04/30 업데이트`)

## <ins><b>Tableau와 Presto연동하기</b></ins>

Tableau내에서 Presto driver를 다운받아서 분산형 클러스터 형태로 SQL 쿼리로 데이터 처리를 빠르게 할 수 있는 Presto를 Tableau 내에서 함께 사용해보도록 할 것이다.

**[STEP1] JDBC DRIVER 다운받아서 Tableau와 연동**

https://prestodb.io/download.html

다운받은 presto \*.jar 파일은 /Library/Tableau/Drivers 하위에 옮겨준다.(Presto와 Tableau 연동)

**[STEP2] Tableau에서 Presto connector를 사용해서 접속**

아래와같이 EMR의 Presto cluster의 master end point 주소, port, catalog, schema, Authentication method, Username을 입력한다.

<div align="center">
  <img src="/images/post_images/220430_tableau_presto_connector.png" alt="Tableau-Presto 연결">
</div>

`별도의 Password 입력 설정이 없기 때문에 8889번 Port에 대한 Inbound rule에서 보안설정을 strict하게 해준다.`

`Tip)`
처음 연결되면 아무것도 보이지 않는데, 스키마 드롭다운 리스트를 클릭하면, 돋보기 모양 아이콘이 보이는데, 아이콘을 클릭하면 schema리스트가 나오고, shema를 선택한 다음에 테이블에서도 돋보기 모양 아이콘을 클릭하면, 세부 데이터 테이블 리스트가 출력된다.

Silver 데이터 테이블을 우측의 workspace에 Drag&Drop을 해주게 되면, 작성했던 silver 데이터 테이블 정보를 볼 수 있다.

<div align="center">
  <img src="/images/post_images/220430_sample_data_analysis.png" alt="Tableau-Presto 연결">
</div>

`(sample data 해석)`
언제 - 어떤 사용자(id)가 - 어떤 단지의 id를 클릭했는지에 대한 정보를 확인할 수 있다.

위의 hive catalog정보를 mysql cataqlog정보의 테이블과 Join해서 새로운 데이터로써 사용할 수 있다.
`(JOIN시, 기준 column 선택)`

**[지도(맵)에 데이터 시각화하기]**

이 부분은 다음에 공공기관의 데이터를 활용하여 한 번 실습을 해보자.
데이터 테이블에서 시/군/구에 해당되는 데이터가 있다면, 해당 칼럼 데이터 항목을 우측 클릭해서 지리적 역할 - 시(도시) 혹은 시군구를 클릭하여 맵핑해주고, 해당 칼럼 데이터 항목을 새로운 시트에서 worksheet에 Drag&Drop해주면 지도상에서 데이터의 분포를 표시해준다.

**[Gold data/Silver data와 BI 툴의 조합]**
일반적으로 GOLD 데이터를 BI tool과 1:1로 mapping 시켜서 데이터를 시각화시키는 방법도 있으나,

여러가지 시각으로 분석을 해야되는 경우,
SILVER 데이터를 BI tool + Presto(빠른 엔진)과 조합을 하여, 다각도로 SILVER 데이터를 시각화해서 분석할 수 있다.

## <ins><b>Tableau와 Athena 연동하기</b></ins>

Athena는 serverless 데이터 분석 툴로, 데이터는 data catalog의 metadata만을 사용(S3의 Glue기반의 metadata)한다. 사용한 데이터 용량을 기준으로 charging되기 때문에 상황에 따라 적절하게 사용되어야 한다.

Athena에서는 RDS의 master성 데이터를 import해서 사용할 수 없기 때문에, 임의로 data catalog에 master성 데이터 테이블을 넣어서 JOIN을 하는 형태로 데이터를 조작해서 전처리해야 한다.

https://docs.aws.amazon.com/ko_kr/athena/latest/ug/connect-with-previous-jdbc.html

`(JDK 7.0호환 드라이버 다운받기)`

Athena도 Presto와 동일하게 다운받은 jar 파일을 /Library/Tableau/Drivers 하위에 옮겨준다. `(Tableau와 Athena 연동)`

### **Amaqzon Athena 서비스에서 쿼리 결과 위치 경로 설정하기**

Amazon Athena -> 쿼리 편집기 -> 설정에서 해당 경로를 추가해줄 수 있다.

### **Tableau에서 Amazon Athena Connector 설정**

(1) server: ena.ap-northeast-2.amazonaws.com

(2) port: 443

(3) S3 Staging directory: [Amazon Athena의 쿼리결과 위치 S3 PATH]

(4) Access key ID: `Access Key ID의 경우에는 다른 계정의 Access Key를 입력할 경우, 해킹의 문제가 될 수 있기 때문에, 반드시 Athena 사용에 대한 권한만 부여받은 별도의 사용자 계정을 만들어서 해당 사용자의 Access Key ID를 입력하도록 한다.`

사용자 권한 추가시에 Athena 사용에 대한 권한 이외에도 AmazonS3FullAccess 권한을 함께 부여해줘야 한다.(`S3 Staging directory read/write`)

(5) Secret Access Key: (Optional)
