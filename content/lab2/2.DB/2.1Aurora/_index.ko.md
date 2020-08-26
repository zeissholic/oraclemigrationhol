---
title: Aurora (MySQL) 생성
weight: 10
pre: "<b>2.1. </b>"
---

**Aurora (mysql호환)을 설치합니다.**

### 1. Amazon Aurora with MySQL compatibility 생성   
1.1 RDS > **Subnet groups** 메뉴를 선택하고 **Creat subnet group** 버튼을 클릭합니다.  
1.2 아래와 같이 입력 후 Create 버튼을 클릭합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **aws-db-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-db-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Availability Zones: **ap-northeast-2a, ap-northeast-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnets: **private-2a, private-2c**  
![](/images/lab2/rds_subnetg.png#center)  
1.3 RDS > **Parameter groups** 메뉴를 선택하고 **Creat parameter group** 버튼을 클릭합니다.  
아래와 같이 두개의 parameter group을 생성합니다.  
 * DB parameter Group  
&nbsp;&nbsp;&nbsp;&nbsp;Parameter group family: **aurora-mysql5.7**  
&nbsp;&nbsp;&nbsp;&nbsp;Type: **DB Parameter Group**  
&nbsp;&nbsp;&nbsp;&nbsp;Group name: **aws-aurora-dbpg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-aurora-dbpg**  
 * DB parameter Group  
&nbsp;&nbsp;&nbsp;&nbsp;Parameter group family: **aurora-mysql5.7**  
&nbsp;&nbsp;&nbsp;&nbsp;Type: **DB Cluster Parameter Group**  
&nbsp;&nbsp;&nbsp;&nbsp;Group name: **aws-aurora-dbcpg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-aurora-dbcpg**  

1.4 앞에서 생성한 **aws-aurora-dbcpg**에서 **lower_case_table_names**를 변경합니다.  
해당 Parameter group을 선택 후 상단에서 "lower_case_table_names"로 검색합니다.    
**Edit Parameter**버튼을 클릭하고 값을 **1**로 변경합니다.  
해당 파라미터는 static으로 적용을 위해서는 DB의 재시동을 필요로 합니다.  
![](/images/lab2/rds_pg.png#center)

또한 DB의 chracter set을 변경합니다.  
**character_set**으로 검색 후 모든 값을 **utf8mb4**로 지정하고 저장합니다.  
![](/images/lab2/rds_pg_2.png#center)

1.5 EC2 > **Security Groups** 메뉴를 선택하고 **Creat Security Group** 버튼을 클릭합니다.


1.6 RDS > **Databases** 메뉴를 선택하고 **Creat Database** 버튼을 클릭합니다.
아래와 같이 설정합니다.
 * Choose a database creation method: **Standard Create**  
 * Engine options
&nbsp;&nbsp;&nbsp;&nbsp;Engine type: **Amazon Aurora**  
&nbsp;&nbsp;&nbsp;&nbsp;Edition: **Amazon Aurora with MySQL compatibility**  
&nbsp;&nbsp;&nbsp;&nbsp;Version: **Aurora (MySQL5.7) 2.08.1**  
 * Templates: **Production**  
 * Settings
&nbsp;&nbsp;&nbsp;&nbsp;DB cluster identifier: **octankdb**  
&nbsp;&nbsp;&nbsp;&nbsp;Master passwor: **qwert12345**  
 * DB instance size: **db.r5.large**   
 * Availability & durability: **Don't create an Aurora Replica**   
 * Connectivity
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet group: **aws-db-subnetg**  
&nbsp;&nbsp;&nbsp;&nbsp;Publicly accessible: **No**  
&nbsp;&nbsp;&nbsp;&nbsp;Existing VPC security groups: **aws-db-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Availability Zone: **ap-northeast-2a**
 * Additional configuration
&nbsp;&nbsp;&nbsp;&nbsp;DB cluster parameter group: **aws-aurora-dbcpg**
&nbsp;&nbsp;&nbsp;&nbsp;DB parameter group: **aws-aurora-dbpg**
&nbsp;&nbsp;&nbsp;&nbsp;Encryption: **No**  
&nbsp;&nbsp;&nbsp;&nbsp;Performance Insights: **No**  
&nbsp;&nbsp;&nbsp;&nbsp;Monitoring: **No Enhanced monitoring**  
&nbsp;&nbsp;&nbsp;&nbsp;Maintenance: **No auto minor version upgrade**
&nbsp;&nbsp;&nbsp;&nbsp;Maintenance window: **Saturday 17:00 UTC 0.5hrs**
![](/images/lab2/rds.png#center)  


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
