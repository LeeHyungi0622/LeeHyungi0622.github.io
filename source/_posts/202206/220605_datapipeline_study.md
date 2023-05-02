---
title: 220605 데이터 파이프라인 구축 오프라인 수업 / 2주차
date: 2022-06-05 13:43:00
tags:
  - AWS
  - Data-Pipeline
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220530_review.png" alt="Review">
</div>

<br/>
<br/>

이번 포스팅에서는 두 번째 데이터 파이프라인 구축 오프라인 수업시간에서 배운 내용을 정리하려고 한다.

## <ins><b>2주차 첫 번째 실습) logstash의 input/output의 file 플러그인 활용</b></ins>

가장 일반적으로 많이 쓰이는 방식으로, 실제 `VM에 서버를 띄우고 해당 로그를 취득하거나 도커나 쿠버네티스에 컨테이너에서 서버를 띄우고 로그를 취득하는 경우`에도 `logstash의 input의 file 플러그인을 사용해서 로그 데이터를 땡겨온다.`

VM이나 컨테이너의 특정 경로에 로그 데이터를 적재해주고, 시스템 자체로는 Log rotate라고 하는 방식으로 `오늘 날짜 기준으로 로그를 쌓다가 날짜가 바뀌면, 다른이름으로 압축을 하거나 삭제를 해서 새로운 로그를 다시 쌓는 방식`으로 한다. 이러한 작업에 logstash와 같은 agent를 통해서 밖으로 빼내는 작업을 하는데, 이때 input의 file 플러그인을 가장 많이 사용한다고 한다.

<!-- more -->

이전에 logstash를 사용해서 단순히 twitter의 로그 데이터만 뽑아서 kafka에 넘겨주는 작업을 했을때에는 logstash에 다양한 플러그인에 대해서 잘 몰랐고, logstash의 단편적인 기능만 알았었는데, 이번 실습을 통해서 logstash라는 agent의 input/output의 플러그인은 종류가 엄청 다양하고, 실무에서는 어떤식으로 이 logstash를 사용해서 서버로부터 로그 데이터를 땡겨서 적재하는지에 대해서 개괄적으로 알게 된 것 같다.

이번 실습에서는 외부에서 로그 데이터가 쌓이는 과정을 임의의 `"echo "1" >> input.file`명령을 통해서 새로운 input.file 로그 파일을 생성하고, 임의의 명령을 통해 로그 파일(input.file)에 데이터가 적재될때마다 logstash의 output의 file 플러그인의 path에 지정된 파일로 `새로운 형태의 데이터가 적재`되는 과정에 대해서 실습해보았다.

```zsh
$logstash -f logstash/config/logstash-config-file.conf

# logstash가 input의 file 플러그인을 사용해서 input.file 로그 파일을 listen한다.
input {
  file {
    path => "/home/ubuntu/input.file"
    start_position => "beginning"
  }
}

# 다양한 플러그인을 사용해서 로그 데이터를 S3에 직접 저장을 하거나 할 수 있다.
output {
  file {
    path => "/home/ubuntu/logstash-output-%{+YYYY_MM_dd_HH}.log"
    # 위에 지정한 새로운 로그파일에 "output: 1" 형태로 데이터가 전처리되어 쌓인다.
    codec => line { format => "output: %{message}"}
  }
}

# echo 명령을 통해서 input.file에 임의의 로그 파일을 쌓으면, 자동으로 output의 file플러그인에 지정한 파일에 정제된 데이터가 저장된다.

#input.file 내의 데이터가 업데이트되면, logstash에서는 output 파일에 지속적으로 업데이트를 한다.
#실제로는 어플리케이션으로부터 로그 데이터가 넘겨져서 떨궈지면, 해당 로그 데이터를 읽어서 처리하는데, 현재 실습에서는 임의로 데이터를 쌓기 위한 작업이 추가되었다.
$echo "1" >> input.file
$echo "2" >> input.file
$echo "3" >> input.file
```

## <ins><b>두 번째 실습) 실제 Apache sample log파일을 활용한 실습</b></ins>

