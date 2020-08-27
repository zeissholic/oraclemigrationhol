---
title: WAS 서버 구성
weight: 40
pre: "<b>4. </b>"
---

**공유된 AMI를 통해 On-premise인프라 중 WAS 서버를 구성합니다.**   
실습은 한국 리전에서 진행합니다. 현재 리전이 **Asia Pacific (Seoul):ap-northeast-2**인지 확인합니다.

### 1. WAS 서버 생성  
1.1 EC2 > Images > **AMIs** 메뉴에서 Owned by me 드랍다운을 **Private Images**로 변경해 **공유된 AMI**를 확인합니다.  
1.2 **on-premise-was-hol**를 선택하고 Actions메뉴의 **Launch**를 선택합니다.  
1.3 Instance 설정  
 * Instance type: **t2.micro**
 * Instance Details:  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;VPC: **on-premise**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **Private**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Enable termination protection: **Check**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Primary IP: **172.16.255.10**    


1.4 Storage 설정  
 * Size(GB): **40**  

1.5 Tag 설정  
 * Name: **on-premise-was**  

1.6 Security Group 설정  
 * Name: **on-premise-was-sg**  
 * Description: **on-premise-was-sg**  
 * Rule:    
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 8080 : *web 서버의 Security group* : WEB**    
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 8080 : *cloud9의 Security group* : Cloud9**     
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 22 : *cloud9의 Security group* : Cloud9**    
![](/images/lab0/launch_was-sg.png#center)  
1.7 Key pair는 앞에서 생성한 key pair, **migrationhol**를 선택하고 생성합니다  
1.8 만약 Marketplace에러가 발생할 경우 안내하는 URL로 이동 후 구독  (이는 CentOS구독을 위한 것 입니다.)     

### 2. WAS 서버 연결  
2.1 Cloud9 에서 **New Terminal**을 선택하고 새로운 탭에서 연결 수행합니다  
![](/images/lab0/cloud9_new.png#center)  

2.3 Cloud9에서 WAS 서버에 접속합니다.  
```
ssh -i migrationhol.pem centos@172.16.255.10
```

### 3. Tomcat 서버 구동  
3.1 Tomcat 서버 start.  
```
sudo systemctl start tomcat

```


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
