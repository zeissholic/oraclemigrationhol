---
title: Tomcat 설치
weight: 20
pre: "<b>3.2. </b>"
---

### 1. Tomcat9 설치
1.1 JDK 설치.
```
sudo yum install java-1.8.0-openjdk-devel -y
```
1.2 Tomcat 설치
```
sudo yum install wget -y
sudo useradd -m -U -d /opt/tomcat  -s /bin/false tomcat
cd /tmp
wget https://www-eu.apache.org/dist/tomcat/tomcat-9/v9.0.37/bin/apache-tomcat-9.0.37.tar.gz
tar -xf apache-tomcat-9.0.37.tar.gz
sudo mv apache-tomcat-9.0.37 /opt/tomcat/
sudo ln -s /opt/tomcat/apache-tomcat-9.0.37 /opt/tomcat/latest
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'

```
1.3 Tomcat 서비스 등록 (CentOS7기준)
```
sudo vi /etc/systemd/system/tomcat.service
```
아래 내용을 붙여 넣습니다. (i=입력모드)
```
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/jre"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"

Environment="CATALINA_BASE=/opt/tomcat/latest"
Environment="CATALINA_HOME=/opt/tomcat/latest"
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC“

ExecStart=/opt/tomcat/latest/bin/startup.sh
ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```
저장은 Esc키 입력 후 wq!를 입력합니다.  

1.4 Tomcat 서비스 등록(2) (CentOS7기준)
```
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat
sudo systemctl status tomcat

```
![](/images/lab2/was_tomcat_1.png#center)



---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
