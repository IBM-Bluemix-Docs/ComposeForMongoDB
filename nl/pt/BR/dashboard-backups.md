---

copyright:
  years: 2017,2018
lastupdated: "2018-02-28"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Backups
{: #backups}

É possível criar e fazer download de backups na guia _Backups_ da página _Gerenciar_ de seu painel de serviço. Backups diários, mensais e on demand estão disponíveis. Eles são retidos de acordo com planejamento a seguir:

Tipo de backup|Planejamento de retenção
----------|-----------
Diárias|Os backups diários são retidos por 7 dias
Semanal|Backups semanais são retidos por 4 semanas
Mensal|Backups mensais são retidos por 3 meses
On demand|Um backup on demand é retido. O backup retido é sempre o backup on demand mais recente.
{: caption="Tabela 1. Planejamento de retenção de backup" caption-side="top"}

Planejamentos de backup e políticas de retenção são fixos. Se você precisar manter mais backups do que o planejamento de retenção permite, faça download de backups e retenha os arquivos de acordo com as suas necessidades de negócios.

## Visualizando backups existentes

Os backups diários de seu banco de dados são planejados automaticamente. Para visualizar os seus backups existentes, navegue para a página *Gerenciar* de seu painel de serviço.
  ![Backups](./images/mongodb-backups-show.png "Uma lista de backups disponíveis")

Clique na linha correspondente para expandir as opções para qualquer backup disponível.
  ![Backup Options](./images/mongodb-backups-options.png "Options for a backup.") 

### Usando a API para visualizar backups existentes

Uma lista de backups está disponível no terminal `GET /2016-07/deployments/:id/backups`. O Terminal Foundation com o ID da instância de serviço e o ID da implementação são ambos mostrados na _Visão geral_ do serviço. Por exemplo: 

```
https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
```
{: codeblock}

## Criando um backup manual

Além de backups planejados, é possível criar um backup manualmente. Para criar um backup manual, siga as etapas para visualizar os backups existentes, em seguida, clique em **Fazer backup agora** acima da lista de backups disponíveis. Uma mensagem é exibida para permitir que você saiba que um backup foi iniciado e um backup 'pendente' é incluído na lista de backups disponíveis.

### Usando a API para criar um backup

Envie uma solicitação POST para o terminal de backups para iniciar um backup manual: `POST /2016-07/deployments/:id/backups`. Ele é imediatamente retornado com o ID do recibo e as informações sobre o backup enquanto ele está sendo executado. Será necessário verificar no terminal de backups se o backup foi concluído e localizar o backup_id antes de usá-lo. Use `GET /2016-07/deployments/:id/backups/`.

## Fazendo download de um backup

Para fazer download de um backup, siga as etapas para visualizar os backups existentes, em seguida, clique na linha correspondente para expandir as opções para o backup que você deseja fazer download. Clique no botão **Download**. O arquivo compactado contém uma captura instantânea binária de seus dados para ser usada localmente.

### Usando um backup com um banco de dados local

É possível usar o backup do {{site.data.keyword.composeForMongoDB}} para executar uma cópia local de seu banco de dados.

Para obter uma restauração local de seus dados e bancos de dados:

1. Faça download de um backup do seu painel de serviço.
2. Instale as ferramentas MongoDB e MongoDB localmente.
3. Extraia os dados do backup em um novo diretório.
4. Inicie uma instância do MongoDB local com o comando `mongod --dbpath ./db` em que './db' é o diretório que contém os dados extraídos.

### Usando a API para fazer download de um backup
Localize o backup do qual gostaria de restaurar na página _Backups_ em seu serviço e copie o backup_id ou use o `GET /2016-07/deployments/:id/backups` para localizar um backup e seu backup_id por meio da API Compose. Em seguida, use o backup_id para localizar informações e um link de download para um backup específico: `GET /2016-07/deployments/:id/backups/:backup_id`.

## Restaurando um backup

Para restaurar um backup para uma nova instância de serviço, siga as etapas para visualizar os backups existentes, em seguida, clique na linha correspondente para expandir as opções para o backup que você deseja fazer download. Clique no botão **Restaurar**. Uma mensagem é exibida para permitir que você saiba que uma restauração foi iniciada. A nova instância de serviço será automaticamente chamada "mongodb-restore-[timestamp]" e aparecerá em seu painel quando o fornecimento iniciar.

### Restaurando por meio da CLI {{site.data.keyword.cloud_notm}}

Use as etapas a seguir para restaurar um backup de um serviço em execução para um novo serviço usando a CLI do {{site.data.keyword.cloud_notm}}. 
1. Se necessário, [faça download e instale-o](https://console.{DomainName}/docs/cli/index.html#overview). 
2. Localize o backup do qual gostaria de restaurar na página _Backups_ em seu serviço e copie o ID do backup.  
  **Ou**  
  Use o `GET /2016-07/deployments/:id/backups` para localizar um backup e seu ID por meio da API Compose. O Terminal base e o ID da instância de serviço são ambos mostrados na _Visão geral_ do serviço. Por exemplo: 
  ``` 
  https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
  ```  
  A resposta terá uma lista de todos os backups disponíveis para essa instância de serviço. Selecione o backup do qual gostaria de restaurar e copie seu ID.

3. Efetue login com a conta e as credenciais apropriadas. `ibmcloud login` (ou `ibmcloud login -help` para ver todas as opções de login).

4. Alterne para a sua Organização e Espaço `ibmcloud target -o "$YOUR_ORG" -s "YOUR_SPACE"`

5. Use o comando `service create` para provisionar um novo serviço e forneça o serviço de origem e o backup específico que você está restaurando em um objeto JSON. Por exemplo:
``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": "$BACKUP_ID" }'
```
  O campo _SERVICE_ deve ser compose-for-mongodb e o campo _PLAN_ deve ser Padrão ou Corporativo dependendo de seu ambiente. _SERVICE\_INSTANCE\_NAME_ é onde você colocará o nome para o seu novo serviço. O _source\_service\_instance\_id_ é o ID da instância de serviço da origem do backup; ele pode ser obtido executando `ibmcloud cf service DISPLAY_NAME --guid`, em que _DISPLAY\_NAME_ é o nome do serviço do qual o backup é feito. 
  
  Os usuários corporativos também precisarão especificar em qual cluster implementar no objeto JSON com o parâmetro `"cluster_id": "$CLUSTER_ID"`.

### Migrando para uma nova versão

Alguns upgrades de versão principal não estão disponíveis na implementação em execução atual. Será necessário provisionar um novo serviço que está executando a versão com upgrade e, em seguida, migrar seus dados nele usando um backup. Esse processo é o mesmo que a restauração de um backup acima, exceto que você especificará a versão para a qual gostaria de fazer ugrade.

``` 
ibmcloud service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": ""$BACKUP_ID", "db_version":"$VERSION_NUMBER" }'
```

Por exemplo, restaurar uma versão mais velha de um serviço do {{site.data.keyword.composeForMongoDB}} para um novo serviço executando o MongoDB 3.4.10 será semelhante ao seguinte:
```
ibmcloud service create compose-for-mongodb Standard migrated_mongo -c '{ "source_service_instance_id": "0269e284-dcac-4618-89a7-f79e3f1cea6a", "backup_id":"5a96d8a7e16c090018884566", "db_version":"3.4.10"  }'
```
