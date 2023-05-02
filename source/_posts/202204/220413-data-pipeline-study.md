---
title: 220413 데이터 파이프라인 - 5
date: 2022-04-13 10:41:00
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

이번 포스팅에서는 웹 서버 전용 EC2 인스턴스를 생성하고, 생성한 EC2 인스턴스에 httpd 설치 및 실행을 해보고 간단한 테스트 웹 페이지를 띄워보는 실습을 해본다.

## <ins><b>Httpd</b></ins>

우선 웹 서버 전용 EC2 인스턴스를 생성한 뒤에 해당 EC2 인스턴스에 Httpd demon을 설치해줬다. 선택한 AMI가 CentOS와 비슷한 이미지를 가지고 만들었기 때문에 설치한 httpd 서비스가 EC2 lifting시에 자동으로 로드되게 하기 위해서는 서비스를 등록해야되는데, 설치하면 default로 서비스에 등록이 되기 때문에 아래와 같은 프로세스로 서비스 설치 및 시작을 하면 된다.
설치된 Httpd에 기본 Home 디렉토리(`/var/www/html`)가 있는데, 해당 위치에 웹 기본이 되는 파일(`index.html`)을 생성해서 간단하게 웹 페이지 테스트를 할 수 있다.

```zsh
# httpd 설치
$sudo yum install httpd -y

# httpd service 시작
$sudo service httpd start
Redirecting to /bin/systemctl start httpd.service

# 현재 실행되고 있는 service process에서 httpd관련 service를 출력
$ps -ef|grep httpd
root      6563     1  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    6564  6563  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    6565  6563  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    6566  6563  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    6567  6563  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    6568  6563  0 02:33 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
ec2-user  6611  3407  0 02:33 pts/0    00:00:00 grep --color=auto httpd

# root 사용자로 변경 (prompt $ -> #)
$sudo su -

# 웹 서버에 출력할 html파일 출력
$cd /var/www/html/

# index.html 파일 작성
$vi index.html
```

위에서 index.html 파일내에 HTML 코드를 작성 후, 주소표시줄에 "연결" 메뉴에서 확인하였던 퍼블릭 DNS 주소를 웹에 붙여넣으면, 작성했던 HTML 페이지가 출력됨을 확인 할 수 있다.
이처럼 Public DNS 주소를 통해 생성된 EC2 인스턴스의 리소스에 접근을 할 수 있다는 것을 알 수 있다.

추가적으로 위의 httpd를 설치 및 서비스 시작하는 일련의 과정을 EC2 인스턴스 생성시에 `고급 세부 정보 세션에`서 `사용자 데이터 부분`에 스크립트로 넣어서 간단하게 실습해볼 수도 있다. (웹 서버 시작 부분까지)

```zsh
$yum update -y
$yum install httpd -y
$sudo service httpd start
```

<!-- more -->

## <ins><b>Kafka 실습 구성도</b></ins>

<div align="center">
  <img src="/images/post_images/220413_kafka_practice_topology.png" alt="Kafka 실습 구성도">
</div>

이번 실습에서는 실제 Cluster까지는 구현을 안하고, 메시지가 어떻게 흘러가는지 이해할정도로만 구성하고, 메시지가 어떻게 흘러가는지 전반적인 흐름에 대해서 파악을 한다.

`파이프라인을 구성할때 PC의 툴이나 연필로 직접 그려가면서 파이프라인을 구성하는 연습을 하면 완성도 높은 파이프라인을 구성할 수 있다. 아직 지식이 많이 부족하더라도 조금씩 이해하고 있는 지식들을 최대한 활용해서 연습해보도록 하자.`

`(Topic name : twitter)`

- Kafka 설치 : Kafka에 Topic을 하나 생성해서 이 곳에 데이터를 쏠 것이다.

