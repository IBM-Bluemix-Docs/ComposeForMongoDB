---

Copyright:
  years: 2017,2018
lastupdated: "2017-11-20"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Visão geral do serviço

A página _Visão geral_ mostra informações sobre seu banco de dados do Compose do {{site.data.keyword.cloud}}. A visão geral inclui informações essenciais de identificação e o uso do recurso atual. Você também localizará uma seção para sequências de conexões que podem ser usadas com ferramentas ou fazer uso de ferramentas para se conectar a seu banco de dados.

## Detalhes da implementação

O painel _Detalhes da implementação_ mostra detalhes de seu serviço.

![Deployment Details](./images/mongodb-deployment-details.png "A view of the Deployment Details panel")

### Tipo

O tipo de banco de dados que é oferecido pelo serviço e a versão do banco de dados que seu serviço usa. Se uma versão de banco de dados mais recente estiver disponível, uma notificação será exibida, junto a um link para a seção [Fazer upgrade da versão](/docs/services/ComposeForMongoDB/dashboard-settings.html#upgrade-version) de seu painel de serviço.

### Nome

Um identificador interno para o serviço.

### Uso

O tamanho de seu banco de dados e a quantia de armazenamento fornecido por seu plano de serviço.

## Tarefas atuais

Fazer mudanças administrativas em seu serviço (como ajuste de escala ou execução de um backup manual) inicia uma tarefa. Enquanto uma tarefa estiver em execução, o painel _Tarefas atuais_ aparecerá na página _Visão geral_, mostrando o nome da tarefa e uma barra de progresso. Quando a tarefa for concluída, o painel _Tarefas atuais_ não aparecerá mais na página _Visão geral_.

## Sequências de conexões

É possível localizar sequências de conexões disponíveis para seu serviço em um conjunto de guias no painel _Sequências de conexões_. É possível descobrir como usar as sequências de conexões para se conectar ao seu serviço em [Conectando um aplicativo externo](./connecting-external.html).

### HTTPS

A sequência de conexões **HTTPS** pode ser usada por algumas bibliotecas do cliente e contém todas as informações necessárias para outras bibliotecas se conectarem.

### Sequência de conexões

É possível usar a sequência de conexões diretamente em um aplicativo para se conectar ao {{site.data.keyword.composeForMongoDB}} ou configurá-la como uma variável de ambiente. Para fazer isso, execute o seguinte no shell:

```
export MONGODB_URL="<CONNECTION STRING>"
```

### Linha de comandos

A **Linha de comandos** é um comando pré-formatado que chamará o `mongo` com os parâmetros corretos. Para usá-la, você precisará ter as ferramentas mongo instaladas no sistema local. É possível descobrir mais sobre como fazer isso em [Conectando um aplicativo externo](./connecting-external.html).

### Certificado SSL

Seu serviço Compose {{site.data.keyword.cloud_notm}} fornece um certificado SSL que é possível usar para conectar-se a seu banco de dados.


## API de administração de instância

É possível gerenciar o serviço {{site.data.keyword.composeForMongoDB}} por meio da API do {{site.data.keyword.cloud_notm}} Compose.

### Terminal de base

O terminal base é composto pela região na qual o serviço reside e pelo ID da instância de serviço. Ela estará no início de cada terminal.

### ID de implementação

O ID de implementação é necessário para a maioria das chamadas e identifica a instância de implementação específica.

### Referência

Para obter mais documentação e referência para usar a API Compose do {{site.data.keyword.cloud_notm}}, ao longo de todos os serviços Compose do {{site.data.keyword.cloud_notm}}, leia [A API Compose do {{site.data.keyword.cloud_notm}}](https://www.compose.com/articles/the-ibm-cloud-compose-api/).
