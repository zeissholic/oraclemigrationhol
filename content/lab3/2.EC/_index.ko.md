---
title: ElastiCache 적용
weight: 20
pre: "<b>4.2 </b>"
---

**State정보를 서버에 저장하지 않도록, 즉 Stateless로 만들기 위해 ElastiCache for Redis를 이용해 세션정보를 외부에 저장할 수 있도록 합니다.** <br/><br/>


### 1. Subnet Groups 생성 
1.1 AWS 콘솔에서 **ElastiCache** 메뉴로 들어간 후 좌측의 **Subnet Groups** 메뉴를 선택한 후 **Create Subnet Group** 버튼을 클릭합니다.  
1.2 아래의 설정으로 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Name: **ec-aws-subnetg**   
&nbsp;&nbsp;&nbsp;&nbsp;Description: **ec-aws-subnetg**     
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;AZ1: **ap-northeast-2a**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **private-2a (10.254.101.0/24)**  
&nbsp;&nbsp;&nbsp;&nbsp;AZ2: **ap-northeast-2c**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subnet: **private-2c (10.254.102.0/24)**  
![](/images/lab3/ec_1.png#center)  

### 2. Parameter Groups 생성 및 수정
2.1 AWS 콘솔에서 **ElastiCache** 메뉴로 들어간 후 좌측의 **Parameter Groups** 메뉴를 선택한 후 **Create** 버튼을 클릭합니다.  
2.2 아래의 설정으로 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Family: **redis5.0**   
&nbsp;&nbsp;&nbsp;&nbsp;Name: **ec-aws-pg**   
&nbsp;&nbsp;&nbsp;&nbsp;Description: **ec-aws-pg**   
![](/images/lab3/ec_2.png#center)  
2.3 생성된 parameter group을 선택하고 아래에서 **Edit parameters** 클릭합니다.  
2.4 두번 째 페이지에서 **cluster-enabled**의 값을 **yes**로 변경합니다.  
![](/images/lab3/ec_3.png#center)  

### 3. ElastiCache용 Security Group 생성
3.1 EC2 > Security Groups로 이동해 새로운 ElastiCache를 위한 Security group를 생성합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Security group name: **aws-ec-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;Description: **aws-ec-sg**  
&nbsp;&nbsp;&nbsp;&nbsp;VPC: **AWS**  
&nbsp;&nbsp;&nbsp;&nbsp;Add Inbound Rules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**TCP : 6379 : aws-was의 security group : WAS**  


### 4. ElastiCache 생성 
4.1 AWS 콘솔에서 **ElastiCache** 메뉴로 들어간 후 좌측의 **Redis** 메뉴를 선택한 후 **Create** 버튼을 클릭합니다.  
4.2 아래의 설정으로 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Cluster engine: **Redis** | **Cluster Mode enabled**   
&nbsp;&nbsp;&nbsp;&nbsp;Name: **ec-aws**   
&nbsp;&nbsp;&nbsp;&nbsp;Description: **ec-aws**   
&nbsp;&nbsp;&nbsp;&nbsp;Engine version compatibility: **5.0.6**   
&nbsp;&nbsp;&nbsp;&nbsp;Port: **6379**   
&nbsp;&nbsp;&nbsp;&nbsp;Parameter group: **ec-aws-pg**   
&nbsp;&nbsp;&nbsp;&nbsp;Node type: **cache.t3.micro**   

&nbsp;&nbsp;&nbsp;&nbsp;Number of Shards: **2**  
&nbsp;&nbsp;&nbsp;&nbsp;Replicas per Shard: **1**  
&nbsp;&nbsp;&nbsp;&nbsp;Multi-AZ: **Yes**  
&nbsp;&nbsp;&nbsp;&nbsp;Subnet group: **ec-aws-subnetg**  

&nbsp;&nbsp;&nbsp;&nbsp;Security group: **aws-ec-sg**  


### 5. Tomcat에 ElastiCache 설정 추가   
5.1 AWS용 Cloud9을 실행합니다.  
5.2 WAS서버에 연결합니다.  
```
ssh -i migrationhol.pem centos@10.254.101.10
```
5.3 Tomcat 폴더로 이동하고 **context.xml**을 수정합니다.  
```
sudo su - root
cd /opt/tomcat/latest/conf
cp context.xml  context.xml.bak
vi context.xml
```
5.4 DB연결 앞에 아래를 추가 합니다.
```
    <Manager className="org.redisson.tomcat.RedissonSessionManager"
             configPath="${catalina.base}/conf/redisson.conf"
             readMode="REDIS"
             updateMode="DEFAULT"/>
```
![](/images/lab3/tomcat_1.png#center)  
5.5 redisson.yaml 파일을 생성합니다.  
```
vi redisson.conf
```
5.6 아래를 복사해 붙입니다.  
**address부분을 ElastiCache의 **Configuration Endpoint**로 변경합니다.**
![](/images/lab3/ec_4.png#center)  
```
{
   "clusterServersConfig": {
      "idleConnectionTimeout": 10000,
      "connectTimeout": 10000,
      "timeout": 3000,
      "retryAttempts": 3,
      "retryInterval": 1500,
      "failedSlaveReconnectionInterval": 3000,
      "failedSlaveCheckInterval": 60000,
      "password": null,
      "subscriptionsPerConnection": 5,
      "clientName": null,
      "nodeAddresses": ["redis://ec-aws.smixfd.clustercfg.apn2.cache.amazonaws.com:6379"],
      "keepAlive": false,
      "tcpNoDelay": false
   },
   "threads": 16,
   "nettyThreads": 32,
   "transportMode": "NIO"
}
```


### 6. 세션 확인
6.1 설정 적용을 위해 아래 명령으로 tomcat을 재시동합니다.     
```
sudo systemctl restart tomcat
sudo systemctl status tomcat
```
6.2 웹화면에서 실제 로그인 등을 수행해 정상적으로 세션이 유지되는지 확인합니다.     

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



