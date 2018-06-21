---

copyright:
  years: 2016,2018
lastupdated: "2018-02-16"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# 시작하기 튜토리얼
이 튜토리얼은 [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 샘플 앱을 사용함으로써, 제공된 신임 정보를 사용하여 Node.js를 사용해 {{site.data.keyword.composeForMongoDB_full}} 서비스에 연결하는 방법을 보여줍니다. 이 애플리케이션은 앱의 웹 인터페이스를 통해 제공된 데이터를 사용하여 데이터베이스를 작성하고 읽기 및 쓰기를 수행합니다.
{: shortdesc}

## 시작하기 전에

[{{site.data.keyword.cloud_notm}} 계정][ibm_cloud_signup_url]{:new_window}이 있는지 확인하십시오.

[Node.js](https://nodejs.org/) 및 [Git](https://git-scm.com/downloads) 또한 설치해야 합니다.

## 1단계: {{site.data.keyword.composeForMongoDB}} 서비스 인스턴스 작성
{: #create-service}

{{site.data.keyword.cloud_notm}} 카탈로그의 [{{site.data.keyword.composeForMongoDB}} 페이지](https://console.{DomainName}/catalog/services/compose-for-mongodb/)에서 {{site.data.keyword.composeForMongoDB}} 서비스를 작성할 수 있습니다.

서비스 이름, 서비스를 프로비저닝할 지역, 조직 및 영역을 선택하고 **데이터베이스 버전 선택** 필드에서 _최신 선호 버전_을 선택하십시오.

다음에는 서비스에 대한 가격 책정 플랜을 선택하십시오. *표준* 또는 *엔터프라이즈* 플랜을 선택할 수 있습니다. *엔터프라이즈* 플랜을 사용하면 {{site.data.keyword.composeForMongoDB}} 인스턴스를 사용 가능한 {{site.data.keyword.composeEnterprise}} 클러스터에 프로비저닝할 수 있습니다. {{site.data.keyword.composeEnterprise}}는 엔터프라이즈 준수에 필요한 보안 및 격리를 제공하며 전용 네트워킹을 사용하여 배치된 데이터베이스의 성능을 보장합니다. 세부사항은 [Compose Enterprise](../ComposeEnterprise/index.html) 문서를 참조하십시오.

## 2단계: Github로부터 Hello World 샘플 앱 복제

다음 명령을 사용하여 터미널에서 로컬 환경으로 Hello World 앱을 복제하십시오.

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```

## 3단계: 앱 종속 항목 설치

npm을 사용하여 종속 항목을 설치하십시오.

1. 터미널에서 디렉토리를 샘플 앱이 있는 디렉토리로 변경하십시오.
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```

2. `package.json` 파일에 나열된 종속 항목을 설치하십시오.
  
  ```
  npm install
  ```

## 4단계: 서비스 신임 정보 작성

앱을 {{site.data.keyword.cloud_notm}}에 푸시하기 전에 로컬에서 실행하여 {{site.data.keyword.composeForMongoDB}} 서비스 인스턴스와의 연결을 테스트할 수 있습니다. 서비스에 연결하려면 서비스 신임 정보 세트를 작성해야 합니다.

1. {{site.data.keyword.cloud_notm}} 대시보드에서 {{site.data.keyword.composeForMongoDB}} 서비스 인스턴스를 여십시오.
2. 기본 메뉴의 _서비스 신임 정보_를 선택하여 서비스 신임 정보 보기를 여십시오.
3. **새 신임 정보**를 클릭하십시오.
4. 신임 정보의 이름을 선택하고 **추가**를 클릭하십시오.
5. 이제 새 신임 정보가 나열됩니다. 테이블의 해당 행에서 **신임 정보 보기**를 클릭하고 **복사** 아이콘을 클릭하여 신임 정보를 복사하십시오.
6. 원하는 편집기에서 다음과 같이 새 파일을 작성하고 표시되어 있는 바와 같이 신임 정보를 삽입하십시오.

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credentials": INSERT YOUR CREDENTIALS HERE
        }
      ]
    }
  }
  ```
6. 이 파일을 샘플 앱이 있는 디렉토리에 `vcap-local.json`으로 저장하십시오.

애플리케이션을 Github 또는 {{site.data.keyword.cloud_notm}}에 푸시할 때 실수로 신임 정보를 노출시키는 것을 방지하기 위해, 신임 정보를 포함하는 파일이 관련 무시 파일에 나열되도록 해야 합니다. 애플리케이션 디렉토리의 `.cfignore` 및 `.gitignore`를 열면 `vcap-local.json`이 Github 또는 {{site.data.keyword.cloud_notm}}에 앱을 푸시할 때 업로드되는 파일에 포함되지 않도록 이들 두 파일 모두에 나열되어 있는 것을 볼 수 있습니다.
{: .tip}

## 5단계: 로컬에서 앱 실행

```
npm start
```

앱이 이제 [http://localhost:8080](http://localhost:8080)에서 실행 중입니다. {{site.data.keyword.composeForMongoDB}} 데이터베이스에 단어 및 정의를 추가할 수 있습니다. 앱을 중지하고 다시 시작하면 페이지를 새로 고칠 때 이미 추가한 단어가 표시됩니다.

다음 단계는 서비스 인스턴스에 앱을 연결하고 앱을 {{site.data.keyword.cloud_notm}}에 배치하는 것입니다.

## 6단계: {{site.data.keyword.cloud_notm}} CLI 도구 다운로드 및 설치

터미널 또는 명령행에서 {{site.data.keyword.cloud_notm}}와 통신하는 데는 {{site.data.keyword.cloud_notm}} CLI 도구를 사용합니다. 세부사항은 [{{site.data.keyword.cloud_notm}} CLI 다운로드 및 설치](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html)를 참조하십시오.

## 7단계: {{site.data.keyword.cloud_notm}}에 연결

1. 명령행 도구에서 {{site.data.keyword.cloud_notm}}에 연결하고 프롬프트에 따라 로그인하십시오.

  ```
  bx login
  ```

  연합 사용자 ID가 있는 경우에는 `bx login --sso` 명령을 사용하여 싱글 사인온 ID로 로그인하십시오. 더 자세히 알아보려면 [연합 ID를 사용하여 로그인](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id)을 참조하십시오.
  {: .tip}

2. 올바른 {{site.data.keyword.cloud_notm}} 조직 및 영역을 대상으로 하고 있는지 확인하십시오.

  ```
  bx target --cf
  ```

  서비스를 작성할 때 사용한 것과 동일한 값을 사용하여 제공된 옵션 중에서 선택하십시오.

## 8단계: 앱의 Manifest 파일 업데이트
{: #update-manifest}

{{site.data.keyword.cloud_notm}}에서는 Manifest 파일 `manifest.yml`을 사용하여 애플리케이션과 서비스를 연관시킵니다. Manifest 파일을 작성하려면 다음 단계를 따르십시오.

1. 편집기에서 새 파일을 열고 다음 항목을 추가하십시오.

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. `host` 값을 고유한 값으로 변경하십시오. 선택하는 호스트에 따라 애플리케이션 URL의 하위 도메인이 결정됩니다(`<host>.mybluemix.net`).
3. `name` 값을 변경하십시오. 선택하는 값은 {{site.data.keyword.cloud_notm}} 대시보드에 표시될 때 앱의 이름입니다.
4. `services` 값을 [{{site.data.keyword.composeForMongoDB}} 서비스 인스턴스 작성](#create-service)에서 작성한 서비스의 이름과 일치하도록 업데이트하십시오. 

## 9단계: 앱을 {{site.data.keyword.cloud_notm}}에 푸시

앱을 푸시하면 자동으로 앱이 Manifest 파일에 지정된 서비스에 바인드됩니다.

```
bx cf push
```

## 10단계: 앱이 {{site.data.keyword.composeForMongoDB}} 서비스에 연결되었는지 확인

1. {{site.data.keyword.composeForMongoDB}} 서비스 대시보드로 이동하십시오.
2. 대시보드 메뉴에서 _연결_을 선택하십시오. 사용자의 애플리케이션이 _연결된 애플리케이션_에 나열되어 있어야 합니다.

사용자의 애플리케이션이 나열되어 있지 않은 경우에는 올바른 세부사항을 [manifest.yml](#update-manifest)에 입력했는지 확인하고 7단계 및 8단계를 반복하십시오.

## 11단계: 앱 사용

이제 `<host>.mybluemix.net/`에 방문하면 {{site.data.keyword.composeForMongoDB}} 콜렉션의 컨텐츠를 볼 수 있습니다. 단어 및 해당 정의를 추가하면 이들이 데이터베이스에 추가되며 표시됩니다. 앱을 중지하고 다시 시작하면 이미 추가한 단어 및 정의가 나열되어 있는 것을 볼 수 있습니다.


## 다음 단계

[compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) 샘플 앱이 어떻게 작동하는지에 대해 더 자세히 알아보려는 경우에는 앱의 기능에 대한 정보를 제공하는 이 애플리케이션의 readme 파일, 또는 `server.js`의 코드 주석을 읽을 수 있습니다.

{{site.data.keyword.composeForMongoDB}} 서비스 탐색을 시작하려면 서비스 대시보드에 대한 다음 주제를 참조하십시오.

- [대시보드 개요](./dashboard-overview.html)
- [백업](./dashboard-backups.html)
- [설정](./dashboard-settings.html)

애플리케이션의 서비스 연결을 위해 작성한 신임 정보에 대한 정보는 [사용 가능한 신임 정보](./connecting-bluemix-app.html#available-credentials)를 참조하십시오.

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
