---
title: CodePipeline
weight: 30
pre: "<b>4.4.3 </b>"
---

**CodePipeline을 구성합니다.** <br/><br/>

### 1. Codepipeline 생성  
1.1 AWS 콘솔에서 **Codepipeline**으로 이동 후 우측 상단의 **Create pipeline**을 클릭합니다.   

### 2. Step 1 Choose pipeline settings 
2.1 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Pipeline name: **ppl-mbpshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Service role: **New Service role**   
![](/images/lab3/ppl_1.png#center)  

### 3. Step 2 Add source stage  
3.1 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Source provider: **AWS CodeCommit**   
&nbsp;&nbsp;&nbsp;&nbsp;Repository name: **mpbshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Branch name: **master**   
![](/images/lab3/ppl_2.png#center)  


### 4. Step 3 Add build stage 
4.1 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Build provider: **AWS CodeBuild**   
&nbsp;&nbsp;&nbsp;&nbsp;Region: **Asia Pacific (Seoul)**   
&nbsp;&nbsp;&nbsp;&nbsp;Project name: 우측의 **Create project** 클릭   
![](/images/lab3/ppl_3.png#center)  

4.2 팝업이 뜨면 아래 내용을 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Project name: **cb-mbpshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Environment image: **Managed Image**   
&nbsp;&nbsp;&nbsp;&nbsp;Operating system: **ubuntu**   
&nbsp;&nbsp;&nbsp;&nbsp;Runtime(s): **Standard**   
&nbsp;&nbsp;&nbsp;&nbsp;Image(s): **aws/codebuild/standard:3.0**   
&nbsp;&nbsp;&nbsp;&nbsp;Service role: **New sercive role**   

 * Additional configuration에서  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**   
&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **private-2a, private-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;Security groups: **default**   

Validate VPC settings 버튼을 클릭해 인터넷 연결이 가능한지 확인합니다.   
Project를 생성합니다.   

4.3 팝업이 닫히고 이전 화면으로 돌아오면 Next를 클릭합니다.  
![](/images/lab3/ppl_4.png#center)  


### 5. Step 4 Add deploy stage  
5.1 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Deploy provider: **AWS CodeDeploy**   
&nbsp;&nbsp;&nbsp;&nbsp;Region: **Asia Pacific (Seoul)**  
&nbsp;&nbsp;&nbsp;&nbsp;Application name: **mbpshop**  
&nbsp;&nbsp;&nbsp;&nbsp;Deployment group: **dg-mbpshop**  
5.2 내용을 확인하고 CodePipeline을 생성합니다.  


### 6. 에러 처리  
6.1 Insufficient permissions
The provided role cannot be assumed: **'Access denied when attempting to assume the role 'arn:aws:iam::567701417813:role/service-role/AWSCodePipelineServiceRole-ap-northeast-2-ppl_mbpshop'**.  
IAM > Role > AWSCodePipelineServiceRole-ap-northeast-2-ppl_mbpshop 에서 trusted relationship을 아래와같이 변경
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
        "codepipeline.amazonaws.com",
          "codebuild.amazonaws.com",
          "codepipeline.amazonaws.com",
          "codedeploy.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

6.2 DownloadBundle > UnknownError  
![](/images/lab3/ppl_error_1.png#center)   
이 경우 EC2서버에 연결된 IAM Role (**mbpshop_role**)에 CodeBuild가 S3에 저장한 버킷에 접근권한을 부여합니다.  
해당롤에 Inline Policy로 아래와 같이 추가 합니다. S3버킷명은 변경하셔야 합니다.  
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:GetObjectAcl",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::codepipeline-ap-northeast-2-7490EXAMPL/*"
        }
    ]
}
```



---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



