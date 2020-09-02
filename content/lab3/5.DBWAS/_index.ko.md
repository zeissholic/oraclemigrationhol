---
title: Secret Manager 적용
weight: 50
pre: "<b>4.5 </b>"
---

**Secret Manager를 활용해 WAS에서 DB연결 시 정보를 secret 형태로 관리합니다.** <br/><br/>

### 1. VPC 생성 
1.1 AWS 콘솔에서 **Secret Manager** 메뉴로 이동하고 **Store a new secret**버튼을 클릭합니다.  

1.2 아래 내용을 입력합니다.
&nbsp;&nbsp;&nbsp;&nbsp;Select secret type: **Credentials for RDS database**    
&nbsp;&nbsp;&nbsp;&nbsp;User name: **webuser**  
&nbsp;&nbsp;&nbsp;&nbsp;Password: **qwert12345**  
&nbsp;&nbsp;&nbsp;&nbsp;Select which RDS database this secret will access: **octankdb**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

&nbsp;&nbsp;&nbsp;&nbsp;Secret name: **mbp/shop/rds2**   
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  
&nbsp;&nbsp;&nbsp;&nbsp;**NEXT**  

### 2. EC2 인스턴스의 IAM role 변경
2.1 AWS 콘솔에서 **IAM** 메뉴에서 좌측의 **Roles**로 이동 후 **mbpshop_role**를 검색 후 선택합니다.   

2.2 **Attach policies** 버튼을 클릭하고 새로운 policy명 **secretmanagerrole** 를 생성합니다.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kms:GetParametersForImport",
                "kms:DescribeCustomKeyStores",
                "secretsmanager:GetRandomPassword",
                "kms:GetPublicKey",
                "secretsmanager:GetResourcePolicy",
                "secretsmanager:GetSecretValue",
                "secretsmanager:DescribeSecret",
                "kms:GetKeyRotationStatus",
                "kms:GetKeyPolicy",
                "kms:DescribeKey",
                "kms:ListResourceTags",
                "secretsmanager:ListSecretVersionIds"
            ],
            "Resource": "*"
        }
    ]
}
```

### 3. Tomcat 설정 변경
3.1 아래 코드를 이용해 Tomcat의 DB 연결 설정을 변경합니다. 이는 기존의 DB암호를 설정파일에 노출하지 않고 암호화하기 위한 방식과 동일합니다.  
여기서는 DB의 ID/PW만을 secret으로 처리했으나 DB endpoint, port 등도 처리 가능합니다.  
해당 핸즈온에서는 이미 컴파일된 jar파일을 사용합니다.  
```
package me.ijpark.tomcat;


import java.nio.ByteBuffer;
import java.util.Properties;
import javax.naming.Context;
import javax.sql.DataSource;
import org.apache.tomcat.jdbc.pool.DataSourceFactory;
import com.amazonaws.client.builder.AwsClientBuilder;
import com.amazonaws.services.secretsmanager.*;
import com.amazonaws.services.secretsmanager.model.*;
import com.google.gson.*;
import java.util.Base64; 
import java.util.Base64.Decoder; 
import java.util.Base64.Encoder;


public class AwsSecretDataSourceFactory extends DataSourceFactory {

	  public static String getSecret() {

	      String secretName = "mbp/shop/rds2";
	      String endpoint = "secretsmanager.ap-northeast-2.amazonaws.com";
	      String region = "ap-northeast-2";

	      AwsClientBuilder.EndpointConfiguration config = new AwsClientBuilder.EndpointConfiguration(endpoint, region);
	      AWSSecretsManagerClientBuilder clientBuilder = AWSSecretsManagerClientBuilder.standard();
	      clientBuilder.setEndpointConfiguration(config);
	      AWSSecretsManager client = clientBuilder.build();

	      String secret, decodedBinarySecret;
	      ByteBuffer binarySecretData;
	      GetSecretValueRequest getSecretValueRequest = new GetSecretValueRequest()
	              .withSecretId(secretName).withVersionStage("AWSCURRENT");
	      GetSecretValueResult getSecretValueResult = null;
	      try {
	          getSecretValueResult = client.getSecretValue(getSecretValueRequest);

	      } catch(ResourceNotFoundException e) {
	          System.out.println("The requested secret " + secretName + " was not found");
	      } catch (InvalidRequestException e) {
	          System.out.println("The request was invalid due to: " + e.getMessage());
	      } catch (InvalidParameterException e) {
	          System.out.println("The request had invalid params: " + e.getMessage());
	      }

	      if(getSecretValueResult == null) {
	          return null;
	      }

	      // Depending on whether the secret was a string or binary, one of these fields will be populated
	      if(getSecretValueResult.getSecretString() != null) {
	          secret = getSecretValueResult.getSecretString();
	          System.out.println("str:"+secret);
	          return secret;
	      }
	      else {
	          //binarySecretData = getSecretValueResult.getSecretBinary();
	          //System.out.println("bin:"+binarySecretData.toString());
	    	  //return null;
	    	  decodedBinarySecret = new String(Base64.getDecoder().decode(getSecretValueResult.getSecretBinary()).array());
	    	  System.out.println("bin:"+decodedBinarySecret);
	    	  return decodedBinarySecret;
	      }
	      
	      
	      
	  }

