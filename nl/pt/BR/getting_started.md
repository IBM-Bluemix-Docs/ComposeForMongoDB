---

copyright:
  years: 2016,2018
lastupdated: "2018-02-16"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Tutorial de introdução
Este tutorial usa o app de amostra [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) para demonstrar como usar o Node.js para se conectar a um serviço {{site.data.keyword.composeForMongoDB_full}} usando as credenciais fornecidas. O aplicativo cria, lê e grava em um banco de dados utilizando os dados fornecidos por meio da interface da web do app.
{: shortdesc}

## Antes de iniciar

É necessário ter uma conta do [{{site.data.keyword.cloud_notm}}][ibm_cloud_signup_url]{:new_window}.

Também é necessário instalar o [Node.js](https://nodejs.org/) e [Git](https://git-scm.com/downloads).

## Etapa 1: Criar uma instância de serviço do {{site.data.keyword.composeForMongoDB}}
{: #create-service}

É possível criar um serviço do {{site.data.keyword.composeForMongoDB}} por meio da página [{{site.data.keyword.composeForMongoDB}}](https://console.{DomainName}/catalog/services/compose-for-mongodb/) no catálogo do {{site.data.keyword.cloud_notm}}.

Escolha um nome do serviço e uma região, organização e espaço no qual provisionar o serviço e para o campo **Selecionar uma versão do banco de dados**, escolha _Versão preferencial mais recente_.

Em seguida, escolha um plano de precificação para o seu serviço. É possível escolher os planos *Padrão* ou *Corporativo*. Com o plano *Corporativo*, é possível provisionar sua instância do {{site.data.keyword.composeForMongoDB}} em um cluster disponível do {{site.data.keyword.composeEnterprise}}. O {{site.data.keyword.composeEnterprise}} fornece a segurança e o isolamento requeridos pela conformidade corporativa e usa rede dedicada para assegurar o desempenho dos bancos de dados implementados. Veja a documentação do [Compose Enterprise](../ComposeEnterprise/index.html) para obter mais detalhes.

## Etapa 2: Clonar o aplicativo de amostra Hello World por meio do Github

Clone o app Hello World para o seu ambiente local do seu terminal usando o comando a seguir:

```
git clone https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs.git
```

## Etapa 3: Instalar as dependências do app

Use npm para instalar dependências.

1. De seu terminal, mude o diretório para o local em que o app de amostra está localizado.
  
  ```
  cd compose-mongodb-helloworld-nodejs
  ```

2. Instale as dependências listadas no arquivo `package.json`.
  
  ```
  npm install
  ```

## Etapa 4: Criar credenciais de serviço

Antes de enviar o app por push para o {{site.data.keyword.cloud_notm}}, é possível executá-lo localmente para testar a conexão com a instância de serviço do {{site.data.keyword.composeForMongoDB}}. Para se conectar ao serviço será necessário criar um conjunto de credenciais de serviço.

1. No painel do {{site.data.keyword.cloud_notm}}, abra sua instância de serviço do {{site.data.keyword.composeForMongoDB}}.
2. Selecione _Credenciais de serviço_ no menu principal para abrir a visualização Credenciais de serviço.
3. Clique em **Nova credencial**.
4. Escolha um nome para as suas credenciais e clique em **Incluir**.
5. As suas novas credenciais agora estão listadas. Clique em **Visualizar credenciais** na linha correspondente da tabela para visualizar as credenciais e clique no ícone **Copiar** para copiar as suas credenciais.
6. Em seu editor de escolha, crie um novo arquivo com o seguinte, inserindo as suas credenciais conforme mostrado:

  ```
  {
    "services": {
      "compose-for-mongodb": [
        {
          "credenciais": INSIRA AS SUAS CREDENCIAIS AQUI }
      ]
    }
  }
  ```
6. Salve o arquivo como `vcap-local.json` no diretório no qual o app de amostra está localizado.

Para evitar expor acidentalmente as suas credenciais ao enviar por push um aplicativo para o Github ou o {{site.data.keyword.cloud_notm}}, certifique-se de que o arquivo que contém as suas credenciais seja listado no arquivo ignorar relevante. Se você abrir `.cfignore` e `.gitignore` em seu diretório de aplicativo verá que `vcap-local.json` é listado em ambos, de maneira que isso não será incluído nos arquivos que serão transferidos por upload quando você enviar por push o app para Github ou {{site.data.keyword.cloud_notm}}.
{: .tip}

## Etapa 5: Executar o app localmente

```
npm start
```

O app agora está em execução em [http://localhost:8080](http://localhost:8080). É possível incluir palavras e definições no banco de dados do {{site.data.keyword.composeForMongoDB}}. Quando você parar e reiniciar o app, qualquer palavra já incluída será exibida ao atualizar a página.

O próximo estágio é conectar o seu app à sua instância de serviço e implementar o app no {{site.data.keyword.cloud_notm}}.

## Etapa 6: Fazer download e instalar a ferramenta CLI do {{site.data.keyword.cloud_notm}}

A ferramenta da CLI do {{site.data.keyword.cloud_notm}} é o que você vai usar para se comunicar com o {{site.data.keyword.cloud_notm}} de seu terminal ou linha de comandos. Para obter detalhes, veja [Faça download e instale a CLI do {{site.data.keyword.cloud_notm}}](https://console.{DomainName}/docs/cli/reference/bluemix_cli/download_cli.html).

## Step 7: Conectar ao {{site.data.keyword.cloud_notm}}

1. Conecte-se ao {{site.data.keyword.cloud_notm}} na ferramenta de linha de comandos e siga os prompts para efetuar login.

  ```
  bx login
  ```

  Se você tiver um ID do usuário federado, use o comando `bx login --sso` para efetuar login com o seu ID de conexão única. Veja [Efetuando login com um ID federado](https://console.{DomainName}/docs/cli/login_federated_id.html#federated_id) para saber mais.
  {: .tip}

2. Certifique-se de que você esteja direcionando para a organização e o espaço do {{site.data.keyword.cloud_notm}} corretos.

  ```
  bx target --cf
  ```

  Escolha nas opções fornecidas, usando os mesmos valores usados ao criar o serviço.

## Etapa 8: Atualizar o arquivo manifest do app
{: #update-manifest}

O {{site.data.keyword.cloud_notm}} usa um arquivo manifest - `manifest.yml` para associar um aplicativo com um serviço. Siga estas etapas para criar o seu arquivo manifest.

1. Em um editor, abra um novo arquivo e inclua o seguinte:

  ```
  ---
  applications:
  - name:    compose-mongodb-helloworld-nodejs
    host:    compose-mongodb-helloworld-nodejs
    memory:  128M
    services:
      - my-compose-for-mongodb-service
  ```

2. Mude o valor `host` para algo exclusivo. O host que você escolher determinará o subdomínio da URL do seu aplicativo: `<host>.mybluemix.net`.
3. Mude o valor `name`. O valor que você escolher será o nome do app como aparece em seu painel do {{site.data.keyword.cloud_notm}}.
4. Atualize o valor `services` para corresponder ao nome do serviço criado em [Criar uma instância de serviço do {{site.data.keyword.composeForMongoDB}}](#create-service). 

## Etapa 9: Enviar por push o app para o {{site.data.keyword.cloud_notm}}.

Quando você enviar por push o app, ele será automaticamente ligado ao serviço especificado no arquivo manifest.

```
bx cf push
```

## Etapa 10: Verificar se o app está conectado ao serviço {{site.data.keyword.composeForMongoDB}}

1. Navegue até o painel de serviço do {{site.data.keyword.composeForMongoDB}}
2. Selecione _Conexões_ no menu do painel. O seu aplicativo deve ser listado sob _Aplicativos conectados_.

Se o seu aplicativo não estiver listado, repita as Etapas 7 e 8, inserindo os detalhes corretos no [manifest.yml](#update-manifest).

## Etapa 11: Usar o app

Agora, quando você visita `<host>.mybluemix.net/`, você será capaz de visualizar o conteúdo de sua coleção do {{site.data.keyword.composeForMongoDB}}. Conforme você inclui palavras e as suas definições, elas são incluídas no banco de dados e exibidas. Se você parar e reiniciar o app verá que qualquer palavra e definição já incluída agora será listada.


## Etapas seguintes

Para entender mais sobre como o app de amostra [compose-mongodb-helloworld-nodejs](https://github.com/IBM-Cloud/compose-mongodb-helloworld-nodejs) funciona, é possível ler o arquivo leia-me do aplicativo ou os comentários do código no `server.js`, que fornecem algumas informações sobre as funções do app.

Para começar a explorar o serviço {{site.data.keyword.composeForMongoDB}}, veja os tópicos a seguir sobre o painel de serviço:

- [Visão geral do painel](./dashboard-overview.html)
- [Backups](./dashboard-backups.html)
- [Configurações](./dashboard-settings.html)

Para obter informações sobre as credenciais que você criou para o aplicativo para se conectar ao seu serviço, veja [Credenciais disponíveis](./connecting-bluemix-app.html#available-credentials).

[ibm_cloud_signup_url]: https://ibm.biz/compose-for-mongodb-signup
