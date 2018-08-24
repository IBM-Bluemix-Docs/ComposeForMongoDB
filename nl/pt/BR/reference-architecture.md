---
copyright:
  years: 2016,2018
lastupdated: "2018-06-11"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Arquitetura 
{: #architecture}

## Replicação

Um serviço {{site.data.keyword.composeForMongoDB_full}} vem em um conjunto de réplicas de três nós dados, configurados como um único shard. Você seleciona uma região na qual o serviço é implementado, os três nós de dados são distribuídos pelas zonas de disponibilidade da região e, em seguida, seus dados são replicados nos três. O MongoDB envia por push todas as operações de gravação para o nó de dados primário e, em seguida, propaga-as (por meio de oplog) para o nó de dados secundário no shard. Se o nó primário falhar, o nó secundário será promovido para o primário. Se qualquer um dos membros de dados se tornar inacessível, seu shard MongoDB continuará a operar normalmente.

## Criptografia de Disco

Todos os serviços do {{site.data.keyword.composeForMongoDB}} têm criptografia em repouso. Seus dados residem em servidores que possuem criptografia em nível de volume ativada. 

Como este {{site.data.keyword.composeForMongoDB}} é um serviço gerenciado, é possível que os operadores do {{site.data.keyword.cloud}} Compose vejam dados. Além da criptografia de disco, recomendamos que, se você estiver armazenando informações pessoais, as criptografe antes de armazená-las no banco de dados ou use extensões ou recursos para ativar a criptografia no próprio banco de dados. Embora esses métodos possam afetar a usabilidade ou o desempenho, é uma boa prática assegurar-se de que as informações pessoais sejam protegidas com criptografia.

## Auto-scaling

Os recursos são escalados automaticamente com base no uso total de armazenamento em disco dos bancos de dados MongoDB. A memória baseia-se em uma razão de disco provisionado em 1:4. Esses recursos são empacotados como unidades.

O Auto-scaling foi projetado para responder às tendências de curto a médio prazo de seu banco de dados. A cada hora, seu serviço será verificado e, se ele estiver sendo executado com pouco espaço em disco, mais unidades serão alocadas para a implementação. 

O Auto-scaling não reduz a capacidade de implementações em que o uso de disco/memória foi reduzido. Os recursos provisionados para seus bancos de dados permanecerão para suas necessidades futuras ou até você reduzir a capacidade de sua implementação manualmente.
{: .tip}

## O banco de dados do Administrador

Se você armazenar dados no banco de dados do Administrador, ele não será bem executado e você arriscará a perda de dados.

Como outras instalações do MongoDB, o {{site.data.keyword.composeForMongoDB}} tem um banco de dados do Administrador. O banco de dados do Administrador contém informações sobre usuários e funções dentro do banco de dados juntamente com várias coletas de rastreamento. Em uma instalação simples do MongoDB, isso é armazenado juntamente com os outros dados no banco de dados. 

No {{site.data.keyword.composeForMongoDB}}, uma arquitetura mais resiliente usando a arquitetura compartilhada do MongoDB. Isso envolve compartilhar dados de administração entre shards. Isso é obtido criando um outro cluster de banco de dados que contém o banco de dados do Administrador.

Esse banco de dados do Administrador do cluster não é construído para escala e vai se comprometer rapidamente com qualquer quantidade de dados sendo armazenada nele. É, portanto, imperativo que você assegure que os aplicativos não gravem dados no banco de dados do Administrador.