    @Override
    public DataSource createDataSource(Properties properties, Context context, boolean XA) throws Exception {
    	//properties.setProperty(PROP_USERNAME, "pocuser");
    	//properties.setProperty(PROP_PASSWORD, "bQ9wBijSoC");
    	//if (properties.getProperty("name").toString().contentEquals("jdbc/shopdb")) {
    		replacePassword(properties);
    		//System.out.println(properties.getProperty("password").toString());
    	//}

		return super.createDataSource(properties, context, XA);
	}

	private void replacePassword(Properties properties) throws Exception {
		String secret = getSecret();
		System.out.println("secret:"+secret);
		JsonObject convertedObject = new Gson().fromJson(secret, JsonObject.class);

        String password = convertedObject.getAsJsonObject().get("password").toString();
        String username = convertedObject.getAsJsonObject().get("username").toString();
        
        System.out.println("id:"+username+",pw"+password);
        
        properties.setProperty(PROP_USERNAME, username);
		properties.setProperty(PROP_PASSWORD, password);
		
	}

}
```

3.2 AWS용 Cloud9으로 이동후 **aws-was-2a**에 연결합니다.
```
ssh -i migrationhol.pem centos@10.254.101.10
```

3.3 아래 명령으로 필요한 jar파일을 다운 받아 lib에 설치합니다.  
```
sudo su -
cd /opt/tomcat/latest/lib

wget https://www.cloudhol.com/migration/ijpark.jar
chown tomcat:tomcat ijpark.jar
chmod 640 ijpark.jar
```

3.4 **context.xml**을 변경합니다.  
```
vi ../conf/context.xml
```
기존 아래 내용을 **본인의 RDS Endpoint로 변경해야 합니다.**
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
아래 내용으로 변경합니다.
```
    <Resource auth="Container"
              driverClassName="org.mariadb.jdbc.Driver"
              factory="me.ijpark.tomcat.AwsSecretDataSourceFactory"
              global="jdbc/shopdb"
              maxActive="40"
              maxIdle="30"
              minIdlea="15"
              maxWait="5000"
              name="jdbc/shopdb"
              type="javax.sql.DataSource"
              url="jdbc:mariadb:aurora//octankdb.cluster-cqxXXXXXXXX.ap-northeast-2.rds.amazonaws.com:3306/WEBUSER"/>
```
![](/images/lab3/sm_1.png#center) 

### 4. Tomcat 재시동
4.1 아래 명령으로 Tomcat을 재시동합니다.
```
sudo systemctl restart tomcat
sudo systemctl status tomcat
```

### 5. Auto Scaling Group 수정
5.1 **aws-was-2a**의 **AMI**를 다시 생성합니다. 이름은 **aws-was-2a-20200901** 등으로 지정합니다.

5.2 AMI 생성이 완료되면 **Launch Templates**로 이동합니다.  

5.2 **lt-aws-was**의 ID를 클릭하고 상세화면으로 들어갑니다.  

5.3 우측 상단 **Actions**에서 **Modify Template**을 선택합니다.  

5.4 AMI에서 방금 생성한 **aws-was-2a-20200901**을 선택하고 저장합니다.  

5.5 **Auto Scaling groups** 메뉴에서 **asg-aws-was**를 선택합니다.  

5.6 두번 째 영역 **Launch template**에서 우측의 **Edit** 버튼을 클릭합니다.  

5.7 **Version**이 **Latest**가 선택 후 저장합니다.  

5.8 기존 운용중인 서버에 반영을 위해 Desired capacity를 0으로 변경 후 다시 1로 변경합니다.  


### 6. 어플리케이션의 정상 작동여부를 확인합니다.


### 7. CI/CD로 두 서버에 모두 배포테스트를 수행합니다.
7.1 우선 배포시 설정을 변경합니다. AWS 콘솔의 **CodeDeploy** 메뉴로 이동합니다. **Deploy 아래 Applications**를 선택하고 우측의 **mbpshop**을 클릭합니다.   

7.2 아래 Deployment groups에서 **dg-mbpshop**을 선택합니다.

7.3 우측 상단의 **Edit** 버튼을 클릭하고 아래 내용을 추가 혹은 변경합니다.
&nbsp;&nbsp;&nbsp;&nbsp;Tag에서: Key **aws:autoscaling:groupName**, Value **asg-aws-was** 추가   
&nbsp;&nbsp;&nbsp;&nbsp;Deployment settings: **CodeDeployDefault.HalfAtATime** 선택  
&nbsp;&nbsp;&nbsp;&nbsp;Enable load balancing: **선택**  
&nbsp;&nbsp;&nbsp;&nbsp;Choose a target group: **tg-aws**  

7.3 mbpshop 소스를 변경합니다.  
DNA/mbpshop/WebContent/common 로 이동합니다.  
```
vi header.jsp
```
버전을 2.3 으로 변경합니다.  

7.4 아래 명령으로 git commit을 수행합니다.  
```
git diff
git add header.jsp
git commit -m "version updated"
git push -u origin master
```

7.5 CodePipeline 메뉴에서 배포가 성공적인지 확인 합니다.  
![](/images/lab3/cp_1.png#center) 

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
