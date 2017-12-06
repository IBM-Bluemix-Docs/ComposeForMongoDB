---

Copyright:
  Years: 2017
lastupdated: "2017-06-17"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Conectando um aplicativo {{site.data.keyword.cloud_notm}}

Para conectar um aplicativo {{site.data.keyword.cloud}} a seu serviço, use as credenciais de serviço que são criadas quando o serviço é provisionado. O aplicativo de amostra demostra como usar o Node.js para se conectar a um serviço {{site.data.keyword.composeForMongoDB_full}} usando as credenciais fornecidas e como criar um banco de dados e ler e gravar no banco de dados.

## Conectando usando o aplicativo de amostra 'Hello World'

O aplicativo de amostra [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-mongodb-helloworld-nodejs) demonstra como usar o Node.js para se conectar a um serviço {{site.data.keyword.composeForMongoDB}} usando as credenciais fornecidas. O aplicativo cria, lê e grava em um banco de dados.

Faça download do aplicativo de amostra e siga as instruções no arquivo leia-me. Em seguida, em sua página de detalhes do aplicativo no {{site.data.keyword.cloud_notm}}, clique em **Visualizar app** para visualizar os conteúdos da tabela *exemplos*.

## Credenciais disponíveis

Campo de nome|Descrição
----------|-----------
`uri`|O URI a ser usado na conexão com o serviço. `uri` inclui o esquema (`mongodb:`), o nome do usuário administrativo e a senha, o nome do host do servidor, o número da porta à qual se conectar, o nome do banco de dados e `?ssl=true` para ativar conexões SSL.
`uri_cli`|Uma linha de comando shell `mongo` que se conecta à instância de banco de dados.
`ca_certificate_base64`|Um certificado autoassinado que é usado para confirmar se um app está se conectando ao servidor apropriado. O certificado é codificado em base64. Deve-se decodificar a chave antes de usá-la, conforme mostrado no app de amostra.
`deployment_id`|Um identificador interno para o serviço conforme criado no Compose.
`db_type`|O tipo de banco de dados que é oferecido pelo serviço: nesse caso, `mongodb`.
`name`|O nome da implementação do banco de dados.
{: caption="Tabela 1. Credenciais do Compose for MongoDB" caption-side="top"}
