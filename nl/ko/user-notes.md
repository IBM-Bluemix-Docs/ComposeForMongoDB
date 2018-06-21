---

copyright:
  years: 2017,2018
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# {{site.data.keyword.composeForMongoDB}}에 대한 사용자 참고사항
{: #user-notes-compose-for-mongodb}

이 참고사항에서는 {{site.data.keyword.composeForMongoDB}} 사용자가 제기한 다양한 주제를 다룹니다.

## Admin 데이터베이스

Admin 데이터베이스에 데이터를 저장하면 성능이 저하되고 데이터가 유실될 위험이 있습니다.

다른 MongoDB 설치와 같이 {{site.data.keyword.composeForMongoDB}}에는 Admin 데이터베이스가 있습니다. Admin 데이터베이스에는 다양한 추적 콜렉션과 함께 데이터베이스 내의 사용자 및 역할에 대한 정보가 들어 있습니다. 단순 MongoDB 설치에서는 다른 데이터와 함께 데이터베이스에 저장됩니다. 

{{site.data.keyword.composeForMongoDB}}에서는 MongoDB의 샤드 아키텍처를 사용하는 복원력이 향상된 아키텍처가 제공됩니다. 여기에는 샤드 간의 관리 데이터 공유가 포함됩니다. Admin 데이터베이스를 포함하는 다른 데이터베이스 클러스터를 작성하여 이를 수행할 수 있습니다.

이 클러스터 Admin 데이터베이스는 스케일에 맞게 빌드되지 않으며 저장되는 데이터의 양에 따라 급격하게 성능이 저하됩니다. 따라서 애플리케이션이 Admin 데이터베이스에 데이터를 쓰지 않도록 해야 합니다.

## 연결 문자열

클라이언트 측의 장애 복구는 애플리케이션 디자이너의 책임입니다. 장애 복구 오류를 완전히 처리하는 드라이버로 작업하지 않는 한 다음을 수행해야 합니다.

* 해당 연결 구성에서 다중 엔드포인트를 허용하는 드라이버로 작업합니다.
* 데이터베이스를 읽거나 쓰기 위한 애플리케이션 호출이 오류에 적절하게 반응하는지 확인합니다. 옵션에는 다음이 포함됩니다.
  + 오류를 로깅한 후 다시 연결
  + 다시 연결하여 오류의 원인이 된 오퍼레이션 재시도
  + 실패한 오퍼레이션을 큐에 넣고 다시 연결 스케줄