week-1의 apache-sample-log 파일의 샘플 로그 데이터로 실습을 진행하였다.

grok 플러그인은 들어온 로그데이터를 parsing하는 formatter역할을 한다.

문자열 한 줄로 출력되던 로그 데이터를 json 형식으로 parsing 되는 것을 확인할 수 있었고, `Apache log의 경우에는 보편적으로 사용되기 때문에 grok으로 filter하면 자동으로 json 형식으로 parsing된다.`

```json
input {
    file {
        path => "/home/ubuntu/[log file]"
        start_position => "beginning"
        sincedb_path => "/dev/null"
    }
}

filter {
// 결과로 문자열로 출력된 데이터를 json 형태의 데이터로 변환해준다.
   grok {
     match => {"message" => "%{COMBINEDAPACHELOG}"}
   }
  //  grok으로 parsing된 json 형식의 데이터를 prune을 활용해서 특정 필드의 데이터만 추출한다.
  // 용량이 큰 경우나 분석가가 원하는 데이터만 추려서 제한된 데이터로 만들때 사용된다.
  // 반드시 위의 grok을 한 뒤에 prune으로 처리해야한다.
   prune {
     interpolate => true
    //  clientip와 timestamp field data만 추려서 로그 파일 출력
     whitelist_names => ["clientip", "@timestamp"]
   }

#   mutate {
#    remove_field => ["referrer", "path", "agent", "message"]
#   }
}

output {
  file {
    path => "/home/ubuntu/logstash-output-%{+YYYY_MM_dd_HH}.log"
    codec => rubydebug
  }
}
```

아래 메뉴얼에서 filter plugins을 확인하고 개인적으로 실습을 해보자.

