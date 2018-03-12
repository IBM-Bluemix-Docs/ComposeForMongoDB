---

copyright:
  years: 2016,2018
lastupdated: "2017-11-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o {{site.data.keyword.composeForMongoDB}}
{: #about-compose-for-mongodb}

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

## Criando uma instância de serviço do {{site.data.keyword.composeForMongoDB}}

É possível criar um serviço do {{site.data.keyword.composeForMongoDB}} por meio da página [{{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) no catálogo do {{site.data.keyword.cloud_notm}}.

Escolha um nome do serviço e uma região, organização e espaço no qual provisionar o serviço. É possível usar o campo **Selecionar uma versão do banco de dados** para escolher nas versões do banco de dados disponíveis.

Quando você provisiona sua instância do {{site.data.keyword.composeForMongoDB}}, é possível escolher os planos *Padrão* ou *Corporativo*. Com o plano *Corporativo*, é possível provisionar sua instância do {{site.data.keyword.composeForMongoDB}} em um cluster disponível do {{site.data.keyword.composeEnterprise}}. O {{site.data.keyword.composeEnterprise}} fornece a segurança e o isolamento requeridos pela conformidade corporativa e usa rede dedicada para assegurar o desempenho dos bancos de dados implementados. Veja a [Documentação do Compose Enterprise](../ComposeEnterprise/index.html) para obter mais detalhes.

## Gerenciando {{site.data.keyword.composeForMongoDB}}

É possível gerenciar seu serviço no painel de serviço. Aqui é possível localizar informações sobre o banco de dados do Compose do {{site.data.keyword.cloud_notm}} e como conectar-se a ele. Também é possível:
- gerenciar seus backups
- alocar mais recursos para seu serviço
- use listas de desbloqueio para restringir o acesso a seus bancos de dados. 
Para obter mais informações, veja [Configurações](./dashboard-settings.html).

## Conectando-se ao {{site.data.keyword.composeForMongoDB}}

É possível se conectar a seu serviço usando as credenciais que são criadas junto com o serviço ou com as sequências de conexões e linha de comandos que são fornecidas na guia *Visão geral* de seu painel de serviço.

## Conectando um aplicativo {{site.data.keyword.cloud_notm}} ao {{site.data.keyword.composeForMongoDB}}

Para conectar um aplicativo {{site.data.keyword.cloud_notm}} a seu serviço, use as credenciais que são criadas com o serviço. É possível localizar informações sobre como conectar um aplicativo {{site.data.keyword.cloud_notm}} a um serviço {{site.data.keyword.composeForMongoDB}} em [Conectando um aplicativo {{site.data.keyword.cloud_notm}}](./connecting-bluemix-app.html).

## Conectando-se ao {{site.data.keyword.composeForMongoDB}} de fora do {{site.data.keyword.cloud_notm}}

Se você deseja se conectar ao {{site.data.keyword.composeForMongoDB}} de fora do {{site.data.keyword.cloud_notm}}, é possível usar as sequências de conexões fornecidas ou a linha de comandos. É possível localizar informações sobre como conectar-se em [Conectando um aplicativo externo](./connecting-external.html).

## Criando um aplicativo da web usando a pilha MEAN

É possível usar o {{site.data.keyword.composeForMongoDB}} como parte de uma pilha MEAN para criar um aplicativo da web. Siga as etapas no tutorial [Aplicativo moderno da web usando a pilha MEAN](../../tutorials/mean-stack.html) para criar um aplicativo da web composto por um Mongo DB, uma estrutura da web Express, uma estrutura de front-end Angular e um tempo de execução Node.js.
