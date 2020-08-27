---
title: CodeDeploy
weight: 20
pre: "<b>4.4.2 </b>"
---

**CodeDeploy를 구성합니다.** <br/><br/>

### 0. CodeDeploy Service Role 생성  
0.1 AWS 콘솔에서 **IAM** 메뉴로 이동하여 좌측의 **Roles** 메뉴를 선택합니다.  
0.2 **Create role** 버튼을 클릭합니다.  
0.3 **AWS service**를 선택하고 아래에서 **EC2**를 선택합니다. (Next)  
0.4 **AWSCodeDeployRole**로 Policy를 검색하고 **AWSCodeDeployRole**를 선택합니다.  
0.5 Role name은 **mbpshop_role**로 지정하고 저장합니다.  
0.6 생성된 Role의 ARN을 기록합니다.  
![](/images/lab3/iam_1.png#center)  
0.7 해당 Role의 Trust **relationship** 탭에서 **Edit trust relationship**을 클릭하고 아래와 같이 변경합니다.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "codedeploy.amazonaws.com",
          "ec2.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
0.8 생성된 Role을 **aws-was-2a**에 적용합니다.  
![](/images/lab3/ec2_1.png#center)   


### 1. Applications 생성  
1.1 AWS 콘솔에서 **CodeDeploy**으로 이동 후 좌측메뉴에서 **Applications**을 선택하고 **Create application** 버튼을 클릭합니다.   
1.2 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Application name: **mbpshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Compute platform: **EC2/On-premises**   
![](/images/lab3/cd_1.png#center)  


### 2. Deployment group 생성  
2.1 좌측 메뉴에서 **Applications**를 선택하고 방금 생성한 **mbpshop**을 클릭합니다.  
2.2 하단의 **Deployment groups** 탭에서 **Create deployment group** 버튼을 클릭합니다.  
2.3 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Enter a deployment group name: **dg-mbpshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Service Role: **위에서 생성한 mbpshop_role의 ARN**   
&nbsp;&nbsp;&nbsp;&nbsp;Deployment type: **In-place**   
* Environment configuration:  
&nbsp;&nbsp;&nbsp;&nbsp;**Amazon EC2 instances**를 선택  
&nbsp;&nbsp;&nbsp;&nbsp;Tag에서 Key는 **Name**, Value는 **aws-was-2a**를 선택하고 **Add tag** 버튼을 클릭  
&nbsp;&nbsp;&nbsp;&nbsp;**1 unique matched instance**를 확인합니다.
* Load balancer:  이 단계에서는 선택하지 않음
![](/images/lab3/cd_2.png#center75)  


### 3. CodeDeploy Agent 설치 
3.1 AWS 콘솔에서 AWS용 Cloud9을 실행하고 aws-was-2a에 로그인 합니다.
```
ssh -i migrationhol.pem centos@10.254.101.10
```
```
sudo yum update -y
sudo yum install ruby wget -y
sudo cd /tmp
wget https://aws-codedeploy-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/latest/install

sudo chmod +x ./install
sudo ./install auto
```
```
sudo systemctl status codedeploy-agent
```



---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



