---
title: 220604 데이터 파이프라인 구축 오프라인 수업 / 2주차 오프라인 수업 전 준비
date: 2022-06-04 15:58:00
tags:
  - AWS
  - Data-Pipeline
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220604_preparation.jpeg" alt="Preparation">
</div>

<br/>
<br/>

이번 포스팅에서는 내일 있을 데이터 파이프라인 구축 관련 수업에서 실습할 내용들에 대해 미리 실습을 해보고, 개념적인 부분을 좀 다져보려고 한다.
미리 실습을 해보면서 혼자서 해보는 과정에서 생기는 의문이나 질문거리를 좀 정리해서 내일 수업시간에 적극 질문해봐야겠다.

## <ins><b>이전 수업 실습내용</b></ins>

우선 이전 실습에서는 ubuntu로 EC2 인스턴스를 하나 생성하고, EC2 인스턴스 내에 logstash와 filebeat를 설치하였다. (`logstash가 Java 기반이기 때문에 JDK도 설치를 해주었다`)

그리고 샘플로 받은 logstash \*.conf 파일을 받아서 간단한 로그 파일을 생성해보았다.

딱 여기까지 실습을 하고 마무리 하였는데, 추가적으로 하면 좋을 부분이 있었다. `버전이 바뀌더라도 일관되게 logstash 명령을 사용하기 위해서 ln -s로 압축을 해제한 폴더와 logstash 명령을 linking`해주는 추가적인 작업을 하면 좋을 것 같았고, 어느 곳에서나 logstash 명령만으로 사용할 수 있도록 .profile 파일에 logstash의 bin 폴더의 경로와 관련된 환경변수를 선언해주는 것이 좋을 것 같다고 생각했다.

```zsh
$ln -s logstash-7.4.0 logstash

$vi ~/.profile
# export LS_HOME=/home/ubuntu/logstash
# PATH=$PATH:$LS_HOME/bin

$source ~/.bash_profile
```

위의 부분에 대해서도 미리 실습을 하면서 적용을 해 볼 것이다.

## <ins><b>다음 시간 실습내용</b></ins>

2주차 수업에서는 이미 생성된 로그파일을 input의 file 플러그인을 사용해서 읽어서 output의 file 플러그인으로 새로운 파일로 추출해낼 것이다. 그리고 filter 플러그인을 사용해서 읽어들인 로그 데이터를 정제(filter 플러그인)해서 내보내는 방법에 대해서도 실습을 하고, 최종적으로 filebeat와 logstash를 연동해보는 실습까지 해 볼 예정이다.

<!-- more -->

## <ins><b>file 플러그인에 대해서 구체적으로 알아보기</b></ins>

- ### **start_position**

  file 플러그인에서 `start_position`은 "beginning", "end"로 값을 설정하는데, "beginning"은 말 그대로 데이터를 처음부터 읽어들이는 방식이고, "end"는 가장 최근부터 읽어 들이는 것을 말한다. 데이터 유실이 없이 데이터를 읽기 위해서는 "beginning"으로 설정해서 데이터를 읽어야 한다. (`default value : end`)

- ### **sincedb**

  logstash의 input file 플러그인을 사용하게 되면, sincedb 파일에 어디까지 데이터를 읽었는지 정보를 기록하게 된다. sincedb 파일들은 총 네 개의 칼럼으로 구성이 되어있는데, `inode number`, `The major device number`, `The minor device number`, `The current byte offset(파일 내)`

  별도로 sincedb_path에 대한 설정을 하지 않았을 경우, `user home 디렉토리를 확인`해보거나 `logstash 폴더/data/plugins/inputs/file/ 안을 확인`해보면 된다.
  input파일을 여러개 등록해두면, sincedb가 여러개 생성이 된다.
  `참고 소스코드(filewatch) : logstash 폴더/vendor/bundle/jruby/1.9/gems/filewatch-0.9.0/lib/filewatch`

  input의 file 플러그인으로 읽고 있는 log 파일 자체의 변경이 발생하면, 이미 생성되어있는 sincedb에 새로운 inode 값과 offset 정보가 새로운 행 정보로써 추가된다. (`예시) 파일 삭제 후 재 생성시`)

  ```zsh
  #pick SINCEDB_DIR if available, otherwise use HOME
  sincedb_dir = ENV["SINCEDB_DIR"] || ENV["HOME"]
  ```

## <ins><b>filter 플러그인 테스트</b></ins>

    filter 플러그인 중에 `grok`, `prune`, `mutate`가 있다.

