---
title: Security Group 생성
weight: 40
pre: "<b>4. </b>"
---

**필요한 Security Group을 생성합니다.**   
### 1. ALB용 Security Group 생성
1.1 EC2 > Security Groups로 이동해 새로운 Web서버를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-alb-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-alb-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 80 : 0.0.0.0/0 : HTTP**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 443 : 0.0.0.0/0 : HTTPS**  

### 2. Web 서버용 Security Group 생성
2.1 EC2 > Security Groups로 이동해 새로운 Web서버를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-web-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-web-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 80 : 0.0.0.0/0 : HTTP**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 22 : AWS VPC용 Cloud9의 Security Group : Cloud9**  


### 3. WAS 서버용 Security Group 생성
3.1 EC2 > Security Groups로 이동해 새로운 WAS서버를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-was-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-was-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 8080 : *Web 서버용 Security Group* : Web**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 8080 : aws-alb-sg : ALB**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 22 : AWS VPC용 Cloud9의 Security Group : Cloud9**  


### 4. Oracle DB 서버용 Security Group 생성
4.1 EC2 > Security Groups로 이동해 새로운 Oracle DB서버를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-db-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-db-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 3306 : *WAS 서버용 Security Group* : WAS**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 3306 : AWS VPC용 Cloud9의 Security Group : Cloud9**  


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
