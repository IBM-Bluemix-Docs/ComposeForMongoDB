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

# Versões

Versões Implementáveis| Versão Preferencial
----------|-----------
3.2.10, 3.2.11, 3.2.18, 3.4.10 | 3.2.18
{: caption="Tabela 1. Versões do MongoDB" caption-side="top"}

## Versão Preferencial

A versão preferencial é geralmente a mais recente do banco de dados MongoDB que está disponível para o {{site.data.keyword.composeForMongoDB}}. É a versão que é o padrão no seletor de versão suspensa na página do catálogo. Também é a versão que será provisionada automaticamente pela API se nenhuma versão for especificada na chamada.

### Novo Protocolo de Versão Preferencial

Quando uma nova versão é disponibilizada, sua liberação é anunciada e fica disponível para implementação. Após a liberação, haverá uma janela de aproximadamente 7 dias na qual a versão mais recente estará disponível, mas não é a versão preferencial. Essa janela fornece uma chance de implementar e testar a nova versão, enquanto a versão atual ainda está disponível. No final da janela de 7 dias, a nova versão é configurada como a versão preferencial ou uma nova data para essa mudança é anunciada.

A lista de versões disponíveis para provisão está na [página de catálogo](https://console.{DomainName}/catalog/services/compose-for-mongodb) do {{site.data.keyword.composeForMongoDB}}.

Para obter uma lista atual de versões disponíveis para o serviço {{site.data.keyword.composeForMongoDB}}, é possível usar o terminal [GET /2016-07/deployments/:id/versions](https://apidocs.compose.com/v1.0/reference#2016-07-get-deployments-versions).
