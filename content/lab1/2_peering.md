---
title: VPC Peering 구성
weight: 20
pre: "<b>2. </b>"
---

**1에서 생성한 On-premise VPC와 2에서 생성한 AWC VPC를 VPC Peering을 연결합니다.**     
실제환경에서는 VPN혹은 Direct Connection 등으로 네트워크 구성이 가능하나 핸즈온 환경을 고려해 VPC Peering으로 연결합니다.  

### 1. VPC Peering 생성  
1.1 VPC메뉴를 클릭하고 좌측의 **Peering Connections**를 선택하고 **Create Peering Connection**버튼을 클릭합니다.  
아래와 같이 설정하고 Create 버튼을 클릭합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Peering connection name tag : **px-onprem-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;VPC (Requester) : **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;VPC (Accepter) : **on-premise**  
![](/OracleMigrationHoL/images/lab1/vpc_peering_1.png#center)

1.2 좌측의 **Filter by VPC**에서 **on-premise**를 선택합니다.
![](/OracleMigrationHoL/images/lab1/vpc_onprem.png#center)

1.2 좌측의 **Peering Connections**메뉴에서 **Actions**를 **Accept Request**를 선택합니다.
![](/OracleMigrationHoL/images/lab1/vpc_peering_2.png#center)
![](/OracleMigrationHoL/images/lab1/vpc_peering_3.png#center)

### 2. Route Table 수정  
2.1 VPC메뉴에서 on-premise VPC로 Filtering 적용합니다.  
2.2 두개의 Route Table에 아래 route를 추가합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Destination : **10.254.0.0/16**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **peering connection**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **px-onprem-aws**  
2.3 VPC메뉴에서 AWS VPC로 Filtering 적용합니다.  
2.4 세개의 Route Table에 아래 route를 추가합니다. (public-route, private-route-2a/2c)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Destination : **172.16.0.0/16**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **peering connection**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Target : **px-onprem-aws**  

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
