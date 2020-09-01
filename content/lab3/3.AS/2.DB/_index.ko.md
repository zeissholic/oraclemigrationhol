---
title: Database
weight: 20
pre: "<b>4.3.2 </b>"
---

**Aurora DB에 대한 Multi-AZ 및 Auto scaling 작업을 적용합니다.** 
{{% notice info %}}
현재 핸즈온에서 사용하는 샘플 어플리케이션은 실제 DB에 큰 부하를 발생시키기 어려우므로 실제 부하시 scale-out되지 않을 수도 있습니다.
{{% /notice %}}

### 1. Aurora Read Replica 생성 
1.1 AWS 콘솔에서 **RDS** 메뉴로 들어간 후 좌측의 **Databases** 메뉴를 선택한 후 **octankdb-instance-1**를 선택합니다.   

1.2 Connectivity & Security탭에서 Networking 항목에서 AZ를 확인합니다. 현재 2a에 생성된 것을 확인할 수 있습니다.
![](/images/lab3/asg_4.png#center)  

1.3 **octankdb**를 선택하고 우측 상단의 **Actions**에서 **add reader**를 선택합니다.
![](/images/lab3/asg_5.png#center)  

1.4 아래와 같이 설정하고 생성합니다.
&nbsp;&nbsp;&nbsp;&nbsp;Availability zone: **ap-northeast-2c**   
&nbsp;&nbsp;&nbsp;&nbsp;Publicly accessible: **No**  
&nbsp;&nbsp;&nbsp;&nbsp;DB instance identifier: **octankdb-reader0**  
&nbsp;&nbsp;&nbsp;&nbsp;Failover Priority: **tier-0**   
&nbsp;&nbsp;&nbsp;&nbsp;DB parameter group: **aws-aurora-dbpg**  

생성되는 동안 다음 과정을 수행합니다.

### 2. Aurora Auto Scaling 설정 
2.1 좌측의 **Databases** 메뉴를 선택한 후 **octankdb**를 선택합니다.   

2.2 아래와 같이 설정하고 생성합니다.
&nbsp;&nbsp;&nbsp;&nbsp;Policy name: **asg-octankdb**    
&nbsp;&nbsp;&nbsp;&nbsp;Target metric: **Average CPU utilization of Aurora Replicas**  
&nbsp;&nbsp;&nbsp;&nbsp;Target value: **50**  
&nbsp;&nbsp;&nbsp;&nbsp;Maximum capacity: **2**   

Reader 생성이 완료되어야 해당 설정 저장이 가능합니다.



---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



