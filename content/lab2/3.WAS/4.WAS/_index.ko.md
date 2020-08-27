---
title: Tomcat 설정
weight: 40
pre: "<b>3.4. </b>"
---

### 1. Tomcat Lib 추가
1.1 tomcat lib를 추가합니다. (향후 핸즈온에 필요한 모든 lib를 다운로드 합니다)
```
sudo su - root
cd /opt/tomcat/latest/lib

wget https://www.cloudhol.com/migration/mariadb-java-client-2.6.2.jar

wget https://www.cloudhol.com/migration/redisson-all-3.13.3.jar
wget https://www.cloudhol.com/migration/redisson-tomcat-9-3.13.3.jar

chown tomcat:tomcat mariadb*.jar redisson*.jar
chmod 640 mariadb*.jar redisson*.jar
```
1.2 db 연결정보를 추가합니다.  
```
cd /opt/tomcat/latest/conf
vi context.xml
```
아래 내용을 붙여 넣습니다. (url은 Aurora Cluster Endpoint로 변경합니다.)
```
    <Resource   auth="Container"
                driverClassName="org.mariadb.jdbc.Driver"
                factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
                global="jdbc/shopdb"
                name="jdbc/shopdb"
                maxActive="40"
                maxIdle="30"
                maxWait="5000"
                username="webuser"
                password="qwert12345"
                type="javax.sql.DataSource"
                url="jdbc:mariadb:aurora//octankdb.cluster-cqxXXXXXXXX.ap-northeast-2.rds.amazonaws.com:3306/WEBUSER"/>
```
![](/images/lab2/was_tomcat_3.png#center)

1.3 소스를 다운받습니다.  
```
cd /opt/tomcat/latest/webapps
wget https://www.cloudhol.com/migration/shop.war
chown tomcat:tomcat shop.war
```

1.4 Tomcat을 재시동합니다.  
```
sudo systemctl restart tomcat
sudo systemctl status tomcat
```

1.6 **ALB DNS name/shop** 에 연결해 화면이 정상적으로 출력되는지 확인합니다.  
![](/images/lab2/alb_2.png#center)

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
