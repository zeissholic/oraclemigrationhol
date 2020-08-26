---
date: 2020-03-05
title: Home
weight: 20
chapter: true
---

# Oracle DB 기반 웹어플리케이션 마이그레이션

---

Oracle DB 기반 웹어플리케이션을 AWS기반으로 마이그레이션하는 방법을 설명합니다.

---

![Architecture image](/images/architecture.jpg)
아키텍처에 대한 설명
1. On-premise Oracle 기반 3-tier 아키텍처
2. 마이그레이션 (CloudEndure이용한 Web서버, WAS는 새로 설치, DMS이용한 DB마이그레이션)
3. Web서버를 CloudFront로 대체
4. Auto-Scaling
5. CI/CD
6. Log 분석

---
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
