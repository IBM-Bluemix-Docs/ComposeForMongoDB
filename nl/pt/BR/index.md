---

Copyright:
  Years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Introdução ao Compose for MongoDB
{: #getting-started-with-compose-for-mongodb}

O {{site.data.keyword.composeForMongoDB_full}} usa indexação e
consulta poderosas, agregação e amplo suporte a driver do MongoDB que fizeram dele
o melhor armazenamento de dados JSON para muitas startups e empresas. O
{{site.data.keyword.composeForMongoDB}} oferece um sistema de implementação
fácil de auto-scaling. Ele entrega alta disponibilidade e redundância, backups contínuos
automatizados e sob demanda, ferramentas de monitoramento, integração em sistemas de
alerta, visualizações de análise de desempenho e muito mais, tudo isso em uma interface
com o usuário limpa e simples.
{:shortdesc}

**Nota:** todas as instâncias de serviço do Compose que foram
provisionadas antes de 14 de setembro de 2016 que ainda estiverem ativas poderão ser
usadas e acessadas diretamente em
[https://www.compose.com/](https://www.compose.com). Qualquer instância de serviço do Compose fornecida deste ponto em diante é acessada e usada diretamente dentro de sua conta do {{site.data.keyword.cloud}}.

## Criando uma instância de serviço do Compose for MongoDB

[Crie
uma instância do {{site.data.keyword.composeForMongoDB}}](https://console.ng.bluemix.net/catalog/services/compose-for-mongodb/).

Ao criar uma instância do serviço, assegure-se de escolher um nome para seu
serviço e um nome de credencial. Deixe o serviço desvinculado; é possível conectar um
aplicativo ao seu serviço mais tarde usando as credenciais que são fornecidas quando o
serviço é provisionado.  Os diversos valores de credenciais são listados na seção
*Credenciais disponíveis*.

Quando você provisiona sua instância do {{site.data.keyword.composeForMongoDB}}, é possível escolher os planos *Padrão* ou *Corporativo*. Com o plano *Corporativo*, é possível provisionar sua instância do {{site.data.keyword.composeForMongoDB}} em um cluster disponível do {{site.data.keyword.composeEnterprise}}. O {{site.data.keyword.composeEnterprise}} fornece a segurança e o isolamento requeridos pela conformidade corporativa e usa rede dedicada para assegurar o desempenho dos bancos de dados implementados. Veja a [Documentação do Compose Enterprise](../ComposeEnterprise/index.html) para obter mais detalhes.

## Gerenciando o Compose for MongoDB

É possível gerenciar seu serviço no painel de serviço. Aqui é possível localizar informações sobre o banco de dados do {{site.data.keyword.cloud_notm}} Compose e como conectar-se a ele. Também é possível:
- gerenciar seus backups
- alocar mais recursos para seu serviço
- use listas de desbloqueio para restringir o acesso a seus bancos de dados. 
Para obter mais informações, veja [Configurações](./dashboard-settings.html).

## Conectando-se ao Compose for MongoDB

É possível se conectar a seu serviço usando as credenciais que são criadas junto com o serviço ou com as sequências de conexões e linha de comandos que são fornecidas na guia *Visão geral* de seu painel de serviço.

## Conectando um aplicativo {{site.data.keyword.cloud_notm}} ao Compose for MongoDB

Para conectar um aplicativo {{site.data.keyword.cloud_notm}} a seu serviço, use as credenciais que são criadas com o serviço. É possível localizar informações sobre como conectar um aplicativo {{site.data.keyword.cloud_notm}} a um serviço {{site.data.keyword.composeForMongoDB}} em [Conectando um aplicativo {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Conectando-se ao Compose for MongoDB de fora do {{site.data.keyword.cloud_notm}}

Se você deseja se conectar ao {{site.data.keyword.composeForMongoDB}} de fora do {{site.data.keyword.cloud_notm}}, é possível usar as sequências de conexões fornecidas ou a linha de comandos. É possível localizar informações sobre como conectar-se em [Conectando um aplicativo externo](./connecting-external.html).
