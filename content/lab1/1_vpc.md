---
title: VPC 구성
weight: 10
pre: "<b>1. </b>"
---

**마이그레이션을 위한 새로운 VPC를 생성합니다.**     
실습은 한국 리전에서 진행합니다. 현재 리전이 **Asia Pacific (Seoul):ap-northeast-2**인지 확인합니다.

### 1. VPC 생성  
이번 단계에서는 기존의 마법사를 사용하지 않고 수동으로 직접 생성합니다.  
1.1 VPC메뉴를 클릭하고 좌측의 **Your VPCs**를 선택하고 **Create VPC**버튼을 클릭합니다.  
1.2 **VPC with Public and Private Subnets**를 선택하고 **Select**버튼을 클릭합니다.  
아래와 같이 입력 후 Create 버튼을 클릭합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Name: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IPv4 CIDR block: **10.254.0.0/16**  
![keypair](/images/lab1/vpc_create.png#center)

1.3 사용의 편의성을 위해 앞에서 생성된 VPC만 보이도록 filtering합니다.
![keypair](/images/lab1/vpc_filter.png#center)

### 2. Subnet 생성
2.1 좌측의 **Subnets** 메뉴를 클릭하고 Subnet을 생성합니다.
Public 2개, Private 2개의 Subnet을 생성하며 각각 AZ-a, AZ-c에 분산 생성합니다.
 * Public subnet:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;public-2a : ap-northeast-2a : **10.254.1.0/24**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;public-2c : ap-northeast-2c : **10.254.2.0/24**  
 * Private subnet    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;private-2a : ap-northeast-2a : **10.254.101.0/24**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;private-2c : ap-northeast-2c : **10.254.102.0/24**  
![](/images/lab1/vpc_subnet_create.png#center)
![](/images/lab1/vpc_subnets.png#center)

### 3. Internet G/W 생성   
3.1 좌측의 **Internet Gateways** 메뉴를 클릭하고 Create 버튼을 클릭합니다.  
3.2 Name tag에 **igw-aws**를 입력하고 Create internet gateway버튼을 클릭합니다.  
3.3 다음 화면에서 **Actions** 메뉴에서 **Attach to VPC**를 선택하여 AWS VPC와 연결합니다.   
![](/images/lab1/vpc_igw.png#center)

### 4. NAT G/W 생성   
4.1 좌측의 **Elastic IPs** 메뉴를 선택하고 **Allocate Elastic IP address** 버튼을 클릭하여 두개의 Public IP를 추가합니다.  
4.1 좌측의 **NAT Gateways** 메뉴를 선택하고  **Create NAT gateway** 버튼을 클릭합니다.  
두개의 NAT G/W를 생성합니다.  
 * ngw-aws-2a:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **public-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EIP : **앞단계에서 부여 받은 Public IP**  
 * ngw-aws-2c    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **public-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EIP : **앞단계에서 부여 받은 Public IP**   
![](/images/lab1/vpc_nat_2a.png#center)

### 5. Route Tables 생성   
5.1 좌측의 **Route Tables** 메뉴를 클릭하고 Route Table을 생성합니다.   
Public subnet용 Route Table을 생성합니다.
![](/images/lab1/vpc_rt_public.png#center)  

5.2 **public-route**을 선택하고 하단의 **Routes 탭**을 선택하고 **Edit Routes**를 선택합니다.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Destination: **0.0.0.0/0**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **Internet Gateway선택 후 앞에서 생성한 igw-aws**선택   
![](/images/lab1/vpc_rt_public_add.png#center)

5.3 동일한 방법으로 아래 Route Table 추가합니다
 * private-route-2a:   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Destination: **0.0.0.0/0**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **NAT Gateway선택 후 앞에서 생성한 ngw-aws-2a**선택   
 * private-route-2c    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Destination: **0.0.0.0/0**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **NAT Gateway선택 후 앞에서 생성한 ngw-aws-2c**선택   

5.3 Route Table과 Subnet을 연결합니다.
아래와 같이 연결을 생성합니다.
 * public-route: **public-2a, public-2c**  
 * private-route-2a: **private-2a**  
 * private-route-2c: **private-2c**  
![](/images/lab1/vpc_rt_public_acc.png#center)


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
