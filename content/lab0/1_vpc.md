---
title: VPC 구성
weight: 10
pre: "<b>1. </b>"
---

**공유된 AMI를 통해 On-premise인프라를 구성합니다.**   
실습은 한국 리전에서 진행합니다. 현재 리전이 **Asia Pacific (Seoul):ap-northeast-2**인지 확인합니다.

### 1. Keypair 생성  
1.1 EC2 > Network & Security > Key Pairs 메뉴에서 **Create Key Pair**를 클릭합니다.  
1.2 Key pair name은 **migrationhol**으로 입력하고 File format은 **pem**을 선택하고 Create key pair를 클릭해 keypair를 생성합니다.
![keypair](/OracleMigrationHoL/images/lab0/keypair.png#center)

### 2. VPC 생성  
2.1 VPC메뉴를 클릭하고 상단의 **Launch VPC Wizard**를 클릭합니다.  
2.2 **VPC with Public and Private Subnets**를 선택하고 **Select**버튼을 클릭합니다.
2.3 VPC 설정을 아래와 같이 입력합니다.  
 * IPv4 CIDR block: **172.16.0.0/16**  
 * VPC name: **on-premise**  
 * Public subnet's CIDR block: **172.16.1.0/24**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Availability Zone: **ap-northeast-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Public subnet name: **DMZ**  
 * Private subnet's CIDR block: **172.16.255.0/24**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Availability Zone: **ap-northeast-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Public subnet name: **Private**  

2.4 NAT는 **Use a NAT instance instead**를 클릭하고 기본 사양을 사용합니다.  
2.5 VPC 생성이 완료될 때까지 기다린 후 다음 과정을 진행하십시오.

아래 이미지의 설정을 참고하십시오.
![keypair](/OracleMigrationHoL/images/lab0/vpc.png#center)

2.6 VPC와 Subnet의 ID를 기록합니다.
![](/OracleMigrationHoL/images/lab0/vpc_id.png#center)
![](/OracleMigrationHoL/images/lab0/subnet_id.png#center)


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.

