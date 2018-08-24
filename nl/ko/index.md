---

copyright:
  years: 2016,2018
lastupdated: "2018-03-26"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.composeForMongoDB}} 정보
{: #about-compose-for-mongodb}

{{site.data.keyword.composeForMongoDB_full}}에서는 MongoDB의 강력한 색인 작성, 조회, 집계, 폭넓은 드라이버 지원을 사용함으로써 많은 스타트업과 엔터프라이즈에서 찾는 JSON 데이터 저장소가 되었습니다. {{site.data.keyword.composeForMongoDB}}에서는 간단한 Auto-Scaling 배치 시스템을 제공합니다. 고가용성과 중복성, 자동화된 백업과 On-Demand의 중단 없는 백업, 모니터링 도구, 경보 시스템으로 통합, 성능 분석 보기 외의 여러 기능을 깔끔하고 단순한 사용자 인터페이스에서 제공합니다.
{:shortdesc}

**참고:** 2016년 9월 14일 이전에 프로비저닝되어 아직 사용 중인 Compose 서비스 인스턴스를 여전히 사용할 수 있으며 [https://www.compose.com/](https://www.compose.com)에서 해당 인스턴스에 직접 액세스할 수 있습니다. 이 시점 이후에 프로비저닝되는 Compose 서비스 인스턴스의 경우 {{site.data.keyword.cloud}} 계정을 사용하여 직접 액세스하고 사용할 수 있습니다.

## {{site.data.keyword.composeForMongoDB}} 서비스 인스턴스 작성

{{site.data.keyword.cloud_notm}} 카탈로그의 [{{site.data.keyword.composeForMongoDB}} 페이지](https://console.{DomainName}/catalog/services/compose-for-mongodb/)에서 {{site.data.keyword.composeForMongoDB}} 서비스를 작성할 수 있습니다.

서비스 이름과 서비스를 프로비저닝할 지역, 조직 및 영역을 선택하십시오. **데이터베이스 버전 선택** 필드를 사용하여 사용 가능한 데이터베이스 버전 중에서 선택할 수 있습니다.

{{site.data.keyword.composeForMongoDB}} 인스턴스를 프로비저닝할 때 *표준* 또는 *엔터프라이즈* 플랜을 선택할 수 있습니다. *엔터프라이즈* 플랜을 사용하면 {{site.data.keyword.composeForMongoDB}} 인스턴스를 사용 가능한 {{site.data.keyword.composeEnterprise}} 클러스터에 프로비저닝할 수 있습니다. {{site.data.keyword.composeEnterprise}}는 엔터프라이즈 준수에 필요한 보안 및 격리를 제공하며 전용 네트워킹을 사용하여 배치된 데이터베이스의 성능을 보장합니다. 세부사항은 [{{site.data.keyword.composeEnterprise}} 문서](/docs/services/ComposeEnterprise/index.html)를 참조하십시오.

## {{site.data.keyword.composeForMongoDB}} 관리

서비스 대시보드에서 서비스를 관리할 수 있습니다. 여기서 {{site.data.keyword.cloud_notm}} Compose 데이터베이스 및 연결 방법에 대한 정보를 찾을 수 있습니다. 또한 다음을 수행할 수 있습니다.
- 백업 관리
- 서비스에 대한 추가 리소스 할당
- 화이트리스트를 사용하여 데이터베이스에 대한 액세스 제한. 

자세한 정보는 [설정](./dashboard-settings.html)을 참조하십시오.

{{site.data.keyword.composeForMongoDB}}는 Cloud Foundry 역할에 의존하여 서비스에 대한 액세스를 관리합니다. 개발자 역할이 있는 사용자만 서비스 대시보드를 보거나 사용할 수 있습니다. Cloud Foundry 역할에 대한 자세한 정보는 [Cloud Foundry 액세스](https://console.{DomainName}/docs/iam/cfaccess.html#cfaccess) 및 [Cloud Foundry 액세스 관리](https://console.{DomainName}/docs/iam/mngcf.html#mngcf) 페이지를 참조하십시오.
{: .tip}

## {{site.data.keyword.composeForMongoDB}}에 연결

서비스와 함께 작성된 신임 정보를 사용하거나 서비스 대시보드의 *개요* 탭에서 제공되는 연결 문자열 및 명령행을 사용하여 서비스에 연결할 수 있습니다.

## {{site.data.keyword.cloud_notm}} 애플리케이션을 {{site.data.keyword.composeForMongoDB}}에 연결

{{site.data.keyword.cloud_notm}} 애플리케이션을 서비스에 연결하려면 서비스와 함께 작성되는 신임 정보를 사용하십시오. [{{site.data.keyword.cloud_notm}} 애플리케이션 연결](./connecting-bluemix-app.html)에서 {{site.data.keyword.cloud_notm}} 애플리케이션을 {{site.data.keyword.composeForMongoDB}} 서비스에 연결하는 방법에 대한 정보를 찾을 수 있습니다.

## {{site.data.keyword.cloud_notm}} 외부에서 {{site.data.keyword.composeForMongoDB}}에 연결

{{site.data.keyword.cloud_notm}} 외부에서 {{site.data.keyword.composeForMongoDB}}에 연결하려는 경우 제공된 연결 문자열 또는 명령행을 사용할수 있습니다. [외부 애플리케이션 연결](./connecting-external.html)에서 연결 방법에 대한 정보를 찾을 수 있습니다.

## MEAN 스택을 사용하여 웹 애플리케이션 작성

{{site.data.keyword.composeForMongoDB}}를 웹 애플리케이션 작성을 위한 MEAN 스택의 일부로서 사용할 수 있습니다. Mongo DB, Express 웹 프레임워크, Angular 프론트 엔드 프레임워크 및 Node.js 런타임으로 구성된 웹 웹 애플리케이션을 작성하려면 [MEAN 스택을 사용하는 모던 웹 애플리케이션](/docs/tutorials/mean-stack.html) 튜토리얼의 단계를 따르십시오.
