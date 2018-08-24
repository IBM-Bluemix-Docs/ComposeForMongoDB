---
copyright:
  years: 2016,2018
lastupdated: "2018-06-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 버전

배치할 수 있는 버전 | 선호 버전
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="표 1. MongoDB 버전" caption-side="top"}

##  선호 버전

일반적으로 선호 버전은 {{site.data.keyword.composeForMongoDB}}에 사용 가능한 최신 MongoDB 데이터베이스 버전입니다. 이 버전은 카탈로그 페이지의 드롭 다운 버전 선택기에 있는 기본값입니다. 호출에 버전이 지정되지 않은 경우 API에서 자동으로 프로비저닝하는 버전이기도 합니다.

### 새로운 선호 버전 프로토콜

새 버전을 사용할 수 있게 되면 릴리스가 발표되고 배치에 사용할 수 있게 됩니다. 릴리스하고 나면 약 7일간의 기간이 있으며, 이 기간 동안 최신 버전을 사용할 수 있지만 해당 버전은 선호 버전이 아닙니다. 이 기간 동안에는 새 버전을 배치하고 테스트할 수 있으며, 현재 버전도 여전히 사용할 수 있습니다. 7일간의 기간이 종료되면 새 버전이 선호 버전으로 설정되거나 이 변경을 위한 새 날짜가 발표됩니다.

프로비저닝하는 데 사용할 수 있는 버전 목록은 {{site.data.keyword.composeForMongoDB}} [카탈로그 페이지](https://console.{DomainName}/catalog/services/compose-for-mongodb)에 있습니다.

{{site.data.keyword.composeForMongoDB}} 서비스에 사용할 수 있는 최신 버전 목록을 얻으려면
[GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions) 엔드포인트를 사용할 수 있습니다.
