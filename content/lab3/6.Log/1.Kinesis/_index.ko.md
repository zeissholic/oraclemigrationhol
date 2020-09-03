---
title: S3에 로그 저장
weight: 10
pre: "<b>4.5.1 </b>"
---

**Kinesis Data Firehose를 통해 S3에 로그를 저장합니다.** <br/><br/>

### 1. S3 버킷 생성
1.1 AWS 콘솔에서 **S3**메뉴로 이동 후 **Create bucket** 버튼을 클릭합니다.  

1.2 Bucket name을 **aws-was-logs-OOOOO** (OOOOO는 개인식별자)로 입력하고 나머지 설정은 기본으로 버킷을 생성합니다.  

### 2. Kinesis Data Firehose 생성
2.1 AWS 콘솔에서 **Kinesis**메뉴로 이동 후 우측에서 **Kinesis Data Firehose** 라디오버튼을 선택하고 **Create delivery stream** 버튼을 클릭합니다.  

2.2 아래 내용으로 입력 후 저장합니다.
&nbsp;&nbsp;&nbsp;&nbsp;Delivery stream name: **kdf-aws-was**  
&nbsp;&nbsp;&nbsp;&nbsp;Source: **Direct PUT or other sources**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  
&nbsp;&nbsp;&nbsp;&nbsp;Destination: **Amazon S3**  
&nbsp;&nbsp;&nbsp;&nbsp;S3 bucket: **aws-was-logs-OOOOO**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  


### 3. IAM Role 변경
3.1 AWS 콘솔에서 **IAM**메뉴로 이동후 Roles에서 **mbpshop_role**을 선택합니다.   

3.2 아래의 내용으로 inline policy를 추가합니다.  (account ID는 변경합니다)
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "firehose:PutRecord",
                "firehose:PutRecordBatch"
            ],
            "Resource": "arn:aws:firehose:ap-northeast-2:567OOOOOOO:deliverystream/kdf-aws-was"
        }
    ]
}
```

### 4. Kinesis Agent 설치 및 설정
4.1 AWS용 Cloud9에 접속합니다.  

4.2 aws-was-2a에 로그인합니다.  
```
ssh -i migrationhol.pem centos@10.254.101.10
```

4.3 아래 명령으로 Kinesis Agent를 설치합니다.  
```
sudo su -
sudo yum install –y https://s3.amazonaws.com/streaming-data-agent/aws-kinesis-agent-latest.amzn2.noarch.rpm
```

4.4 Kinesis Agent 설정을 변경합니다.  
```
cp /etc/aws-kinesis/agent.json /etc/aws-kinesis/agent.json.org
vi /etc/aws-kinesis/agent.json
```

4.5 아래의 내용으로 변경합니다.
```
{
  "cloudwatch.emitMetrics": false,
  "firehose.endpoint": "firehose.ap-northeast-2.amazonaws.com",
  "flows": [
    {
      "filePattern": "/opt/tomcat/latest/logs/localhost_access_log.*.txt",
      "deliveryStream": "kdf-aws-was"
    }
  ]
}
```

4.6 Kinesis Agent를 실행합니다.
```
sudo systemctl start aws-kinesis-agent
sudo systemctl enable aws-kinesis-agent
```


### 5. Tomcat 설정 변경
Tomcat log파일은 기본적으로 UMASK 0027(640)으로 설정되어 tomcat 유저 혹은 tomcat 그룹이 아닌 경우 파일을 읽을 수 없습니다. 이 부분 0022(644)로 수정합니다.  

5.1 아래 경로로 이동해 tomcat.service 파일을 오픈합니다.  
```
cd /etc/systemd/system
vi tomcat.service
```
5.2 Group=tomcat 아래 아래 내용을 추가하고 저장합니다.
```
Environment='UMASK=0022'
```
![](/images/lab3/log_1.png#center75) 

5.3 Tomcat 데몬을 다시 시작합니다.
```
sudo systemctl daemon-reload
sudo systemctl restart tomcat
```

5.4 폴더의 권한을 수정합니다.
```
chmod 755 /opt/tomcat
chmod 755 /opt/tomcat/latest/logs
```

5.5 기존에 저장된 로그도 전송을 원할 경우 아래 명령을 실행합니다.
```
cd /opt/tomcat/latest/logs
chmod 644 localhost_access_log.*.txt
```

5.6 Kinesis 메뉴로 돌아가 데이터가 들어오고 있는지 확인합니다.
![](/images/lab3/log_2.png#center75) 

5.7 S3에도 데이터가 들어오고 있는지 확인합니다.
![](/images/lab3/log_3.png#center75) 


### 6. Tomcat Log 형식 변경
Tomcat Log에는 실제 클라이언트의 IP가 아닌 ALB의 IP가 기록됩니다. 이를 아래의 방법으로 접속 IP를 기록하게 합니다.  

6.1 Tomcat설정에서 **server.xml**을 오픈하고 아래 내용을 추가 합니다.
```
cd /opt/tomcat/latest/conf
cp server.xml server.xml.org
vi server.xml
```

```
        <!-- Remote IP Valve -->
        <Valve className="org.apache.catalina.valves.RemoteIpValve" remoteIpHeader="x-forwarded-for" protocolHeader="x-forwarded-proto" />
```

6.2 해당파일의 제일 아래 pattern에 아래를 추가합니다.
```
%{X-Forwarded-For}i
```
![](/images/lab3/log_4.png#center) 

6.3 Tomcat을 다시 시작합니다.
```
sudo systemctl restart tomcat
```




---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
