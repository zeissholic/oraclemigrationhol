---
title: Athena
weight: 20
pre: "<b>4.5.2 </b>"
---

**Athena를 통해 S3에 저장된 로그를 조회합니다.** <br/><br/>


### 1. Athena 
1.1 S3 메뉴로 이동해 query result를 저장할 버킷을 생성합니다. **athena-query-result-OOOOO** OOOOO는 개인별로 임의의 값을 사용합니다.

1.2 AWS 콘솔에서 **Athena** 메뉴로 이동합니다. 상단의 **set up query result location** 링크를 클릭하고 이전에 생성한 버킷의 정보를 등록합니다.
```
s3://athena-query-result-OOOOO/
```
![](/images/lab3/a_1.png#center) 
![](/images/lab3/a_2.png#center) 


1.3 AWS 콘솔에서 **Athena** 메뉴로 이동 후 우측의 **New query**에서 아래 쿼리로 데이타베이스를 생성합니다.  
```
CREATE DATABASE mbpshop;
```
![](/images/lab3/a_3.png#center) 

1.4 **tomcat_access_logs** 테이블을 생성합니다.  
```
CREATE EXTERNAL TABLE IF NOT EXISTS mbpshop.tomcat_access_logs (
  source_ip string,
  col1 string,
  col2 string,
  accesstime string,
  timezone string,
  request_verb string,
  request_url string,
  request_proto string,
  httpcode int,
  transferred bigint,
  remoteip string 
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH SERDEPROPERTIES (
  'serialization.format' = '1',
  'input.regex' = '([^ ]*) ([^ ]*) ([^ ]*) \[([^ ]*) ([^ ]*)\] \"([^ ]*) ([^ ]*) ([^ ]*)\" ([0-9]*) ([^ ]*) ([^ ]*)'
) LOCATION 's3://aws-was-logs-ijpark/';
```
![](/images/lab3/a_4.png#center) 

1.5 저장된 데이터를 확인합니다.  
![](/images/lab3/a_5.png#center) 

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
