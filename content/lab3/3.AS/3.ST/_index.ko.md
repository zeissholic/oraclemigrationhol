---
title: 부하 테스트
weight: 30
pre: "<b>4.3.3 </b>"
---

**실제 부하를 발생시켜 Auto-scaling이 동작하는 지 확인 합니다** <br/><br/>
{{% notice info %}}
Apache JMeter는 Java 8이상을 필요로합니다. 설치 후 다음 과정을 수행하십시오. 또한 분산환경의 세부적인 설정 등은 여기서 다루지 않으므로 다른 자료를 참고하십시오. 해당 핸즈온른 JMeter 5.3으로 테스트 되었습니다.
{{% /notice %}}

### 1. Apache JMeter 다운로드 
1.1 브라우저를 이용해 https://jmeter.apache.org/download_jmeter.cgi 에 접속합니다.   

1.2 Binary 파일을 적절한 위치에 다운 받고 압축을 해제합니다.   

1.3 해당 폴더아래 **bin** 폴더로 이동해 아래 파일을 에디터로 오픈합니다.  
윈도우: jmeter.bat  
맥, 리눅스: jmeter.sh  

1.4 상단의 주석 바로 아래 아래를 추가하고 저장합니다. 메모리 여유가 있는 경우 더 많은 메모리 할당도 가능합니다.  
```
JVM_ARGS="-Xms1g -Xmx1g"
```

1.5 bin 폴더로 이동후 아래 방법으로 JMeter를 실행합니다.  
윈도우: jmeter.bat 더블 클릭  
맥, 리눅스: ./jmeter.sh  
![](/images/lab3/asg_7.png#center)  


### 2. 사용자 등록 
2.1 아래 2개의 파일을 다운로드 하고 JMeter아래 저장합니다.  
[users.csv](/files/users.csv)  
[useradd.jmx](/files/useradd.jmx)


2.2 실행된 JMeter화면에서 useradd.jmx 파일을 오픈합니다.  

2.3 좌측의 트리를 열어 **/todo/register**를 열고 우측에 **Server Name or IP**에 CloudFront의 주소를 입력합니다.
![](/images/lab3/asg_8.png#center) 

2.4 좌측에서 **CSV Data Set Config** 선택하고 **Filename**에 위에서 다운받은 **users.csv** 경로를 입력합니다.
![](/images/lab3/asg_9.png#center) 

2.5 좌측의 **View Results Tree**를 선택하고 상단의 **Start**버튼을 누릅니다. (1000명의 사용자를 등록합니다.)
모든 등록이 완료되고 Start 버튼으로 다시 돌아오고 우측의 상태가 모두 초록색으로 완료 되어야 합니다.
![](/images/lab3/asg_10.png#center) 


### 3. 부하 발생 
3.1 아래 2개의 파일을 다운로드 하고 JMeter아래 저장합니다.  
[shoplist.csv](/files/shoplist.csv)  
[shoptest.jmx](/files/shoptest.jmx)  

3.2 실행된 JMeter화면에서 shoptest.jmx 파일을 오픈합니다.  

3.3 좌측의 트리를 열어 **User Defined Variables**를 열고 우측에 **host**에 CloudFront의 주소를 입력합니다.
![](/images/lab3/asg_11.png#center) 

3.4 좌측의 트리를 열어 **HTTP Request Defaults**를 열고 우측에 **Server Name or IP**에 CloudFront의 주소를 입력합니다.
![](/images/lab3/asg_14.png#center) 

3.5 좌측에서 **CSV Data Set user** 선택하고 **Filename**에 위에서 다운받은 **users.csv** 경로를 입력합니다.
![](/images/lab3/asg_12.png#center) 

3.6 좌측에서 **CSV Data Set shops** 선택하고 **Filename**에 위에서 다운받은 **shoplist.csv** 경로를 입력합니다.
![](/images/lab3/asg_13.png#center) 

3.7 좌측의 **Summary Report**를 선택하고 상단의 **Start**버튼을 누릅니다.  
(100명의 사용자가 로그인->리스트->상품구입->메인->루트화면을 반복합니다.)
![](/images/lab3/asg_15.png#center) 
### 4. Scale out 이벤트 확인
4.1 Auto Scaling Groups 메뉴에서 asg-aws-was를 선택 후 상단의 Activity를 선택하고 아래 내역을 확인합니다.
![](/images/lab3/asg_16.png#center) 
4.2 실제 생성된 EC2 인스턴스의 내역을 확인합니다.
![](/images/lab3/asg_17.png#center) 







---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



