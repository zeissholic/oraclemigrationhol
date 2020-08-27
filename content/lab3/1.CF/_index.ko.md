---
title: CloudFront 적용
weight: 10
pre: "<b>4.1 </b>"
---

**기존 웹서버를 CloudFront로(CF) 대체합니다.** <br/><br/>
{{% notice info %}}
웹서버가 SSL 및 정적파일의 캐싱용도로만 사용된다면 CloudFront로 변경가능합니다. 이 과정에서는 동적, 정적파일 모두를 CF를 통해서 서비스하도록 설정합니다. CF는 글로벌 서비스로 만약 **Certificate Manager**로 SSL인증서를 발급할 경우 **N.Virginia:us-east-1**에서 작업해야합니다. 해당 실습에서는 SSL인증서 발급 및 적용은 제외합니다.
{{% /notice %}}

### 1. Cloudfront 생성   
1.1 AWS 콘솔에서 **CloudFront** 메뉴로 이동합니다.   
1.2 좌측의 **Distributions**로 이동하고 **Creat Distribution** 버튼을 클릭합니다.  
1.3 상단의 **Web**에서 **Get Started** 버튼을 클릭합니다.   
1.4 아래의 설정으로 입력합니다.  
 * Origin Settings  
&nbsp;&nbsp;&nbsp;&nbsp;Origin Domain Name: **aws-alb** (리스트에서 보이지 않을 경우 ALB의 DNS name을 입력합니다.)  
&nbsp;&nbsp;&nbsp;&nbsp;Origin Protocol Policy: **HTTP only**  
&nbsp;&nbsp;&nbsp;&nbsp;HTTP Port: **80**  
 *  Default Cache Behavior Settings  
&nbsp;&nbsp;&nbsp;&nbsp;Viewer Protocol Policy: **Redirect HTTP to HTTPS**  
&nbsp;&nbsp;&nbsp;&nbsp;Allowed HTTP Methods: **GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE**  
&nbsp;&nbsp;&nbsp;&nbsp;Cache and origin request settings: **Use legacy cache settings**  
&nbsp;&nbsp;&nbsp;&nbsp;Object Caching: **Customize**  
&nbsp;&nbsp;&nbsp;&nbsp;Minimum TTL: **0**  
&nbsp;&nbsp;&nbsp;&nbsp;Maximum TTL: **0**  
&nbsp;&nbsp;&nbsp;&nbsp;Default TTL: **0**  
&nbsp;&nbsp;&nbsp;&nbsp;Forward Cookies: **all**  
&nbsp;&nbsp;&nbsp;&nbsp;Query String Forwarding and Caching: **Forwarded all, cache based on all**  
![](/images/lab3/cf_1.png#center)  
![](/images/lab3/cf_2.png#center)  
![](/images/lab3/cf_3.png#center)  
1.5 CF의 상태가 완료될 때까지 조금 기다립니다.  
![](/images/lab3/cf_4.png#center)  

### 2. Behaviors 추가   
정적파일 캐싱을 위한 설정을 추가합니다.  
2.1 앞에서 생성된 CF distribution을 선택한 후 **Behaviors** 탭을 선택하고 **Create Behavior** 버튼을 클릭합니다.   
![](/images/lab3/cf_5.png#center)  
2.2 아래의 설정으로 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Path Pattern: ***\*.css***   
&nbsp;&nbsp;&nbsp;&nbsp;Object Caching: **Customize**  
&nbsp;&nbsp;&nbsp;&nbsp;Minimum TTL: **86400** (1일)  
&nbsp;&nbsp;&nbsp;&nbsp;Maximum TTL: **86400**  
&nbsp;&nbsp;&nbsp;&nbsp;Default TTL: **86400**  
2.3 2.2와 동일한 방법으로 ***\*.js***, ***\*.png***, ***\*.jpg*** 를 추가합니다.  
![](/images/lab3/cf_6.png#center)   

{{% notice info %}}
위의 Behavior 설정은 기본 설정을 캐싱 안함 (TTL=0)으로 설정하고 캐싱할 대상을 추가적으로 등록했다면, 반대로 캐싱해야하는 파일의 유형이 다양하다면 기본 설정을 캐싱함으로 설정하고 동적페이지(예를 들면, .html, .do, .jsp 등)만 캐싱안하는 방식으로 처리할 수 있습니다.
{{% /notice %}}

### 3. 연결 확인   
3.1 생성한 Distribution의 Domain Name (d15gXXXXXXX.cloudfront.net 형태) 를 복사합니다.  
3.2 브라우저에서 개발자환경을 오픈한 상태에서 **http://d15gXXXXXXX.cloudfront.net/shop** 에 접속합니다.  
 * URL이 http에서 https로 변경된 것을 확인합니다.  
 * 페이지를 refresh한 후 개발자 환경에서 **x-cache: Hit from cloudfront** 로 변경된 것을 확인합니다.  

![](/images/lab3/cf_7.png#center) 

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



