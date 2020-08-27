---
title: CodeCommit
weight: 10
pre: "<b>4.4.1 </b>"
---

**CodeCommit 서비스를 구성합니다.** <br/><br/>

### 1. Git repository 복제 
1.1 임의의 위치에 DNA명의 폴더를 생성하고 해당 폴더로 이동합니다.   
```
mkdir ./DNA 
cd DNA
```

1.2 아래 명령으로 git을 clone 합니다..   
```
git clone https://github.com/zeissholic/mbpshop.git  
```

### 2. CodeCommit Repository 생성  
2.1 AWS 콘솔에서 **CodeCommit**으로 이동 후 좌측에서 **Repositories**메뉴을 선택합니다.  
2.2 **Create repository** 버튼을 클릭하고 아래 정보를 입력합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Repository name: **mbpshop**   
&nbsp;&nbsp;&nbsp;&nbsp;Description: **demo shop**   

### 3. Git Credential 생성  
3.1 AWS 콘솔에서 IAM메뉴로 이동 후 로그인한 사용자의 상세화면을 오픈합니다.  
3.2 **Security credentials** 탭에서 하단의 **HTTPS Git credentials for AWS CodeCommit**에서 **Generate credentials** 버튼을 클릭합니다.  
3.3 팝업창의 정보를 기록하고 Credential을 다운받습니다.  

### 4. CodeCommit에 소스 업로드
4.1 mbpshop으로 이동해 아래 명령을 수행합니다.  
```
cd mbpshop
git remote remove origin
git init
git add .
git commit -m "first commit"
git remote add origin https://git-codecommit.ap-northeast-2.amazonaws.com/v1/repos/mbpshop
git push -u origin master
```
4.2 3에서 기록한 git credential을 입력합니다.  
4.3 CodeCommit 메뉴로 이동해 소스가 등록되었는지 확인합니다.
![](/images/lab3/ccommit_1.png#center)  

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



