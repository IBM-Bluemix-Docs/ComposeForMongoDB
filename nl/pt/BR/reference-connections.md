---
copyright:
  years: 2016,2018
lastupdated: "2018-06-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuração da Conexão
{: #connection-configuration}

As conexões com o banco de dados em clusters sharded MongoDB são manipuladas por roteadores mongos. Os serviços do {{site.data.keyword.composeForMongoDB}} vêm com dois desses roteadores com 64 MB de memória por parte. O HAProxy foi incluído nos roteadores Mongo, fornecendo uma avenida para conexões TLS/SSL seguras. Os dois portais que permitem que os aplicativos mantenham a conectividade no caso de um deles se torne inacessível. Quase todos os drivers MongoDB manipulam múltiplos pontos de conexão, mas o failover no lado do cliente é responsabilidade do designer de aplicativo. A não ser que esteja trabalhando com um driver que manipule completamente erros de failover será necessário:
* Trabalhar com um driver que aceite múltiplos terminais em sua configuração de conexão.
* Assegurar que as suas chamadas de aplicativos leiam ou gravem adequadamente na reação do banco de dados a erros. As opções incluem:
  + Criar log do erro e reconectar.
  + Reconectar e tentar novamente a operação que causou o erro.
  + Enfileirar a operação com falha e planejar a reconexão.

## Criptografia em Trânsito

Todos os portais HAProxy do {{site.data.keyword.composeForMongoDB}} têm o TLS/SSL ativado e suportam o TLS versão 1.2. Os certificados para o serviço são certificados autoassinados. Talvez você tenha que salvar uma cópia local do certificado de seu serviço e fornecer seu local para o driver de seus aplicativos para fazer uma conexão segura.

## Limites de Conexão

Os dois roteadores mongos têm um limite de 5.000 conexões por mongos. Exceder o limite de conexão tornará seu serviço não responsivo. É possível gerenciar conexões de seu aplicativo por meio do recurso de definição do conjunto de conexões do driver.

## Tempos limite de proxy

Os roteadores mongos gerenciam o ciclo de vida de conexões entre o servidor e o cliente. Nós as detalhamos aqui como uma referência para gravadores de driver e outros que têm interesse na atividade de baixo nível de conexões com o banco de dados do {{site.data.keyword.composeForMongoDB}}.

Configurando | Value | Aplica
----------|-----------|-----------
cliente | 5m | Quando se espera que o cliente reconheça ou envie dados.
conectar | 4s | Quando uma conexão está sendo feita entre o proxy e o servidor.
server | 5m | Quando se espera que o servidor reconheça ou envie dados.
túnel | 1d | Quando uma conexão bidirecional é estabelecida entre um cliente e um servidor e a conexão permanece inativa em ambas as direções.
cliente-fin | 1h | Quando se espera que o cliente reconheça ou envie dados enquanto uma direção já está encerrada.
check | 5s | Como uma verificação de tempo limite secundário quando uma conexão está sendo feita entre o proxy e o servidor.
{: caption="Tabela 1. Tempos limite do roteador Mongos" caption-side="top"}
