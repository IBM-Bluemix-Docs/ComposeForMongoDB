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

# Notas do usuário para o {{site.data.keyword.composeForMongoDB}}
{: #user-notes-compose-for-mongodb}

Essas notas cobrem vários tópicos que foram levantados por usuários do {{site.data.keyword.composeForMongoDB}}.

## O banco de dados do Administrador

Se você armazenar dados no banco de dados do Administrador, ele não será bem executado e você arriscará a perda de dados.

Como outras instalações do MongoDB, o {{site.data.keyword.composeForMongoDB}} tem um banco de dados do Administrador. O banco de dados do Administrador contém informações sobre usuários e funções dentro do banco de dados juntamente com várias coletas de rastreamento. Em uma instalação simples do MongoDB, isso é armazenado juntamente com os outros dados no banco de dados. 

No {{site.data.keyword.composeForMongoDB}}, uma arquitetura mais resiliente usando a arquitetura compartilhada do MongoDB. Isso envolve compartilhar dados de administração entre shards. Isso é obtido criando um outro cluster de banco de dados que contém o banco de dados do Administrador.

Esse banco de dados do Administrador do cluster não é construído para escala e vai se comprometer rapidamente com qualquer quantidade de dados sendo armazenada nele. É, portanto, imperativo que você assegure que os aplicativos não gravem dados no banco de dados do Administrador.

## Sequências de conexões

Failover no lado do cliente é responsabilidade do designer de aplicativo. A não ser que esteja trabalhando com um driver que manipule completamente erros de failover será necessário:

* Trabalhar com um driver que aceite múltiplos terminais em sua configuração de conexão.
* Assegurar que as suas chamadas de aplicativos leiam ou gravem adequadamente na reação do banco de dados a erros. As opções incluem:
  + Criar log do erro e reconectar.
  + Reconectar e tentar novamente a operação que causou o erro.
  + Enfileirar a operação com falha e planejar a reconexão.






