---
title: Cloud9 구성
weight: 20
pre: "<b>2. </b>"
---

**원활한 핸즈온을 위해 Cloud9 환경을 구축합니다.**   
실습은 한국 리전에서 진행합니다. 현재 리전이 **Asia Pacific (Seoul):ap-northeast-2**인지 확인합니다.
 
### 1. Cloud9 환경 생성  
1.1 **Cloud9** 메뉴에서 **Create environment**를 클릭합니다  
1.2 Name을 **on-premise-cloud9**로 설정하고 Next를 클릭합니다  
1.3 **Network settings**에서 앞에서 생성한 **on-premise** VPC와 **DMZ** subnet를 선택하고 나머지는 기본값으로 두고 생성합니다. (생성에 수분의 시간이 소요될 수 있습니다.)   


### 2. Key Pair 업로드  
2.1 Cloud9 화면에서 File > **Upload Local Files**를 선택하고 3.1에서 생성한 **migrationhol.pem**을 선택하고 업로드 합니다.
![keypair](/OracleMigrationHoL/images/lab0/cloud9_upload.png#center) 
2.2 아래 명령으로 key pair의 권한을 수정합니다. 
```
chmod 600 migrationhol.pem
```


---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.

