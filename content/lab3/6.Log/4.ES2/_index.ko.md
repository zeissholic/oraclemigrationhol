---
title: Kibana 연결
weight: 40
pre: "<b>4.5.4 </b>"
---

**SCT용으로 만든 윈도우 서버에 로그인 해 Kibana를 사용합니다.** <br/><br/>

### 1. SCT 서버 시작 
1.1 AWS 콘솔에서 **EC2** 메뉴에서 **sct-svr**을 시작하고 로그인 합니다.**    

1.2 바탕화면의 **EC2 Microsoft Windows Guide** 아이콘을 클릭하고 ES의 **Kibana Endpoint**로 이동합니다.
![](/images/lab3/kibana_1.png#center)



### 2. Kibana Index Pattern 생성 

2.1 좌측의 **Dev Tools** 버튼을 누르고 실행해 데이터 입력을 확인합니다.  
![](/images/lab3/kibana_2.png#center)

2.2 좌측의 **Management** 버튼을 누르고 **Index Patterns**를 클릭하고 **Create index pattern**을 선택합니다.  
![](/images/lab3/kibana_3.png#center)

2.3 Index pattern에 **mbpshop**을 입력하고 **Next step**을 선택합니다.  
![](/images/lab3/kibana_4.png#center)

2.4 Time filter field name에서 **accesstime**을 선택하고 **Create index pattern**을 선택합니다.  
![](/images/lab3/kibana_5.png#center)
![](/images/lab3/kibana_6.png#center)


### 3. Kibana 대시보드 생성 
3.1 좌측에서 **Dashboard**를 선택하고 **Create new dashboard**버튼을 클릭합니다.
![](/images/lab3/kibana_7.png#center)

3.2 **Create New**를 선택하고 **Vertical Bar**버튼을 클릭합니다.
![](/images/lab3/kibana_8.png#center)

3.3 **mbpshop**을 클릭합니다.

3.4 우측의 **Buckets**에서 add 버튼을 누른후 **X-axis**를 선택합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Aggregation: **Date histogram**  
&nbsp;&nbsp;&nbsp;&nbsp;Field: **accesstime**  
&nbsp;&nbsp;&nbsp;&nbsp;minimum **interval: houly**  
![](/images/lab3/kibana_9.png#center)
![](/images/lab3/kibana_10.png#center)

시간을 들여 다양한 Visualize를 이용해 대시보드 구성을 진행하실 것을 추천합니다.




---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
