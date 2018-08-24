---

copyright:
  years: 2016,2018
lastupdated: "2018-01-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Preços
{: #pricing}

## Configuração base

Um serviço {{site.data.keyword.composeForMongoDB_full}} começa com um conjunto de réplicas de três nós de dados, em uma configuração de um único shard. Cada nó tem 1 GB de armazenamento e 102 MB de memória, que é igual a 1 unidade de recursos. O serviço _inclui_ replicação e alta disponibilidade, de maneira que cada unidade e o preço por unidade _inclua_ o custo dos recursos em todos os três nós.

Também estão incluídos dois roteadores mongos para gerenciar conexões e alta disponibilidade em 64 MB de memória cada um.

A configuração de serviço de base tem um preço definido. Consulte o ladrilho de catálogo no {{site.data.keyword.cloud_notm}} para precificação base em sua moeda local. Por exemplo, o preço base em dólares americanos é $18/mês.

## Aumentando recursos

Se você precisar de armazenamento ou memória adicional para o seu serviço, será possível aumentar os recursos alocados em uma razão 10:1 de armazenamento em disco para unidade de memória. Aumentar o disco alocado para a implementação também aumenta a RAM alocada. Uma unidade do {{site.data.keyword.composeForMongoDB}} é 1 GB de armazenamento para 102 MB de memória e cada unidade e o preço por unidade _incluem_ o custo para aumentar os recursos em todos os três nós de dados. 

## Calculando o custo de sua implementação
{: #tiered-pricing}

Cada unidade adicional (1 GB de armazenamento/102 MB de memória) tem um preço unitário, que é listado em sua moeda local no ladrilho de catálogo do {{site.data.keyword.cloud_notm}} para o serviço. Em dólares americanos cada unidade adicional custa $18. Conforme o tamanho _total_ de todos os serviços do {{site.data.keyword.composeForMongoDB}} aumenta, o preço por unidade diminui, conforme mostrado na tabela de precificação em camadas.

Número de unidades|Preço por unidade
----------|-----------
1 - 9 unidades|base por preço unitário -- US$18,00/Unidade
10 - 24 unidades|redução de 10% -- US$16,20/Unidade
25 - 49 unidades|redução de 20% -- US$14,40/Unidade
50 - 99 unidades|redução de 30% -- US$12,60/Unidade
100 - 499 unidades|redução de 40% -- US$10,80/Unidade
500 - 999 unidades|redução de 50% -- US$9,00/Unidade
1.000 - 4.999 unidades|redução de 60% -- US$7,20/Unidade
+ de 5.000 unidades|redução de 70% -- US$ 5,40/GB
{: caption="Tabela 1. Precificação em camadas do {{site.data.keyword.composeForMongoDB}}" caption-side="top"}
