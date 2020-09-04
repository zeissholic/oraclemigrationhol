---
title: Aurora-Elasticsearch
weight: 70
pre: "<b>4.7 </b>"
---

**Aurora에 저장된 데이터를 Elasticsearch Service로 동기화합니다.** <br/><br/>


### 0. Aurora Binlog 설정 변경
0.1 AWS용 Cloud9으로 이동합니다.

0.2 아래 명령어로 Aurora DB에 연결합니다. (자신의 aurora cluster url로 변경합니다.)
```
mysql --host=octankdb.cluster-cqx6j1wxxxx.ap-northeast-2.rds.amazonaws.com --port=3306 --user=admin --password=qwert12345
```

0.3 binlog 보관기간을 24시간으로 늘립니다.
```
 call mysql.rds_set_configuration('binlog retention hours', 24);
                    
```

0.4 RDS메뉴에서 Parameter groups로 이동 후 **aws-aurora-dbcpg** 에서 아래 내용을 변경합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;binlog_format: **ROW** 
&nbsp;&nbsp;&nbsp;&nbsp;binlog_checksum: **NONE**

0.5 Aurora Instance를 재시동 합니다. (binlog_format은 static 변수로 서버 재시동까지는 반영이 되지 않습니다. )

### 1. DMS Source Endpoint 생성
1.1 AWS 콘솔에서 **DMS**메유로 이동 후 좌측의 **Endpoints** 선택 후 **Create endpoint** 버튼을 클릭합니다.  

1.2 Aurora 연결
아래를 입력후 저장합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint type: **Source endpoint**  
&nbsp;&nbsp;&nbsp;&nbsp;Select RDS DB instance: **선택**   
&nbsp;&nbsp;&nbsp;&nbsp;RDS instance: **octankdb-instance-1**   
&nbsp;&nbsp;&nbsp;&nbsp;Password: **qwert12345**   

VPC를 AWS선택하고 Replication instance를 dms-aws를 선택후 성공적으로 연결되는지를 확인합니다  


1.3 DMS용 IAM Role 생성합니다.  
이름은 dms2es-policy로 아래 내용을 추가 합니다. dms-es-role을 생성하고 아래 policy를 추가 합니다.  
서비스는 DMS를 선택하고 진행합니다.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "es:ESHttpDelete",
                "es:ESHttpGet",
                "es:ESHttpHead",
                "es:ESHttpPost",
                "es:ESHttpPut"
            ],
            "Resource": "*"
        }
    ]
}
```

1.4 ES의 security group에 DMS의 접근을 허용합니다.
aws-es-sg의 inbound rule에 아래를 추가합니다.
**TCP | 443 | dms-sg | DMS**


1.4 Elasticsearch 연결
아래를 입력후 저장합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint type: **Target endpoint**  
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint identifier: **target-es**   
&nbsp;&nbsp;&nbsp;&nbsp;Target Engine: **elasticsearch**   
&nbsp;&nbsp;&nbsp;&nbsp;Service access role ARN: **방금생성산 IAM의 ARN**   
Endpoint URI: **es-mbpshop의 VPC endpoint**  

VPC를 AWS선택하고 Replication instance를 dms-aws를 선택후 성공적으로 연결되는지를 확인합니다  


### 2. DMS Task 생성
2.1 AWS 콘솔에서 **DMS**메유로 이동 후 좌측의 **Database migration tasks** 선택 후 **Create task** 버튼을 클릭합니다.  

2.2 아래를 입력후 저장합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Task identifier: **aurora-es-task**  
&nbsp;&nbsp;&nbsp;&nbsp;Replication instance: **dms-aws**   
&nbsp;&nbsp;&nbsp;&nbsp;Source database endpoint: **octankdb-instance-1**   
&nbsp;&nbsp;&nbsp;&nbsp;Target database endpoint: **target-es**   
&nbsp;&nbsp;&nbsp;&nbsp;TMigration type: **migrate existing data and replicate ongoing changes**  

&nbsp;&nbsp;&nbsp;&nbsp;Table mappings에서 아래 내용으로 변경합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;TSelection rules에서 add new selection rule을 선택합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Schema: **Enter a schema**  
&nbsp;&nbsp;&nbsp;&nbsp;Schema name: **WEBUSER**  
&nbsp;&nbsp;&nbsp;&nbsp;Table name: **CARTS**  
![](/images/lab3/dms_1.png#center)

부하테스트를 이용해 실제 데이터를 입력하고 변경 내역이 잘 동기화 되는지 확인합니다.

### 3. Kibana Dashboard에 추가
3.1 SCT 서버로 이동해 Kibana에 연결합니다.  

3.2 Managment >  Index patterns > Create Index 선택하고 **carts**를 입력합니다.  
![](/images/lab3/kibana_17.png#center)

3.3 Time filter에 **target_date**를 선택하고 인덱스를 생성합니다.  

3.4 좌측의 Dashboard를 선택하고 Create New를 클릭합니다.  

3.5 Vertical bar 그래프를 선택하고 carts 를 소스로 선택합니다.  

3.6 Metrics > Add > Y-axis > Aggeregation:Sum > Field:Price 선택
![](/images/lab3/kibana_18.png#center30)

3.7 Buckets > Add > X-axis > Aggregation:Date Histogram > Field:Target date > interval:hourly 선택
![](/images/lab3/kibana_19.png#center30)


3.5 간단히 아래와 같이 배치해 실제 접속 로그와 매출의 관계를 비교해서 살펴볼 수 있습니다.
![](/images/lab3/kibana_20.png#center)



---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
