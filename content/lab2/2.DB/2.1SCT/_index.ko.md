---
title: SCT 설치
weight: 10
pre: "<b>2.1. </b>"
---

SCT의 경우 로컬 PC에서 접속이 가능하나 현재 on-premise, AWS 모두 Private Subnet에 위히하고 있어 접속이 용이치 않으므로 VCP내부에 윈도우서버를 설치해 변환작업을 수행합니다. 차선책으로 각 VPC에 Client VPN을 연결하거나 Port Forwarding등을 사용할 수 있습니다.

### 1. SCT 수행을 위한 인스턴스 생성  
1.1 EC2 > **Instances** 메뉴에서 **Launch Instance** 버튼을 클릭합니다.  
1.2 **Microsoft Windows Server 2012 R2 Base - ami-0b556f49b82ce9aa4**를 선택합니다.  
1.3 **r5.large** 인스턴스 타입을 선택합니다.  
1.4 상세 정보에서 아래와 같이 설정합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Network: **AWS VPC**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **public-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;Auto-assign Public IP: **enable**  
&nbsp;&nbsp;&nbsp;&nbsp;Private IP: **10.254.1.14**  
1.5 스토리지는 **50GiB**로 변경합니다.  
1.6 Tag는 Name을 **sct-svr**로 지정합니다.  
1.7 Security Group은 아래와 같이 신규로 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **sct-svt-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **sct-svt-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Inbound Rules: **RDP | TCP | 3389 | *My IP* | My IP**  
1.8 Key paire는 **migrationhol**을 선택하고 인스턴스를 생성합니다.  



### 2. SCT 서버 연결   
RDP연결을 위한 어플리케이션이 없는 경우 다운 받은 후 다음을 진행합니다.  
2.1 EC2 > **Instances** 메뉴에서 **sct-svr** 를 선택합니다.  
2.2 상단 Actions 혹은 마우스 오른쪽 클릭 후 **Connect** 메뉴를 선택합니다.  
2.3 **Download Remote Desktop File**을 선택해 연결설정파일을 다운 받습니다.  
2.4 해당 파일을 더블클릭하여 RDS 어플리케이션이 실행되고 팝업창에서 암호를 물어볼 때 AWS 콘솔에서 **Get Password**를 클락하고 migrationhol.pem키를 입력해 암호를 확인 후 입력합니다.  

### 3. SCT 서버에서 on-premise 및 AWS로 접근 허용  
3.1 EC2 > **Security Groups** 메뉴를 선택합니다.  
3.2 검색에서 **on-premise-db-sg**를 선택하고 아래에서 **Inbound rules** 탭을 클릭한 후 **Edit inbound rules** 버튼을 클릭합니다.  
3.3 **add rule** 버튼을 클릭하고 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Inbound Rules: **Oracle-RDS | TCP | 1521 | 10.254.1.14/32 | SCT**  

3.4 EC2 > **Security Groups** 메뉴를 선택합니다.  
3.2 검색에서 **aws-db-sg**를 선택하고 아래에서 **Inbound rules** 탭을 클릭한 후 **Edit inbound rules** 버튼을 클릭합니다.  
3.3 **add rule** 버튼을 클릭하고 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Inbound Rules: **MYSQL/Aurora | TCP | 3306 | *sct-svr-sg 선택* | SCT**    



### 4. SCT 설치   
RDP연결로 돌아가 아래를 진행합니다.  
4.1 Windows Start > **Server Manager**를 실행합니다.  
4.2 **하단의 **Local Server**를 선택합니다.  
4.3 **IE Enhanced Security Configuration**을 클릭하고 아래와 같이 설정합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_1.png#center)    
4.4 바탕화면의 EC2 Feedback 을 더블클릭하여 IE를 엽니다.  
4.5 https://aws.amazon.com/dms/schema-conversion-tool/ 로 이동해 화면 중앙의 Windows 인스톨러를 다운 받아 설치합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_2.png#center)    

### 5. JDBC 드라이버 다운로드    
5.1 아래 두파일을 다운받아 Desktop에 저장합니다.
https://www.cloudhol.com/migration/ojdbc8.jar  
https://www.cloudhol.com/migration/mysql-connector-java-8.0.18.jar

### 6. Windows 방화벽 비활성화    
6.1 Windows Start > Control Panel 에서 Firewall을 선택하고 좌측의 Turn Windows Firewall on or off를 선택합니다.  
6.2 모든 설정을 off로 변경합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_5.png#center)  


