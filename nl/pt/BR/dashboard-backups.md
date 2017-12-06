---

copyright:
  years: 2017
lastupdated: "2017-09-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Backups
{: #backups}

É possível criar e fazer download de backups na página *Gerenciar* do painel de serviço do {{site.data.keyword.composeForMongoDB_full}}. Ambos os backups, planejado e manual, estão disponíveis.

## Visualizando backups existentes

Os backups diários de seu banco de dados são planejados automaticamente. Para visualizar seus backups existentes, navegue para a página *Gerenciar* de seu painel de serviço. 

![Backups](./images/mongodb-backups-show.png "A list of available backups")

Clique na linha correspondente para expandir as opções para qualquer backup disponível.
  
![Backup Options](./images/mongodb-backups-options.png "Options for a backup.") 

## Criando um backup sob demanda

Além de backups planejados, é possível criar um backup manualmente. Para criar um backup manual, navegue para a página *Gerenciar* de seu painel de serviço e clique em *Fazer backup agora*.

## Fazendo download de um backup

Para fazer download de um backup, navegue para a página *Gerenciar* de seu painel de serviço e clique em *Fazer download* na linha correspondente para o backup que você deseja fazer download.

## Usando um backup com um banco de dados local

É possível usar o backup do {{site.data.keyword.composeForMongoDB}} para executar uma cópia local de seu banco de dados.

Para obter uma restauração local de seus dados e bancos de dados:

1. Faça download de um backup do seu painel de serviço.
2. Instale as ferramentas MongoDB e MongoDB localmente.
3. Extraia os dados do backup em um novo diretório.
4. Inicie uma instância do MongoDB local com o comando `mongod --dbpath ./db` em que './db' é o diretório que contém os dados extraídos.

## Restaurando um backup

Para restaurar um backup para uma nova instância de serviço, siga as etapas para visualizar os backups existentes, em seguida, clique na linha correspondente para expandir as opções para o backup que você deseja fazer download. Clique no botão **Restaurar**. Uma mensagem é exibida para permitir que você saiba que uma restauração foi iniciada. A nova instância de serviço será automaticamente chamada "mongodb-restore-[timestamp]" e aparecerá em seu painel quando o fornecimento iniciar.
