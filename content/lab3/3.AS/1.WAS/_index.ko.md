---
title: WAS
weight: 10
pre: "<b>4.3.1 </b>"
---

**Spot 인스턴스를 활용해 비용을 절약할 수 있는 Auto Scaling을 적용합니다. 또한 현재 단일 AZ로 구성된 인프라를 다중 AZ 구성으로 변경합니다** <br/><br/>


### 1. WAS AMI 생성 
1.1 AWS 콘솔에서 **EC2** 메뉴로 들어간 후 좌측의 **Instances** 메뉴를 선택한 후 **aws-was-2a** 서버를 선택합니다.   

1.2 마우스 왼쪽 클릭 혹은 상단의 Actions 메뉴에서 **Image > Create Image** 를 선택합니다.   
![](/images/lab3/asg_1.png#center)  

1.3 팝업화면에서 아래 내용을 입려하고 AMI를 생성합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Image name: **aws-was-2a**   
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-was-2a**     
&nbsp;&nbsp;&nbsp;&nbsp;No reboot: **선택**  
![](/images/lab3/asg_2.png#center)  
1.4 좌측의 **AMIs** 메뉴에서 상태가 **available**이 될때까지 기다립니다.   

### 2. Auto Scaling 생성 
2.1 좌측 메뉴에서 Instances > **Launch Templates** 을 선택하고 **Create Launch Templates** 버튼을 클릭합니다. 

2.2 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Launch template name: **lt-aws-was**     
&nbsp;&nbsp;&nbsp;&nbsp;Auto Scaling guidance: **선택**   
&nbsp;&nbsp;&nbsp;&nbsp;AMI: **aws-was-2a**   
&nbsp;&nbsp;&nbsp;&nbsp;Instance type: **t3.micro**   
&nbsp;&nbsp;&nbsp;&nbsp;Keypair: **migrationhol**  
&nbsp;&nbsp;&nbsp;&nbsp;Security group: **aws-was-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Advanced details에서  
&nbsp;&nbsp;&nbsp;&nbsp;IAM instance profile: **mbpshop_role**  
&nbsp;&nbsp;&nbsp;&nbsp;Detailed CloudWatch monitoring: **Enable**  


2.3 다음화면에서 가운에 **Create Auto Scaling group** 링크를 클릭합니다.  

2.4 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Auto Scaling group name: **asg-aws-was**   
&nbsp;&nbsp;&nbsp;&nbsp;Launch template: **lt-aws-was** 선택 확인  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**   

2.5 **Configure settings** 에서 아래를 입력합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Purchase options and instance types: 우측의 **Combine purchase options and instance types** 선택   
&nbsp;&nbsp;&nbsp;&nbsp;Optional On-Demand base: **2**  
&nbsp;&nbsp;&nbsp;&nbsp;On-Demand percentage above base: On-demand (**0**), Spot (**100**)  
&nbsp;&nbsp;&nbsp;&nbsp;Instance types: **t3.micro, t3.small, t3a.small, t3a.micro** 만 남기고 제거  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**   
&nbsp;&nbsp;&nbsp;&nbsp;Subnets: **private-2a, private-2c**   
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

2.6 **Configure advanced options** 에서 아래를 입력합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Enable load balancing: **체크** 후  
&nbsp;&nbsp;&nbsp;&nbsp;Choose a target group for your load balancer: **tg-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Health check type: **ELB** 선택  
&nbsp;&nbsp;&nbsp;&nbsp;Enable group metrics collection within CloudWatch: **체크**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

2.7 **Configure group size and scaling policies** 에서 아래를 입력합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Desired capacity: **1**  
&nbsp;&nbsp;&nbsp;&nbsp;Minimum capacity: **1**  
&nbsp;&nbsp;&nbsp;&nbsp;Maximum capacity: **3**  
&nbsp;&nbsp;&nbsp;&nbsp;Scaling policies: **Target tracking scaling policy** 선택  
&nbsp;&nbsp;&nbsp;&nbsp;Target value: **5** 원활한 핸즈온 진행을 위해 낮은 수치 입력  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

2.8 **Add tags** 에서 아래를 입력합니다.   
&nbsp;&nbsp;&nbsp;&nbsp;Resource tags: key (**Name**), value (**aws-was**)   
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT** 
&nbsp;&nbsp;&nbsp;&nbsp;**Review**후 생성  (수분 소요가능)

2.9 정상적으로 서버가 생성되고 동작하는지 확인합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;좌측의 **Target Groups**에서 **tg-aws**를 선택하고, 하단에서 **Targets** 탭을 선택합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;아래 **aws-was**가 **healthy**한 상태인지 확인합니다.  
![](/images/lab3/asg_3.png#center) 

### 2. 화면 확인 
2.1 앞에서 생성한 CloudFront 주소로 접속해 브라우저를 리프레시 하면서 아래에 IP가 변경되는 지 확인합니다.   

2.2 로그인 후 동일하게 리프레시 하면서 아래에 IP가 변경되는 지 확인합니다. (ElastiCache를 이용한 세션 확인)  




---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