### 7. SCT 설정 및 Schema Convert  
7.1 바탕화면의 SCT를 실행합니다.  
7.2 상단의 **Settings**를 클릭해 신규 프로젝트를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Project name: **Octank Project**  
&nbsp;&nbsp;&nbsp;&nbsp;Source Engine: **Oracle**  
7.3 다음 화면에서 Oracle 연결정보를 입력합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_4.png#center)  
7.4 모든 정보를 입력 후 좌측 하단의 **Test Connection** 버튼을 이용해 정상적으로 연결되는지 확인 후 **Next**를 클릭합니다.  
7.5 Schema에서 **webuser** 만 선택하고 Next를 클릭합니다.   
7.6 Target 설정에서 아래와 같이 입력합니다.   
![](/OracleMigrationHoL/images/lab2/sct_windows_6.png#center)   
7.7 설정이 완료되면 좌측에서 **webuser** 스키마만 선택하고 Actions의 **Create Report**를 선택하고 레포트를 확인합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_9.png#center)   
7.8 상단의 **Action Items** 탭에서 이슈를 확인합니다.  
**No issues were found.**를 확인합니다.  
7.9 좌측(Source)에서 모든 체크박스를 해제하고 webuser만 선택한 후 상단의 Actions메뉴에서 Convert schema를 클릭합니다   
![](/OracleMigrationHoL/images/lab2/sct_windows_7.png#center)  
7.9 우측에서 컨터트된 스키마가 제대로 보이는지 확인합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_8.png#center)  
7.10 우측에서 **webuser** 스키마 선택 후 마우스 오른쪽 클릭하여 Apply to database를 선택합니다.   
만약 메뉴가 나타나지 않으면 상단에서 Target연결을 다시 시도합니다.  
![](/OracleMigrationHoL/images/lab2/sct_windows_13.png#center)  
7.11 **webuser.carts** 테이블에서 오류가 발생했습니다.  
아래와 같이 변경 후 테이블에 대해서만 Apply to database를 실행합니다.
![](/OracleMigrationHoL/images/lab2/sct_windows_14.png#center) 
```
CREATE TABLE IF NOT EXISTS WEBUSER.CARTS (
ID BIGINT NOT NULL AUTO_INCREMENT,
PRICE DOUBLE DEFAULT NULL,
IS_DONE VARCHAR(1) DEFAULT NULL,
TARGET_DATE DATETIME  DEFAULT CURRENT_TIMESTAMP,
USERNAME VARCHAR(255) DEFAULT NULL,
ITEMNM VARCHAR(255) DEFAULT NULL,
IPADDR VARCHAR(20) DEFAULT NULL,
PRIMARY KEY (ID)
);
```
### 8. 스키마 확인 
8.1 AWS VPC용 Cloud9을 엽니다.  
8.2 아래 명령으로 **mysql client**를 설치합니다
```
sudo yum install mysql
```
8.3 mysql에 로그인합니다. (해당 host는 이전 단계에서 생성한 RDS의 Cluster Endpoint로 변경합니다.)
```
mysql --host=octankdb.cluster-cqxXXXXXXXX.ap-northeast-2.rds.amazonaws.com --port=3306 --user=admin --password=qwert12345
```
8.4 위 스키마 컨버전에 오류가 발생할 경우 아래 스크립트를 붙여넣습니다.
```
CREATE DATABASE IF NOT EXISTS WEBUSER;

CREATE TABLE IF NOT EXISTS WEBUSER.CARTS (
ID DOUBLE NOT NULL AUTO_INCREMENT,
PRICE DOUBLE DEFAULT NULL,
IS_DONE VARCHAR(1) DEFAULT NULL,
TARGET_DATE DATETIME  DEFAULT CURRENT_TIMESTAMP,
USERNAME VARCHAR(255) DEFAULT NULL,
ITEMNM VARCHAR(255) DEFAULT NULL,
IPADDR VARCHAR(20) DEFAULT NULL,
PRIMARY KEY (ID)
);

CREATE TABLE IF NOT EXISTS WEBUSER.USERS (
ID DOUBLE NOT NULL AUTO_INCREMENT,
FIRST_NAME VARCHAR(20) DEFAULT NULL,
LAST_NAME VARCHAR(20) DEFAULT NULL,
USERNAME VARCHAR(250) DEFAULT NULL,
PASSWD VARCHAR(20) DEFAULT NULL,
PRIMARY KEY (ID)
);
```
8.5 아래 스크립트를 이용해 추가 인덱스를 생성합니다.
```
CREATE INDEX IDX_USER_001 ON WEBUSER.USERS (USERNAME);
CREATE INDEX IDX_CART_001 ON WEBUSER.CARTS (USERNAME);
CREATE INDEX IDX_CART_002 ON WEBUSER.CARTS (USERNAME, ID DESC);
```
8.6 **webuser** 사용자를 생성하고 권한을 부여합니다.
```
CREATE USER 'webuser'@'%' IDENTIFIED BY 'qwert12345';
GRANT ALL PRIVILEGES ON WEBUSER.* TO 'webuser'@'%';

```
8.7 **webuser** 로 로그인되는지 확인합니다.
```
mysql --host=octankdb.cluster-cqxXXXXXXXX.ap-northeast-2.rds.amazonaws.com --port=3306  --database=WEBUSER --user=webuser --password=qwert12345
```


### 9. SCT 서버 중단  
9.1 필요한 스키마가 컨버트 되었으므로 서버를 중단합니다. 필요 시 다시 시작 후 사용할 수 있습니다. 

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