- Producer에서는 Logstash가 있는데, 이 친구는 Elastic search에서 데이터를 수집해서 Elastic search 안으로 넣어주는 프로그램이다. 데이터를 임의로 생성하기 어렵기 때문에, 트위터에서 데이터를 받아서 카프카에 넣어서 처리를 해볼 것이다.

- Topic을 Consumer하는 부분까지 구성한다. `Consumer 자체에서도 Logstash를 띄워서` 카프카 큐에 있는 데이터를 가져와서 화면에 띄워주는 부분까지 작업

위의 실습은 마치 온프레미스 환경에서 서버 3대(EC2 3개)를 가지고 구성한다고 가정하고 실습하도록 하자.

## <ins><b>Kafka EC2 인스턴스 생성 및 Kafka 설치</b></ins>

Kafka 실습 - 유형 t2.medium 이상 선택

인스턴스 세부 정보 구성 : 인스턴스 갯수 : 2 (한 번에 두 개 이상 시작 할 수 있도록 구성)

Kafka가 기본적으로 자바 베이스로 돌아가기 때문에 자바를 설치하고 카프카를 설치하면 된다. Open source용 자바를 설치 권장

```zsh
$sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```

```zsh
$wget https://dlcdn.apache.org/kafka/3.0.1/kafka_2.12-3.0.1.tgz
$tar xvf kafka_2.12-3.0.1.tgz
```

버전이 바뀌었을때에도 논리적 링크를 가져가기 위해서 링크를 걸어서 사용한도록 한다. (kafka 뒤에 길게 붙어있는 버전명을 신경쓰지 않아도 되는 장점이 있다)

```zsh
# 압축을 해제한 디렉토리를 kafka의 alias로 link를 걸어서 재사용할 수 있다.
$ln -s kafka_2.12-3.0.1 kafka
```

## <ins><b>Zookeeper & Kafka server 시작</b></ins>

Zookeeper에 대해서는 이전에 Hadoop의 eco system에 대해서 전반적으로 알아보면서 정리했을때 알아보았는데, Zookeeper는 클러스터의 모든 것을 조직화하는 기술이다. 이 기술을 이용하면 클러스터내에서 어떤 노드가 살아있는지 추적할 수 있고, 여러 어플리케이션이 사용하는 클러스트의 공유 상태를 안정적으로 확인할 수 있다. (`Hadoop과 치해지기 세 번째 이야기 중`)

```zsh
# 압축을 해제한 Kafka 폴더 내에서 아래 commandline을 실행한다.
# zookeeper 띄우기
# 클러스터간의 통신을 담당하는 친구 (=> zookeeper)
$./bin/zookeeper-server-start.sh config/zookeeper.properties &

# Kafka server 시작
$./bin/kafka-server-start.sh config/server.properties &
# 현재 실행중인 서비스 데몬 port로 확인
$sudo netstat -anp | egrep "9092|2181" #Kafaka server: 9092 / zookeeper: 2181
```

- 외부의 서버와 통신하기 위해서는 보안그룹에서 9092 port를 열어놔야지 다른 서버에서 producer가 메세지를 넘겨줄 수 있다. 보안그룹에서 9092 port 추가 필요.
  (`EC2 인스턴스의 보안그룹 -> 인바운드 그룹 규칙에 9092 port 추가` / `kafka-producer EC2 서버에서 데이터를 넘겨줄때 해당 포트를 통해 kafka-server의 가상서버 자원으로 접근할 수 있다`)

## <ins><b>Kafka에서 Topic 생성하기</b></ins>

```zsh
# Kafka에 Topic 생성
$bin/kafka-topics.sh --create --topic twitter --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092  &

# 생성된 topic 확인
$bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

메시지가 서버에 잘 쌓이는지, 서버 한 대에서 확인

```zsh
$./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic twitter --from-beginning
```

## <ins><b>Producer Setup</b></ins>

kafka를 테스트용으로 producer에서도 설치를 한다.

```zsh
# Java 설치
$sudo yum install -y java-1.8.0-openjdk-devel.x86_64

