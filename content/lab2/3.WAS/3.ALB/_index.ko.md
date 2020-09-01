---
title: ALB 구성
weight: 30
pre: "<b>3.3. </b>"
---

### 1. ALB 생성
1.1 AWS 콘솔에서 EC2 > **Load Balance** 아래 Load Balancers를 선택하고 **Create Load Balancer**버튼을 클릭합니다.  
1.2 **Application Load Balancer** 선택합니다.   
1.2 ALB설정을 아래와 같이 적용합니다  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **alb-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Scheme: **internet-facing**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;AZ: **public-2a, public-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;Security Group: **aws-alb-sg**  
1.3 Target Group 설정을 아래와 같이 적용합니다  
&nbsp;&nbsp;&nbsp;&nbsp;Target group: **New Target group**  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **tg-aws**  
&nbsp;&nbsp;&nbsp;&nbsp;Target type: **instance**  
&nbsp;&nbsp;&nbsp;&nbsp;Health check path: **/shop/healthcheck**  
&nbsp;&nbsp;&nbsp;&nbsp;Port: **8080**  
&nbsp;&nbsp;&nbsp;&nbsp;Protocol: **HTTP**  
1.4 다음 화면에서 하단의 **aws-was-2a**를 선택 후 중앙의 **Add to registered** 버튼을 클릭하여 등록합니다.   
![](/images/lab2/alb_1.png#center)   

### 2. ALB 연결 확인  
2.1 AWS 콘솔에서 EC2 > **Load Balance** 아래에서 **alb-aws**를 선택하고 아래 Description 탭에서 **DNS name**을 확인하고 복사합니다.  
2.2 브라우저에서 주소창에 붙여넣은 후 아래 화면이 뜨는지 확인합니다.   
![](/images/lab2/was_tomcat_2.png#center)


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
