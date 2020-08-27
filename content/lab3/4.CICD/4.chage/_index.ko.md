---
title: 변경 확인
weight: 40
pre: "<b>4.4.4 </b>"
---

**실제 소스를 변경해 반영되는지 확인합니다.** <br/><br/>

### 1. 소스 수정
1.1 아래 폴더로 이동합니다.   
```
DNA/mbpshop/WebContent/common  
```

1.2 아래 명령으로 파일을 수정합니다.  
Octank Shopping Mall (**AWS v2.1**) ->  Octank Shopping Mall (**AWS v2.2**)  
```
vi header.jsp
```
![](/images/lab3/header_1.png#center75)  

### 2. git commit  
2.1 아래 명령으로 변경 내역을 확인합니다.  
```
git diff
```
![](/images/lab3/header_2.png#center75)  

2.2 git commit / push.   
```
git status
git add header.jsp
git commit -m "version updated 2.2"
git push -u origin master
```


### 3. CodePipeline 및 사이트 확인  
![](/images/lab3/ppl_5.png#center75)
![](/images/lab3/ppl_6.png#center75)

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.