# kafka 설치/압축해제/압축 해제된 디렉토리를 link 걸어서 사용/해당 디렉토리로 이동 (과정 생략)

# Server로 Data를 Producing 할 것이기 때문에 Server쪽 IP의 확인이 필요하다.
# 내부 통신이기 때문에 Kafka-server의 Private IP 주소를 target IP 주소로 설정한다.
# topic이 twitter라는 곳에 message를 제공
$bin/kafka-console-producer.sh --topic twitter --bootstrap-server 172.31.47.33:9092
```

위의 설정이 다 끝나고 Producer 역할의 EC2에서 메시지를 입력하면, Consumer 역할을 하는 EC2의 Terminal에 출력이 되는 것을 확인할 수 있다.

<div align="center">
  <img src="/images/post_images/220413_kafka_producer_consumer.png" alt="Kafka server + Producer/Consumer 실습">
</div>

하나하나 파이프라인을 만들때마다 중간 체크를 확인해가면서 파이프라인을 구축해나가는 연습을 해야한다.
ctrl+d : 신호를 보내는 것이 아닌, 특수한 바이너리 값을 나타내며, EOF(End Of File)을 의미

`cf)`

- `ctrl+c` : 중단 명령 (프로그램의 실행을 강제로 중단)
- `ctrl+z` : 중단 명령 (작업이 끝나지 않았으므로, 프로세스에서 끊긴 상태를 유지. fg/bg 명령을 사용하여 프론트나 백스테이지의 작업을 이어나갈 수 있다)

## <ins><b>**[Producer side]** Logstash 설정 및 twitter 연결하기</b></ins>

### **[Logstash 설정하기]**

```zsh
$wget https://artifacts.elastic.co/downloads/logstash/logstash-7.4.0.tar.gz

$tar xvzf logstash-7.4.0.tar.gz
$ln -s logstash-7.4.0 logstash
```

logstash같은 경우에는 접속 정보나 hump라는 파일을 구성하는데 어느 폴더의 위치에서나 Logstash를 실행시키기 위해서 `.bash_profile에 path를 추가`해준다.

```bash
$ vi ~/.bash_profile

export LS_HOME=/home/ec2-user/logstash
PATH=$PATH:$LS_HOME/bin

$source ~/.bash_profile

# logstash 버전 확인
$logstash --version

# Producer_test.conf 파일 작성
# input, output 두 개의 section으로 나눠져있으며,
# input에는 twitter에서 정보를 가져오기 위한 consumer_key, consumer_secret, oauth_token, oauth_token_secret, keywords, full_tweet의 정보가 포함되어있다.
# output은 print문으로 생각하면 된다.
# keywords는 가져오고자 정보의 키워드를 문자열 배열로 구성한다.

# twitter developer 사이트에 가입하고, 프로젝트를 생성하면 API Key, API Secret key, Bearer Token을 주는데,
# API Key = Consumer key
# API Secret key = Consumer secret key
#

input {
  twitter {
    consumer_key => "[consumer_key]"
    consumer_secret => "[consumer_secret]"
    oauth_token => "[oauth_token]"
    oauth_token_secret => "[oauth_token_secret]"
    keywords => []
    full_tweet => true
  }
}

output{
    stdout{
      codec => rubydebug
  }
}

#logstash 실행하기
#앞서 작성한 *.conf 파일을 logstash 명령을 사용하여 실행한다.
$logstash -f producer_test.conf

