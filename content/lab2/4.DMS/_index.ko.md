---
title: DMS 구성 및 DB 동기화
weight: 40
pre: "<b>4. </b>"
---

**on-premise와 AWS의 Database 동기화를 위한 Database Migration Service (DMS)를 구성하고 설정합니다.**ㄴ

### 1. DMS Subnet gorups 생성  
1.1 AWS 콘솔에서 DMS메뉴로 이동합니다.  
1.2 좌측의 **Subnet gorups**을 선택하고 **Create subnet group**을 클릭합니다.  
1.3 아래 설정을 등록합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **dms-aws-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **dms-aws-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnets: **private-2a, private-2c**  
![](/images/lab2/dms_1.png#center)

### 2. DMS용 Security Group 생성  
2.1 EC2 > Security Groups로 이동해 새로운 Web서버를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **dms-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **dms-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  


### 3. On-premise Oracle DB 수정  
3.1 on-premise용 Cloud9을 실행하고 DB서버여 연결합니다.  
```
ssh -i migrationhol.pem centos@172.16.255.100
```
3.2 oracle 유저로 switch user 합니다.  
```
sudo su - oracle
```
3.3 sqlplus에 연결합니다.  
```
sqlplus "/as sysdba"
```
3.4 아래 명령으로 Archieve 모드로 변경합니다.
```
shutdown immediate;
startup mount;
ALTER database ARCHIVELOG;
shutdown immediate;
startup;
```
3.5 아래 명령으로 Oracle supplemental logging을 활성화 합니다.
```
ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
```
3.6 아래 명령으로 테이블단위 supplemental logging을 활성화 합니다.
```
ALTER TABLE "WEBUSER"."USERS" ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
ALTER TABLE "WEBUSER"."CARTS" ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
```

### 4. DMS 인스턴스 생성  
4.1 AWS 콘솔에서 DMS메뉴로 이동합니다.  
4.2 **Replication instance**을 클릭합니다.   
4.3 아래 설정을 등록합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **dms-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **dms-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Instance class: **dms.t2.large**    
&nbsp;&nbsp;&nbsp;&nbsp;Engine version: **3.4.1**    
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**    
&nbsp;&nbsp;&nbsp;&nbsp;Public accessible: **No**   
&nbsp;&nbsp;&nbsp;&nbsp;Advanced security and network configuration  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Replication subnet group: **dms-aws-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Availability zone: **ap-northeast-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Security Group: **dms-sg**  
![](/images/lab2/dms_2.png#center)  
4.4 DMS서버가 생성된 후 Details에서 Private IP를 확인합니다.


### 5. on-premise, AWS DB에 dms-sg 접근 허용  
5.1 EC2 > Security Groups로 이동합니다.  
5.2 **on-premise-db-sg**에 아래 inbound rule 추가  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 1521 : DMS서버의 IP : DMS**  
5.3 **aws-db-sg**에 아래 inbound rule 추가  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 3306 : dms-sg : DMS**  

### 6. Source Endpoints 생성  
6.1 DMS > **Endpoints** 선택하고 **Create Endpoint**버튼을 클릭합니다.  
6.2 아래 설정을 등록합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint type: **Source endpoint**  
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint identifier: **source-oracle**  
&nbsp;&nbsp;&nbsp;&nbsp;Source engine: **oracle**   
&nbsp;&nbsp;&nbsp;&nbsp;Server name: **172.16.255.100**  
&nbsp;&nbsp;&nbsp;&nbsp;Port: **1521**  
&nbsp;&nbsp;&nbsp;&nbsp;User name: **system**  
&nbsp;&nbsp;&nbsp;&nbsp;Password: **qwert12345**  
&nbsp;&nbsp;&nbsp;&nbsp;SID: **orcl**  

Test endpoint connection (optional)에서 AWS VPC와 dms-aws를 선택하고 **Run test**를 클릭해 연결을 확인합니다.

### 7. Target Endpoints 생성  
7.1 DMS > **Endpoints** 선택하고 **Create Endpoint**버튼을 클릭합니다.  
7.2 아래 설정을 등록합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint type: **Target endpoint**   
&nbsp;&nbsp;&nbsp;&nbsp;Select RDS DB instance: **octainkdb-instance-1**  
&nbsp;&nbsp;&nbsp;&nbsp;Endpoint identifier: **target-aurora**  
&nbsp;&nbsp;&nbsp;&nbsp;User name: **admin**  
&nbsp;&nbsp;&nbsp;&nbsp;Password: **qwert12345**  

Test endpoint connection (optional)에서 AWS VPC와 dms-aws를 선택하고 **Run test**를 클릭해 연결을 확인합니다.


### 8. Migration Task 생성  
7.1 DMS > **Database migration tasks** 선택하고 **Create Task**버튼을 클릭합니다.  
7.2 아래 설정을 등록합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Task identifier: **shopdb-repl-task**   
&nbsp;&nbsp;&nbsp;&nbsp;Replication instance: **dms-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Source database endpoint: **source-oracle**  
&nbsp;&nbsp;&nbsp;&nbsp;Target database endpoint: **target-aurora**  
&nbsp;&nbsp;&nbsp;&nbsp;Migration type: **Migrate existing data and replicate ongoing changes**  
![](/images/lab2/dms_3.png#center)  
&nbsp;&nbsp;&nbsp;&nbsp;Enable validation: **Yes**   
![](/images/lab2/dms_4.png#center)  

**Table Mapping**
&nbsp;&nbsp;&nbsp;&nbsp;Editing mode: **Guided UI**   
 * Selection rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Schema: **Enter a schema**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Schema name: **WEBUSER**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Table name: **%**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Action: **include**  


### 9. 동기화 확인
9.1 Task 정상 동작을 확인합니다.  
![](/images/lab2/dms_5.png#center)
9.2 On-premise 어플리케이션에서 직접 데이터를 입력해 데이터가 정상적으로 복제되는지 확인합니다.  
![](/images/lab2/dms_6.png#center)

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
