---
title: Web 서버 구성
weight: 30
pre: "<b>3. </b>"
---

**공유된 AMI를 통해 On-premise인프라 중 Web 서버를 구성합니다.**   
실습은 한국 리전에서 진행합니다. 현재 리전이 **Asia Pacific (Seoul):ap-northeast-2**인지 확인합니다.  

### 1. 웹서버 생성  
1.1 EC2 > Images > **AMIs** 메뉴에서 Owned by me 드랍다운을 **Private Images**로 변경해 **공유된 AMI**를 확인합니다.  
![keypair](/images/lab0/shared_ami.png#center)  
1.2 **on-premise-web-hol**를 선택하고 Actions메뉴의 **Launch**를 선택합니다.  
![keypair](/images/lab0/launch_web0.png#center)  
1.3 Instance 설정  
 * Instance type: **t2.micro**
 * Instance Details:  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;VPC: **on-premise**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **DMZ**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Auto-assign Public IP: **Enable**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Enable termination protection: **Check**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Primary IP: **172.16.1.10**    
![keypair](/images/lab0/launch_web1.png#center)

1.4 Storage 설정  
 * Size(GB): **40**  

1.5 Tag 설정  
 * Name: **on-premise-web**  

1.6 Security Group 설정  
 * Name: **on-premise-web-sg**  
 * Description: **on-premise-web-sg**  
 * Rule:    
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 22 : *My IP* : My IP**  
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 80 : 0.0.0.0/0 : HTTP**    
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 22 : *cloud9의 Security group* : Cloud9**    
![keypair](/images/lab0/launch_web-sg.png#center)  
3.6 Key pair는 앞에서 생성한 key pair, **migrationhol**를 선택하고 생성  
3.7 만약 Marketplace에러가 발생할 경우 안내하는 URL로 이동 후 구독  (이는 CentOS구독을 위한 것 입니다.)     

### 2. Web 서버 연결  
2.1 Cloud9에서 web서버에 접속합니다.  
```
ssh -i migrationhol.pem centos@172.16.1.10
```

### 3. Nginx 서버 구동  
3.1 Nginx 서버 start.  
```
sudo systemctl start nginx  

```


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