```

Twitter 개발자 페이지에서 필요한 key들(consumer key, consumer secret, oauth token, oauth token secret)을 발급받아 사용하는 과정에서 에러가 발생해서 시간이 많이 지연되었다. 결과적으로 아래의 Twitter log를 Producer로부터 취득해서 가져올 수 있었고, 내일부터는 Producer로부터 취득한 Log 데이터를 Kafka의 message queue에 담아보는 작업부터 이어서 해봐야겠다. 쉬운게 없다...

```zsh
{
             "quoted_status_id" => 1514173632012234754,
                       "source" => "<a href=\"http://twitter.com/download/android\" rel=\"nofollow\">Twitter for Android</a>",
      "quoted_status_permalink" => {
         "display" => "twitter.com/Pirate_Radio_/…",
        "expanded" => "https://twitter.com/Pirate_Radio_/status/1514173632012234754",
             "url" => "https://t.co/CrNaTanRd5"
    },
                 "filter_level" => "low",
      "in_reply_to_screen_name" => nil,
                   "@timestamp" => 2022-04-13T11:15:32.000Z,
                           "id" => 1514200610903494658,
      "in_reply_to_user_id_str" => nil,
                  "quote_count" => 0,
              "is_quote_status" => true,
                       "id_str" => "1514200610903494658",
                  "reply_count" => 0,
                 "timestamp_ms" => "1649848532783",
                          "geo" => nil,
                     "@version" => "1",
                "quoted_status" => {
                           "source" => "<a href=\"http://twitter.com/download/iphone\" rel=\"nofollow\">Twitter for iPhone</a>",
                     "filter_level" => "low",
          "in_reply_to_screen_name" => nil,
                               "id" => 1514173632012234754,
          "in_reply_to_user_id_str" => nil,
                      "quote_count" => 3,
                  "is_quote_status" => false,
                           "id_str" => "1514173632012234754",
                      "reply_count" => 0,
                              "geo" => nil,
                      "coordinates" => nil,
                     "contributors" => nil,
                    "retweet_count" => 89,
                        "retweeted" => false,
            "in_reply_to_status_id" => nil,
                        "favorited" => false,
              "in_reply_to_user_id" => nil,
                       "created_at" => "Wed Apr 13 09:28:20 +0000 2022",
               "possibly_sensitive" => false,
                            "place" => nil,
                             "lang" => "ja",
                        "truncated" => false,
                   "favorite_count" => 418,
                             "user" => {
                              "favourites_count" => 63751,
                                    "utc_offset" => nil,
                                  "listed_count" => 1734,
                               "translator_type" => "none",
                               "followers_count" => 248287,
                       "profile_background_tile" => true,
                                            "id" => 8048402,
                            "profile_link_color" => "ABB8C2",
                                          "name" => "燃え殻",
                                 "notifications" => nil,
                         "withheld_in_countries" => [],
                                        "id_str" => "8048402",
                                      "verified" => false,
                      "profile_background_color" => "131516",
                                      "location" => "渋谷円山町",
                                           "url" => nil,
            "profile_background_image_url_https" => "https://abs.twimg.com/images/themes/theme14/bg.gif",
                                "statuses_count" => 22258,
                            "profile_text_color" => "333333",
                          "contributors_enabled" => false,
                                     "protected" => false,
                            "profile_banner_url" => "https://pbs.twimg.com/profile_banners/8048402/1622249405",
                             "profile_image_url" => "http://pbs.twimg.com/profile_images/892675680596484097/o5jBHSyC_normal.jpg",
                               "default_profile" => false,
                       "profile_image_url_https" => "https://pbs.twimg.com/profile_images/892675680596484097/o5jBHSyC_normal.jpg",
                    "profile_sidebar_fill_color" => "EFEFEF",
                                     "following" => nil,
                                 "friends_count" => 1794,
                         "default_profile_image" => false,
                                    "created_at" => "Wed Aug 08 06:48:57 +0000 2007",
                                     "time_zone" => nil,
                           "follow_request_sent" => nil,
                                   "description" => "http://moegara.com ←諸々の問い合わせはこちらまで何卒。小説『これはただの夏』公式HP https://www.shinchosha.co.jp/special/tadanonatsu/",
                                          "lang" => nil,
                                 "is_translator" => false,
                                   "screen_name" => "Pirate_Radio_",
                  "profile_background_image_url" => "http://abs.twimg.com/images/themes/theme14/bg.gif",
                  "profile_use_background_image" => true,
                  "profile_sidebar_border_color" => "000000",
                                   "geo_enabled" => false
        },
                         "entities" => {
            "user_mentions" => [],
                  "symbols" => [],
                     "urls" => [
                [0] {
                     "display_url" => "news.yahoo.co.jp/articles/0ebb8…",
                    "expanded_url" => "https://news.yahoo.co.jp/articles/0ebb8f0cfa9377ae6d317df08076ee6809847ddd",
                         "indices" => [
                        [0] 83,
                        [1] 106
                    ],
                             "url" => "https://t.co/Ekqgt1GcpV"
                }
            ],
                 "hashtags" => [
                [0] {
                    "indices" => [
                        [0] 72,
                        [1] 82
                    ],
                       "text" => "Yahooニュース"
                }
            ]
        },
                             "text" => "最後の1行まで、たっぷりいい記事。\n\n笑い飯 哲夫が経営する“激安の学習塾“とは「学力差が家庭環境によって起こるのはおかしい」(女子SPA！)\n#Yahooニュース\nhttps://t.co/Ekqgt1GcpV",
        "in_reply_to_status_id_str" => nil
    },
                  "coordinates" => nil,
                 "contributors" => nil,
         "quoted_status_id_str" => "1514173632012234754",
                "retweet_count" => 0,
                    "retweeted" => false,
        "in_reply_to_status_id" => nil,
                    "favorited" => false,
          "in_reply_to_user_id" => nil,
                   "created_at" => "Wed Apr 13 11:15:32 +0000 2022",
                        "place" => nil,
                         "lang" => "el",
                    "truncated" => false,
               "favorite_count" => 0,
                         "user" => {
                          "favourites_count" => 4818,
                                "utc_offset" => nil,
                              "listed_count" => 0,
                           "translator_type" => "none",
                           "followers_count" => 3,
                   "profile_background_tile" => false,
                                        "id" => 1348652191205052417,
                        "profile_link_color" => "1DA1F2",
                                      "name" => "🕊️ココキョウ",
                             "notifications" => nil,
                     "withheld_in_countries" => [],
                                    "id_str" => "1348652191205052417",
                                  "verified" => false,
                  "profile_background_color" => "F5F8FA",
                                  "location" => nil,
                                       "url" => nil,
        "profile_background_image_url_https" => "",
                            "statuses_count" => 1532,
                        "profile_text_color" => "333333",
                      "contributors_enabled" => false,
                                 "protected" => false,
                        "profile_banner_url" => "https://pbs.twimg.com/profile_banners/1348652191205052417/1610533728",
                         "profile_image_url" => "http://pbs.twimg.com/profile_images/1349314708696748036/FlCQDL_N_normal.jpg",
                           "default_profile" => true,
                   "profile_image_url_https" => "https://pbs.twimg.com/profile_images/1349314708696748036/FlCQDL_N_normal.jpg",
                "profile_sidebar_fill_color" => "DDEEF6",
                                 "following" => nil,
                             "friends_count" => 68,
                     "default_profile_image" => false,
                                "created_at" => "Mon Jan 11 15:25:46 +0000 2021",
                                 "time_zone" => nil,
                       "follow_request_sent" => nil,
                               "description" => "無言フォロー、リツイート等でご無礼いたした折は声を掛けて頂けると助かります。",
                                      "lang" => nil,
                             "is_translator" => false,
                               "screen_name" => "2NRcxTDUqzoqQCo",
              "profile_background_image_url" => "",
              "profile_use_background_image" => true,
              "profile_sidebar_border_color" => "C0DEED",
                               "geo_enabled" => false
    },
                     "entities" => {
        "user_mentions" => [],
              "symbols" => [],
                 "urls" => [],
             "hashtags" => []
    },
                         "text" => "φ(．．)",
    "in_reply_to_status_id_str" => nil
}
```
