---
title: Kibana 연결
weight: 40
pre: "<b>4.5.4 </b>"
---

**SCT용으로 만든 윈도우 서버에 로그인 해 Kibana를 사용합니다.** <br/><br/>

{{% notice info %}}
Elasticsearch Service를 Public하게 노춣하고 Cognito를 이용해 인증할 수 있습니다. 아래 링크를 참고하십시오. https://docs.aws.amazon.com/ko_kr/elasticsearch-service/latest/developerguide/es-cognito-auth.html
{{% /notice %}}

### 1. SCT 서버 시작 
1.1 AWS 콘솔에서 **EC2** 메뉴에서 **sct-svr**을 시작하고 로그인 합니다.**    

1.2 바탕화면의 **EC2 Microsoft Windows Guide** 아이콘을 클릭하고 ES의 **Kibana Endpoint**로 이동합니다.
![](/images/lab3/kibana_1.png#center)



### 2. GEO_POINT 적용
위경도값을 통해 geo_point를 생성하도록 인덱스를 새로 만듭니다.
2.1 좌측의 **Dev Tools** 버튼을 누르고 콘솔에 아래 명령을 입력하고 실행합니다.
```
DELETE mbpshop
```
![](/images/lab3/kibana_11.png#center)

2.2 아래를 실행해 새로운 인덱스를 생성합니다.
```
PUT mbpshop/
{
    "mappings" : {
      "properties" : {
        "accesstime" : {
          "type" : "date"
        },
        "country" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "httpcode" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "location" : {
          "type" : "geo_point"
        },
        "protocol" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "source_ip" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "transferred" : {
          "type" : "long"
        },
        "url" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "verb" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
}
```
![](/images/lab3/kibana_12.png#center)

2.3 access log를 발생시켜 데이터가 입력되도록 합니다. (화면 리프레시, 로그인 등)  

### 3. Kibana Index Pattern 생성 

3.1 좌측의 **Dev Tools** 버튼을 누르고 실행해 데이터 입력을 확인합니다.  
![](/images/lab3/kibana_2.png#center)

3.2 좌측의 **Management** 버튼을 누르고 **Index Patterns**를 클릭하고 **Create index pattern**을 선택합니다.  
![](/images/lab3/kibana_3.png#center)

3.3 Index pattern에 **mbpshop**을 입력하고 **Next step**을 선택합니다.  
![](/images/lab3/kibana_4.png#center)

3.4 Time filter field name에서 **accesstime**을 선택하고 **Create index pattern**을 선택합니다.  
![](/images/lab3/kibana_5.png#center)
![](/images/lab3/kibana_6.png#center)


### 4. Kibana 대시보드 생성 (Vertical Bar)
4.1 좌측에서 **Dashboard**를 선택하고 **Create new dashboard**버튼을 클릭합니다.
![](/images/lab3/kibana_7.png#center)

4.2 **Create New**를 선택하고 **Vertical Bar**버튼을 클릭합니다.
![](/images/lab3/kibana_8.png#center)

4.3 **mbpshop**을 클릭합니다.

4.4 우측의 **Buckets**에서 add 버튼을 누른후 **X-axis**를 선택합니다.  
&nbsp;&nbsp;&nbsp;&nbsp;Aggregation: **Date histogram**  
&nbsp;&nbsp;&nbsp;&nbsp;Field: **accesstime**  
&nbsp;&nbsp;&nbsp;&nbsp;minimum **interval: houly**  
![](/images/lab3/kibana_9.png#center)
![](/images/lab3/kibana_10.png#center)




### 5. Kibana 대시보드 생성 (Vertical Bar)
5.1 좌측 상단의 **Create New** 버튼을 클릭합니다.  
![](/images/lab3/kibana_13.png#center)

5.2 **Coordinate Map** > **mbpshop**을 차례로 선택합니다.  

5.3 Buckets > add > Geo coordinate > Geohash > location을 선택하고 Update버튼을 누릅니다.
![](/images/lab3/kibana_14.png#center)

5.4 지도를 확대해서 정확한 위치도 확인합니다.
![](/images/lab3/kibana_15.png#center)
![](/images/lab3/kibana_16.png#center)

시간을 들여 다양한 Visualize를 이용해 대시보드 구성을 진행하실 것을 추천합니다.

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