- ### **grok**

  grok은 `(심적으로)이해하다, 공감하다`라는 의미를 가졌다. 이런 의미와는 전혀 관계없이 grok이라는 필터 플러그인의 기능은 문자열 포멧 개념으로 이해 할 수 있다.

  input을 통해 들어온 로그 데이터 정보가 `KEY:HI VALUE:100`라는 문자열을 가지고, filter의 grok 플러그인은 아래와 같이 정의된다고 하자.

  ```json
  input {
    .....
  }
  filter {
    grok {
      match => {
        "message" => "KEY\:%{WORD:key_field} VALUE\:%{WORD:value_field}"
      }
    }
  }
  output {
    stdout {
      codec => rubydebug
    }
  }
  ```

  grok의 패턴은 파이썬과 달리 아래와 같은 필터 규칙을 따른다.

  ```python
  # ${정규 패턴명:사용자 정의 필드}`
  # KEY 이후에 오는 단어 하나를 key_field라는 사용자 정의 필드에 저장한다.
  KEY\:%{WORD:key_field} VALUE\:%{WORD:value_field}
  ```

  패턴을 사용해서 로그를 분석할 수도 있지만, 패턴으로 표현하기에 복잡한 로그들의 경우에는 패턴이 아닌 정규 표현식을 사용한다.

  ```python
  # ?<필드명>정규표현식
  # KEY에 적용된 정규표현식 w+은 값이 단어로, 한 단어 이상이라는 의미를 가지며,
  # VALUE에 적용된 정규표현식 d+는 숫자이면서 정수 전체라는 의미를 가진다.
  grok {
    match => {
      "message" => "KEY\:(?<key_field>\w+) VALUE\:(?<value_field>\d+)"
    }
  }
  ```

  grok 패턴을 이해하기 위해서는 정규 표현식을 정확히 알아야 어떤 패턴의 데이터인지 구분할 수 있기 때문에 패턴 사용보다는 정규 표현식으로만 해보는 것이 좋다고 한다.

  - grok의 정규표현식 예시

    ```python
    # \w : 한 글자
    # \w+ : 한 단어
    # \d : 정수 한 자리
    # \d+ : 정수 전체
    # \s : 공백 한 칸
    # \s+ : 공백 전체
    # \특수문자 : 특수문자 구분

    grok {
    match => {
        "message" => "\[(?<year>\d+)\-(?<month>\d+)\-(?<day>\d+)T(?<hour>\d+)\:(?<minute>\d+)\:(?<second>\d+)\,(?<millisecond>\d+)\]\[(?<log_level>\w+\s+)\]\[(?<log_kind>\w+\.\w+\s+)\]\[(?<log_class>\w+)\] %{GREEDYDATA:log_info}"
      }
    }
    ```

- ### **prune**

  `(구체적인 활용방법 찾아서 정리해보기)`
  prune은 가지치기하다, 부분을 없애서 축소하다는 의미를 가졌다. interpolate(참견하다, 덧붙이다)와 whitelist_names 라는 옵션을 가진다.

- ### **mutate**

  `(구체적인 활용방법 찾아서 정리해보기)`
  mutate는 "새로운 형태로 변형되다"라는 의미를 가졌다. remove_field라는 옵션을 가지며, remove_field 옵션의 값으로 제거 할 필드의 이름을 배열로 선언한다.

## <ins><b>filebeat-logstash 연동 실습</b></ins>

여러 타입들이 있는데, 그 중 파일(input.file)을 읽기 위해 `filestream`을 type의 값으로 넣어준다. path(기본경로)로 지정한 위치의 input.file을 읽어서 해당 파일의 내용을 logstash로 전달하도록 `output.logstash`의 hosts 정보에 5044 포트번호를 listen하도록 한다.

`(command의 실행 순서는 logstash 다음에 filebeat를 실행해야 한다. 우선 logstash가 filebeat를 listen하고 있는 상태를 유지한 상태에서 filebeat가 5044번 포트로 로그 데이터를 쏴준다)`

`[logstash-config-filebeat.conf]`
우선 logstash는 5044번 포트를 listen하고 있는 상태이다.

```json
input {
  beats {
    port => 5044
  }
}

output {
 stdout {
   codec => line { format => "From filebeat : %{message}"}
 }
}
```

`[filebeat.yml]`
filebeat를 run하기 위해서는 \*.yml 파일이 필요하다.
`chmod go-w ﬁlebeat.yml`는 그룹 및 기타 사용자(group/others)에 대한 쓰기 권한 거부 (오직 소유자만 수정 할 수 있도록 설정)
filebeat는 logstash가 listen하고 있는 5044 포트로 읽은 log data를 넘겨준다.

```yml
filebeat.inputs:
  - type: filestream
    enabled: true
    paths:
      - /home/ubuntu/input.file

output.logstash:
  hosts: ['localhost:5044']
```

`과정 : input.file -> beats -> logstash -> output`

## <ins><b>실습 과정 중 알게 된 내용들</b></ins>

filebeat의 filebeat.inputs의 path에 `input.file`을 넣어주게 되면, 임의로 `"echo "1" >> input.file"`명령으로 생성해준 로그파일의 데이터를 읽어서 넘겨준다. output의 file 플러그인에서 path 속성에 경로와 함께 파일명을 지정해주면, 아래 codec에 line 내에 지정한 format 텍스트가 새로 지정한 로그 파일 안의 내용으로 들어간다.
(`단, 동 시간에 복수의 로그파일을 생성할 경우, 동일한 로그파일에 중복되서 데이터가 누적될 수 있기 때문에 삭제를 하고 다시 로그파일을 생성하는 것이 좋다`)