[https://www.elastic.co/guide/en/logstash/current/filter-plugins.html](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)

유용한 플러그인으로는 IP주소를 CIDR 표기법(`ip/(서브넷 bit 수)`)로 변환해 줄 수 있는 `cidr`플러그인도있으며, `csv`플러그인을 사용해서 comma-separated value 데이터를 개별 필드로 분류해서 뽑아낼 수도 있고, `kv` 필터 플러그인을 사용해서 key-value 페어로 로그 데이터를 parsing 할 수도 있다. (`이 부분에 대해서는 차후에 개인 프로젝트에 적용해서 실습을 해보도록 하자`)

## <ins><b>세 번째 실습) Beats - Logstash 연동 실습</b></ins>

Beats는 Logstash의 앞 단에 경량화된 데이터 수집기를 심기 위한 목적에서 사용한다. (`input.file 로그 파일을 Beats가 읽어서 Logstash로 넘겨준다.`)

현재 실습으로 제공된 파일에서 절대경로로 filebeat가 실행이 되지 않기 때문에 우선적으로 filebreat.yml파일을 ec2 instance의 local의 filebeat 압축해제 폴더에 복사를 한 뒤에 상대경로로 해서 filebeat를 실행해서 실습하도록 한다.

```zsh
#다른 ec2 instance에서는 logstash를 실행한다.
$logstash -f ...../logstash-config-filebeat.conf

#우선 git repository의 week-1 폴더에 있는 filebeat.yml 파일을 local의 filebeat 폴더에 복사를 해둔다.
$./filebeat run -e filebeat.yml
```

logstash 로그에서 확인을 해보면, filebeat로부터 넘겨받은 로그데이터 목록을 순차적으로 받는 것을 확인할 수 있다.
(`filebeat가 listen하고 있는 input.file 로그 파일에 echo 명령을 사용해서 새로운 로그 데이터를 적재해서 변화를 줘야 filebeat에서 이를 감지하고, 변화된 로그 데이터를 읽어서 logstash로 넘겨준다`)

filebeat - logstash - [output plugin 사용해서 S3에 저장을 할 수도 있다] (`응용해서 실습해보기`)

filebeat - logstash 구조로 실행을 할 때, background에서 계속 실행이 될 수 있도록 할 수도 있고, 개별 node에 설치를 할 수도 있다.
logstash 전용 노드를 설치를 해서 사용할 수 있는 부분에 대해서도 직접 실습을 해보자.(`추가 자료 참고`)

<!-- 이 부분까지 정리 완료(1/3 지점) -->

이 부분부터는 2주차 본 수업에서 다룬 데이터 파이프라인의 이론적인 부분에 대한 정리이다.

## <ins><b>용어에 대한 설명</b></ins>

- ### **Data Lake**

  최근들어 클라우드의 도입으로, 대용량 분산 스토리지 개념이 많이 발달하였다. 따라서 정형과 비정형 데이터(사진, 동영상, json 파일(반정형)를 구분하지 않고 우선적으로 적재를 한 뒤에 필요에 따라 데이터를 추출해서 일부 데이터를 추출하고, 정제한다.
  (`정형화나 정규화가 되어있지 않은 데이터 스토리지`, `분산 스토리지(S3, HDFS 등을 사용)`)
  DL는 테이블이 아니며, 별도의 스키마가 없고 schema on read(스키마를 읽을 때 스키마가 결정)의 형태를 갖는다. 그리고 ELT(Extract Load Transform)의 중간 저장소 역할을 한다.
  ETL에서는 DB에 정제된 데이터를 DB에 로드할 때 각 각의 데이터의 스키마가 정해져야 문제없이 데이터가 적제될 수 있기 때문에 우선적으로 데이터가 정제되고, DB에 데이터를 쌓는다.

- ### **Data Warehouse**

  실제 의사결정을 위해서 소스 DB(사용자 정보, 거래정보)를 분석하고, 구조화된 형식으로 저장하는 것이 Data Warehouse이다. `(다양한 소스로부터 필요한 데이터를 수정/정제해서 집계하는 저장공간)`
  ETL 과정을 통해서 분석가능하고 구조화된 데이터로 저장하는 저장소
  대량의 데이터를 `장기 보관(3개월 ~ 2년)` (부서 및 기업별로 분류하여 데이터를 체계적으로 정리한다)
  원천 데이터를 가져와서 데이터를 테이블화하는 것을 말하며, `테이블화를 할 수 있다는 의미는 key:value 형태로 데이터가 정형화되어있다는 의미`이다.
  잦은 읽기 쓰기의 RDB를 쓰는 것이 아닌, `웨어하우스 솔루션이 많이 쓰인다.` (`일반적인 RDB와는 다른 구조를 갖지만, 때때로 작은 규모의 데이터에서는 RDB로 대체해서 사용하기도 한다`)
  보통 데이터 웨어하우스 솔루션은 같은 칼럼의 데이터만 묶어서 관리한다. (대량의 배치작업을 위한 처리)
  보통은 각 각의 칼럼 데이터가 하나의 행으로써 관리가 되는데, 웨어하우스 솔루션에서는 칼럼 데이터로 묶여서 관리된다.
  `ref.` **columnar** : 데이터 베이스의 각 칼럼 데이터를 별도로 분리해서 관리한다. 그 이유는 대량의 데이터 쓰기 작업이 발생하기 때문

- ### **Data Mart**

  데이터 웨어하우스가 중앙 집중식으로 여러 분야의 데이터를 가지고 있다면, 데이터마트는 특정 분야의 데이터만 따로 분류해서 구성한다.(`Domain specific data는 DW에 비해 소규모화된 데이터`) 각 부서에서 필요로하는 도메인이 확실한 데이터이고, 데이터 웨어하우스보다는 용량이 작기 때문에 (`특정 도메인의 데이터를 가져와서 사용하기 때문에`) RDS의 사용이 가능하다.
  그리고 Tableau와 같은 시각화 도구를 직접 DM에 붙여서 사용하기도 하며, OLAP(Online Analysis Processing) 작업을 위해서 BI 툴을 붙여서 사용한다.

  ETL 과정을 통해 데이터 웨어하우스의 데이터를 도메인 세분화된 데이터 마트의 데이터로 분류를 한다.

- ### **OLAP(Online Analytical Processing)**

  온라인으로 분석한다는 의미로 데이터 마트에서 사용된다. 반대되는 개념으로 OLTP(RDB)이다. 사용자가 대화형 쿼리를 통해서 다차원의 데이터(OLAP CUBE)들을 분석하고, 여러 테이블을 조인해서 분석하는 과정을 통해 의사결정에 데이터를 활용한다.
  주로 Columnar (Column Oriented DB)를 사용한다.

- ### **Row oriented DB & Column oriented DB(Columnar)**

  `Row Oriented DB와 Column Oriented DB의 개념은 데이터베이스의 내부 엔진의 동작방식상의 차이이다. 실제 사용자에게 있어서는 사용에 있어 차이가 없다.`

  특정 행을 검색/삭제/업데이트하는 과정에서 사용되는 DB를 Row Oriented DB하고 하며, 트랜젝션과 데이터의 무결성이 중요하다.
  반면에 데이터 웨어하우스에 많이 사용되는 데이터베이스는 Columnar(Column Oriented DB라고 한다)
  (`Row Oriented DB - OLTP - RDB`)

  `데이터 엔지니어 분야에서는 Column oriented DB(columnar)를 주로 사용`한다. Columnar는 열별로 데이터를 저장하고, Row Oriented DB와는 달리 특정 행의 데이터를 삭제하는 것은 가능하나, `데이터를 업데이트하는데 비효율적이거나 불가능`하다.
  Columar DB는 특정 칼럼의 데이터만 가져오거나 대량의 데이터에 대한 쿼리를 하는데 유용하다. (`같은 자료형의 데이터 처리에 유용`)그리고 대량의 데이터를 읽고 쓰는데 종종 쓰인다.
  (`Column Oriented DB - OLAP - DW(Data Warehouse`)

  업무에서 배치처리가 70%이상 많은 비중을 차지하고 있다.

## <ins><b>Batch processing</b></ins>

데이터의 일괄처리에 주로 사용되며, 데이터를 일정 기간동안 수집을 하고 나서 one slot으로 데이터를 처리하는 것을 말한다. (`대용량의 데이터 처리에 사용`)
주로 연산량이 많은 ML(Machine Learning)/DL(Deep Learning)과 같이 CPU 집약적인 작업에 많이 사용되며, latency보다는 throughput(일정 시간내에 처리해야 되는 처리량)에 더 중요성이 부여된 작업의 경우 사용된다.

- **단점**
  - 불규칙한 데이터 사이즈(데이터 사이즈의 변화가 심하기 때문에 각 데이터 처리 section에 대해 작업 주기를 할당하기가 어렵다. (`컴퓨팅 리소스와 배치 작업 주기의 산정의 어려움`))
  - 비즈니스나 DS의 특정 요구사항에 따라 연산 처리 속도와 배치 주기를 다르게 하기 위해 별도의 리소스가 필요
  - 데이터 처리 중에 문제가 발생해서 재처리하는 경우, 기존의 리소스를 재사용할지 아니면 재처리용 리소스를 별도로 할당할지에 대해 기존의 작업에 영향이 없는 선에서 결정해야 한다.
  - 연속된 태스크를 스케줄링하는 작업이 태스크 스케줄링(`워크플로 스케줄링`)이다.
    (A Task -> B Task if A Task is failed, C Task is executed와 같은 데이터 스케줄링 과정)
  - 실시간 데이터의 처리가 증가하고 있지만, 여전히 데이터 처리에 있어서는 Batch 처리의 비중이 크다.

## <ins><b>Hadoop echo system</b></ins>

하둡은 빅데이터 프레임워크로, 주로 배치 처리를 담당한다. HDFS, MapReduce, Spark 등의 Hadoop echo system을 구성하는 오픈소스 기술들을 사용한다.
(AWS EMR을 사용하여 Hadoop의 역할을 대체할 수 있고, Hive의 역할은 Amazon Athena이다.(`Query로 데이터를 분석`))

이 부분에 대해서는 기본 개념을 잡고, 실제 업무를 하면서 깊게 파고드는 것이 좋다.

## <ins><b>ETL & ELT</b></ins>

- ### **ETL(Extract Transform Load)**

  주로 OLTP(Online Transaction Processing) DB를 source로 하는 ETL 파이프라인을 만들고 DW와 DM으로 저장한다. 그리고 주로 일괄 처리 방식에서 사용되며, 주로 정형 데이터를 처리한다.

  `ref`. Transform(=ﬁlter, sort, aggregate, join, dedup(중복제거), validate, missing value, etc)

- #### **ETL의 단점**

  - 추출해야되는 데이터 스토어가 BE, DE의 영역으로 분류되어 DS나 BI 담당자가 원하는 데이터를 얻기 어렵다.

  - DE가 ETL 작업을 해줘야 원하는 dataset을 얻을 수 있다. (`민첩성 결여`)

  - 기존 DB 구조에서 크게 벗어나지 못하기 때문에 스키마 종속적이고 제한적인 데이터 타입을 가지며, 쿼리 등의 성능 때문에 DB 모델링의 중요성이 크다.

- #### **ETL보다 ELT인가?**

  아니다. 개인정보, 보안, 암호화 등의 요구사항에 따라 `데이터의 비식별 처리를 위해서는 ETL 방식이 필요`하다.(`전체 사용자 대상의 추천 시스템 구축`) 그리고 Data outpost가 필요한 경우에는 ETL로 데이터를 뽑아서 DW, DM을 구축한다.
  따라서 `ETL과 ELT를 시스템 및 비즈니스의 요구사항에 맞춰서 구축하는 것이 현명`하다.

<!-- 2차 정리 완료 (02:00:00) -->

## <ins><b>Storage 구분</b></ins>

Storage는 `Block, File, Object Storage`로 나뉜다.

File Storage는 File system이 셋팅된 환경에 데이터를 저장하는 것을 말하며, 물리적 저장소에 Block 단위로 저장하는 것을 Block Storage, Object Storage는 구글 드라이브와 같은 저장소에 파일(Object) 단위로 데이터를 저장하는 것을 말한다.

## <ins><b>Amazon S3</b></ins>

Amazon S3는 DL의 역할을 한다. 개발자들이 코드를 올려서 배포도 가능하며, Data Lake 역할 이외에도 다른 서비스와 연결되는 anchor point 역할을 해주기도 한다.
다양한 스토리지 클래스를 나뉘고, 스토리지 내의 데이터 로그 정보, Compliance(규정 준수 및 감사 관련) 관련 정보도 지원한다.

- ### **Amazon S3의 특징**

  - Object storage
  - 정형/비정형 데이터를 모두 저장할 수 있는 Data Lake 역할
  - 무한대로 확장할 수 있다.
  - 99.999999999% 데이터 내구성
  - 중간(파일 캐싱, 코드 저장소 등 다양한 쓰임)/최종 저장소로 사용된다.
  - AWS의 많은 서비스들을 엮어주는 허브의 역할
  - 객체를 버저닝하고, 비용 효율을 위해서 특정 조건에 따라 스토리지 클래스를 변경하는 것이 자유롭니다.(`예시. 30일 후에 Glacier로 저장하는 작업, 1년이 지난 후에 Glacier의 데이터를 업데이트`)

- ### **Amazon S3의 스토리지 클래스**

  - Standard : 자주 읽고 쓰는 경우 사용
  - Intelligent-Tiering : 엑세스 빈도에 따라 비용 효율적인 티어로 자동 관리
  - Standard-IA : 자주 액세스하진 않지만,필요할 때 빠르게 액세스해야되는 데이터
  - One Zone-IA : 3개가 아닌 1개의 AZ에만 저장(Standard보다 20% 저렴)
  - Glacier : 데이터 아카이브 (instant retrieval, flexible retrieval, deep archive 등)

## <ins><b>Amazon Athena</b></ins>

Amazon Athena는 S3의 데이터를 표준 SQL을 사용해서 분석할 수 있는 서버리스(`별도의 infrastructure나 Capacity에 대한 설정없이 서비스만 이용`) 대화식 쿼리 서비스(`Interactive하게 데이터를 조회하면서 결과를 바로 학인 가능`)이다.

- ### **비용 청구**

  비용에 대한 청구는 정상적으로 SQL로 처리한 데이터의 양에 따라 청구된다.

- ### **CTAS 쿼리 사용**

  CTAS 쿼리를 사용해서 쿼리를 통해 나온 결과 데이터를 별도의 파일로 추출 할 수 있다. (`이외에도 데이터 변환, 포멧 변환이 가능`)
  포멧 변환은 CSV, JSON, ORC, Avro, Parquet 등 다양한 표준 데이터 형식과 호환된다.

## <ins><b>S3 & Amazon Athena 실습</b></ins>

- ### **(1) 분석할 target log 데이터를 담을 디렉토리와 log정보를 담을 디렉토리를 위한 각 각의 디렉토리는 S3에 생성한다.**

  파이프라인이 구축되면 지정된 디렉토리에 정기적으로 로그 데이터가 쌓인다.

<br/>

- ### **(2) Amazon Athena(Query editor) - [설정] - log를 떨궈줄 S3의 디렉토리 경로 위치정보를 업데이트해줘야 한다.**

  이 설정을 안해주면 테이블 생성시에 에러가 발생한다.

<br/>

- ### **(3) Amazon Athena에 새로운 테이블 생성**

  테이블과 DB 생성 - Amazon Athena 페이지에서 Tables and views의 우측 [Create] 클릭 - S3 bucket data 클릭 - Table명 입력, Database 선택, Dataset을 분석할 dataset이 위치한 S3 bucket을 선택한다.
  Data format은 `JSON-Hive SerDe`를 선택하고, Column details에서 각 칼럼의 이름과 타입을 분석할 타겟 로그 데이터를 참고하여 입력해준다.
  최종적으로 `Preview table query`를 통해 쿼리가 생성된 것을 확인 할 수 있으며, 최종적으로 `Create table` 클릭을 통해 테이블을 생성해야 한다.

<br/>

- ### **(4) CTAS 쿼리를 통해서 쿼리한 결과 데이터에 해당하는 칼럼만 가지는 새로운 테이블을 생성한다.**

  ```sql
  CREATE TABLE new_table1 WITH (external_location='s3://..../ctas/',
  format='PARQUET',parquet_compression='SNAPPY')
  AS SELECT time, remote_ip FROM “class"."hg-data-table"

  CREATE TABLE new_table2 WITH (external_location=‘s3://..../ctas-bucketing/',
  format='PARQUET',parquet_compression='SNAPPY',bucketed_by=ARRAY['time'], bucket_count=1)
  AS SELECT time, remote_ip FROM "class"."hg-data-table"
  ```

  <div align="center">
    <img src="/images/post_images/220607_ctas_query_preview_table.png" alt="CTAS query 결과를 S3에 저장해서 Athena에서 새로 생성된 테이블 미리보기">
  </div>

  디렉토리 내에 있는 전체 파일에 대해서 분석이 되기 때문에 요금은 디렉토리 내의 전체 파일에 대해서 계산을 해야한다.

## <ins><b>Q&A section</b></ins>

- logstash와 filebeat의 실행 순서는 logstash가 listen된 상태에서 filebeat를 띄워야 순차적으로 처리가 되기 때문에 logstash를 우선적으로 실행한 상태에서 filebeat를 실행하도록 한다.

- JDBC와 같은 툴을 이용해서 다이렉트로 Athena에 로그 데이터를 날려서 처리를 할 수도 있다. (`Athena와 JDBC와 연동을 해서 사용하는 방법 찾아보기`)

- CTAS 쿼리를 통해서 나온 결과 데이터를 S3에 새로운 파일의 형태로 (parquet) 생성을 해주는데, 파일이 2개 생성되는 이유는 Athena 내부적으로 여러 engine들이 돌고 있기 때문이다. (`테이블의 갯수와는 상관이 없음`)
