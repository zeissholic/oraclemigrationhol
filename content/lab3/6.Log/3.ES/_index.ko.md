---
title: Elastic Search 저장
weight: 30
pre: "<b>4.5.3 </b>"
---

**S3에 로그가 저장되면 Lambda가 호출되고 함수에서 IP를 위경도 기반으로 변경한 후 Elastic Search Service에 저장합니다.** <br/><br/>

### 1. Elasticsearch Service 및 Lambda용 Security Group 생성 
1.1 AWS 콘솔에서 EC2메유에서 Security group 메뉴로 이동 후 아래 정보로 Security group을 생성합니다.  

&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-lambda-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-lambda-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Inbound rules  

&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-es-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-es-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Inbound rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;TCP | 9200 | sct-svr-sg | SCT Server  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;TCP | 443 | sct-svr-sg | SCT Server  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;TCP | 443 | aws-lambda-sg | Lambda  





### 2. Elasticsearch Service 생성 
2.1 AWS 콘솔에서 **Elasticsearch Service**로 이동 후 **Create a new domain** 버튼을 클릭합니다.  

2.2 아래 정보를 입력하고 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Deployment type: **Production**  
&nbsp;&nbsp;&nbsp;&nbsp;Elasticsearch version: **7.7**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

* Data nodes storage  
&nbsp;&nbsp;&nbsp;&nbsp;Elasticsearch domain name: **es-mbpshop**  
&nbsp;&nbsp;&nbsp;&nbsp;Availability zones**2-AZ**  
&nbsp;&nbsp;&nbsp;&nbsp;Instance type**t2.small.elasticsearch**  
&nbsp;&nbsp;&nbsp;&nbsp;Number of nodes**2**  
* Dedicated master nodes  
&nbsp;&nbsp;&nbsp;&nbsp;Instance type**t2.small.elasticsearch**  
&nbsp;&nbsp;&nbsp;&nbsp;Number of master nodes**3**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

&nbsp;&nbsp;&nbsp;&nbsp;VPC**AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet**Private-2a, Private-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;Security group**aws-es-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Domain access policy**Allow open access to the domain**  
&nbsp;&nbsp;&nbsp;&nbsp;Encryption**선택 해제**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

VPC endpoint를 기록합니다.

### 3. Lambda 함수 생성
3.1 AWS 콘솔에서 **Lambda** 메뉴로 이동 후 좌측에서 **Functions**로 이동합니다.  

3.2 우측의 **Create Fuction** 버튼을 클릭합니다.  

3.2 아래 설정으로 입력합니다.
&nbsp;&nbsp;&nbsp;&nbsp;**Author from scratch 선택**  
&nbsp;&nbsp;&nbsp;Function name: **s3-to-es**  
&nbsp;&nbsp;&nbsp;Runtime: **Python 3.7**  
&nbsp;&nbsp;&nbsp;&nbsp;**Create Function**  

3.3 아래 파일을 다운 받습니다.  
**[s3-to-es.zip](/files/s3-to-es.zip)**  

3.4 우측 중앙에 **Actions** 메뉴에서 **Upload a .zip file**을 선택하고 앞에서 받은 파일을 선택합니다.  
![](/images/lab3/es_1.png#center)


3.5 좌측의 **Basic settings**에서 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;Handler: **s32es.handler**  
&nbsp;&nbsp;&nbsp;Memory(MB): **512**  
&nbsp;&nbsp;&nbsp;Timeout: **5min 0sec**  

3.6 상단의 **Environment Variables**에서 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;ES_REGION: **ap-northeast-2**  
&nbsp;&nbsp;&nbsp;ES_HOST: **https://vpc-es-mbpshop-pcrxxxxxxxxxx.ap-northeast-2.es.amazonaws.com**  ES의 VPC endpoint   
&nbsp;&nbsp;&nbsp;ES_INDEX: **mbpshop**  
![](/images/lab3/es_2.png#center75)


3.7 **Lambda**의 **IAM role**을 수정합니다.  
**IAM 메뉴**로 이동한 후 **s3-to-es-role-xxxxx**를 선택합니다.  
퍈의를 위해 **AmazonS3FullAccess**, **AWSLambdaBasicExecutionRole** Policy를 추가합니다.  


3.8 하단의 **VPC**에서 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;VPC connection: **Custom VPC**  
&nbsp;&nbsp;&nbsp;VPC: **AWS**   
&nbsp;&nbsp;&nbsp;Subnets: **private-2a, private-2c**  
&nbsp;&nbsp;&nbsp;Security groups: **aws-lambda-sg**   
![](/images/lab3/es_3.png#center75)
 

### 3. S3 Trigger 생성 
3.1 좌측 상단의 **Add trigger** 버튼을 클릭합니다.  

3.2 Trigger 대상으로 S3를 선택하고 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;Bucket: **aws-was-logs-xxxxx**   
&nbsp;&nbsp;&nbsp;Event type: All object creat events 아래 **PUT**  
![](/images/lab3/es_4.png#center75)


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
