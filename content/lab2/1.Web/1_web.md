---
title: Web 서버
weight: 10
pre: "<b>1. </b>"
---

**Cloud Endure(CE)를 이용해 Web서버를 마이그레이션 합니다..**     

### 1. Cloud Endure(CE) 계정 생성  
참고로, AWS로 마이그레이션의 경우 Cloud Endure 라이선스는 무료입니다.  
1.1 등록 페이지로 이동합니다. <a href="https://migration-register.cloudendure.com/">https://migration-register.cloudendure.com/</a>.  
1.2 개인 혹은 회사메일 및 암호를 등록하고 계정을 생성합니다.    
![](/OracleMigrationHoL/images/lab2/ce_register.png#center)
1.3 등록한 이메일로 발송되는 메일을 확인하고 안내에 따라 등록을 완료합니다.   

### 2. CE 로그인  
2.1 로그인 페이지로 이동합니다. <a href="https://console.cloudendure.com/#/signIn">https://console.cloudendure.com/#/signIn</a>. 
![](/OracleMigrationHoL/images/lab2/ce_login.png#center)

### 3. CE Project 생성  
3.1 좌측 상단의 + 버튼을 클릭해 프로젝트를 생성합니다. 
&nbsp;&nbsp;&nbsp;&nbsp;Project name: **web-migration**  
&nbsp;&nbsp;&nbsp;&nbsp;Project type: **Migration**   
![](/OracleMigrationHoL/images/lab2/ce_project.png#center)
3.2 IAM Credential 입력화면을 그대로 두고 새창 혹은 새탭을 열어 AWS 콘솔에서 다음을 작업합니다.


### 4. CE를 위한 IAM Policy 생성   
4.1 AWS 콘솔에서 **IAM** 메뉴를 선택합니다.  
4.2 좌측에서 **Policies**를 선택한 후 **Create Policy** 버튼을 클릭합니다.  
4.3 **JSON 탭**을 클릭하고 아래 링크의 내용을 복사해 대체 합니다.  
https://docs.cloudendure.com/Content/IAMPolicy.json  
Review 화면에서 Name, Description 모두 **CEPolicy**로 입력후 Create Policy 버튼을 클릭합니다

### 5. CE를 위한 IAM 계정 생성   
5.1 AWS 콘솔에서 **IAM** 메뉴를 선택합니다.  
5.2 좌측에서 **Users**를 선택한 후 **Create User** 버튼을 클릭합니다.  
5.3 아래 설정으로 IAM 계정을 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;User name: **CEUser**  
&nbsp;&nbsp;&nbsp;&nbsp;Access type: **Programmatic access만 선택**  
5.4 Set permissions 화면에서 Attach existing policies directly를 선택합니다.  
5.5 **CEPolicy** 선택하고 다음으로 넘어갑니다.  
5.6 Create User 클릭 후 화면에 표시되는 Credentials를 CE 콜솔에 입력후 저장합니다.
![](/OracleMigrationHoL/images/lab2/ce_iam.png#center)
![](/OracleMigrationHoL/images/lab2/ce_credential.png#center)

### 6. CE Replication Settings   
6.1 아래와 같이 설정합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Migration Source: **AWS Asia Pacific(Seoul)**  
&nbsp;&nbsp;&nbsp;&nbsp;Migration Target: **AWS Asia Pacific(Seoul)**  
&nbsp;&nbsp;&nbsp;&nbsp;Choose the default disk type...: **Use slover, low cost... 선택**  
&nbsp;&nbsp;&nbsp;&nbsp;Choose the subnet...: **AWS VPC의 Public-2a Sunet 선택**   
6.2 화면에 나오는 방법을 이용해 7번 단계를 수행합니다.

### 7. CE Agent 설치
On-premise의 Web서버에 CE Agent를 설치합니다.   
7.1 Cloud9 화면으로 돌아가 아래 명령으로 On-premise Web서버에 연결합니다.
```
ssh -i migrationhol.pem centos@172.16.1.10
```
7.2 로그인 후 CE콘솔의 안내에 따라 Agent를 설치합니다.
![](/OracleMigrationHoL/images/lab2/ce_agent.png#center)
![](/OracleMigrationHoL/images/lab2/ce_agent_2.png#center)
7.3 CE 콘솔에서 on-premise-web이 등록된 것을 확인 합니다.
![](/OracleMigrationHoL/images/lab2/ce_web_registered.png#center)
7.4 잠시 후 해당 머신을 선택하고 Initial Sync가 진행중임을 확인합니다.  
![](/OracleMigrationHoL/images/lab2/ce_web_replication.png#center)

### 8. CE Blueprint 작성
동기화가 진행되는 동안 BluePrint를 작성합니다.   
8.1 Blueprint는 동기화된 서버가 AWS상에서 생성될 환경 및 사양을 정의합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Machine type: **t2.micro**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **AWS VPC / public-2a Subnet**  
&nbsp;&nbsp;&nbsp;&nbsp;Security group: **aws-web-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Private IP: **custom**  
&nbsp;&nbsp;&nbsp;&nbsp;Private IP address: **10.254.1.10**  
&nbsp;&nbsp;&nbsp;&nbsp;Disks: **SSD** 
![](/OracleMigrationHoL/images/lab2/ce_bp.png#center)

8.2 동기화가 완료될 때까지 WAS 마이그레이션을 수행합니다.

### 9. CE Test Mode로 타켓 생성
9.1 CE 콘솔에서 **on-premise-web**을 선택하고 우측 상단에서 **Launch...** 버튼을 선택하고 **Test Mode**로 실행합니다
![](/OracleMigrationHoL/images/lab2/ce_test.png#center)
9.2 좌측의 **Job Progress**를 클릭해 현재 진행 상태를 확인합니다.
![](/OracleMigrationHoL/images/lab2/ce_job.png#center)
9.3 작업이 완료되었음을 확인 합니다.
![](/OracleMigrationHoL/images/lab2/ce_job_2.png#center)
9.4 Private IP가 10.254.1.10인 서버의 이름을 **aws-web-2a**로 변경합니다.
![](/OracleMigrationHoL/images/lab2/ce_web_2.png#center)


### 10. aws-web-2a에 로그인  
10.1 AWS VPC를 위한 Cloud9에서 **10.254.1.10** 에 아래와 같이 연결합니다.  
```
ssh -i migrationhol.pem centos@10.254.1.10  
```
10.2 Nginx 서비스를 가동합니다.  
```
sudo systemctl start nginx
```

### 11. Nginx 화면 확인
11.1 aws-web-2a의 Public IP로 웹브라우저에서 접속해 Nginx화면이 뜨는지 확인합니다. 
현재 WAS서버가 없어 화면이 timeout걸리고 에러화면이 뜨게 됩니다. 이는 현재 상황에서 정상적인 화면입니다. 
![](/OracleMigrationHoL/images/lab2/nginx_error.png#center)

### 12. aws-web-2a 및 CE Project 삭제
성공적으로 복제됨을 확인했습니다. 하지만 일반적인 Web서버는 SSL인증서 및 정적파일의 캐싱용도로 사용되므로 이를 제거하고 CloudFront(CDN)을 사용하는 것으로 변경합니다.
12.1 aws-web-2a 인스턴스를 Termination 합니다.
12.2 CE Project를 삭제합니다.


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
