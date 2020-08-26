---
title: EC2 인스턴스 생성
weight: 10
pre: "<b>3.1. </b>"
---

### 1. EC2 인스턴스 생성  
1.1 EC2 > **Instances** 메뉴에서 **Launch Instance** 버튼을 클릭합니다.  
1.2 **Marketplace**에서 **CentOS 7 (x86_64) - with Updates HVM**을 선택합니다. 
![](/OracleMigrationHoL/images/lab2/was_ec2_1.png#center) 
1.3 **t3.micro** 인스턴스 타입을 선택합니다.  
1.4 상세 정보에서 아래와 같이 설정합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Network: **AWS VPC**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **private-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;Auto-assign Public IP: **diable**  
&nbsp;&nbsp;&nbsp;&nbsp;Private IP: **10.254.101.10**  
1.5 스토리지는 **40GiB**로 변경합니다.  
1.6 Tag는 Name을 **aws-was-2a**로 지정합니다.  
1.7 Security Group은 기존에 생성한 **aws-was-sg**를 선택합니다.   
1.8 Key paire는 **migrationhol**을 선택하고 인스턴스를 생성합니다.  

### 2. WAS서버 연결 
2.1 AWS VPC용 Cloud9을 엽니다.  
2.2 아래 명령으로 서버에 로그인합니다. 
```
ssh -i migrationhol.pem centos@10.254.101.10
```
2.3 timezone 변경. 
```
sudo timedatectl list-timezones | grep Seoul
sudo timedatectl set-timezone Asia/Seoul
date
```
2.4 방화벽 작업
```
sudo yum install firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --zone=public --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
