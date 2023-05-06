# Splunk 9: Employing the Splunk Common Information Model (CIM)
## Getting Familiar with the Splunk Common Information Model
### Getting Familiar with the Splunk Common Information Model
#### What is the Common Information Model?
* Conhecido como CIM
* É um add-on do Splunk
    * Modelos de dados
    * Documentação
    * Ferramentas
* Tratamento consistente e normalizado de dados

#### Por que usar o CIM do Splunk?
Suponhamos que queremos capturar e analisar os dados de diferentes sistemas de origem, podemos ter logs de Apache, Cisco, um banco de dados SQL Server e alguns lgos do AWS e GCP (Goolge Cloud Platform).
O Splunk CIM é capaz de pegar esses dados de origem, padronizá-los e criar um esquema dinâmico ou pesquisa em tempo real.

#### Schema-on-the-fly 
Schema-on-the-fly significa que o CIM permite que você normalize seus dados enquanto deixa os dados de origem intactos.


### Examining a Few of the CIM Data Models
[Common Information Model Add-on Manual](https://docs.splunk.com/Documentation/CIM/5.1.1/User/Overview)

#### Modelo de Dados de Alerta
* Data Model Name: Alerts
* Dataset Names: Alerts
* Selected Fields
    * Dataset: Alerts
    * Fields
        * app: the application involved in the event
        * body: the body of the message
        * severity: the severity of a message
        * type: the type of message (alarm, alert, event, task)

#### Modelo de Dados de Malware
* Data Model Name: Malware
* Dataset Names: Malware_Attacks e Malware_Operations
* Selected Fields
    * Dataset: Malware_Attacks
    * Fields
        * category: category of a malware event
        * file_name: name of suspected file
    * Dataset: Malware_Operations
    * Fields
        * dest_nt_domain: domain name of the destination
        * vendor_product: the name of the malware product

#### Modelo de Dados de Network Traffic
* Data Model Name: Network Traffic
* Dataset Names: All_Traffic
* Selected Fields
    * Dataset: All_Traffic
    * Fields
        * app: the application protocol
        * bytes: total count of bytes
        * dest_ip: the IP address of the destination
        * dest_port: the port number at the destination

### Splunk Knowledge Objects
#### The Five Pillars of Splunk Knowledge
* Data Interpretation
* Data Classification
* Data Enrichment
* Data Normalization
* Data Models

##### Data Interpretation: Field and Field Extractions
O Splunk descobre valores automaticamente procurando por pares key = value nos dados, mesmo que não seja explicitamente na forma de key = value. Os campos que o Splunk não descobre podem ser extraídos usando o extrator de campo ou regex ou comandos de pesquisa específicos.

###### Fields and Fields Extractions
* ip_address=68.147.170.101 - claramente é um campo key = value e tem um = no meio. Esse é o cenário ideal e o Splunk não faz muito trabalho para identificar que isso é um campo.
* ip_address 10.50.161.3 - mesmo sem o sinal de =, o Splunk pode facilmente detectar isso como um par key-value. 
* 9:02 404 stevej 10.10.1.5 - aparência de mutios dados de máquina, valores sem nenhuma chave. O Splunk os identifica facilmente, especialmente por serem delimitados por um espaço. 

* 709FAqB33C6U8xUbuln69ENr2Qoggnrf4kfuNLflg5RcWv3yShylLvTCu3RMSaw87hcwqnAnluTVIbIMmmZgzRumpjgl4L2E02hF - mesmo nesse caso sendo um sequência aleatória de caracteres, o Splunk não seria capaz de identificar campos. E, as vezes, logs de máquina podem ser enigmático desta forma. Desta forma, seria necessário ensinar o Splunk o que procurar usando o extrator de campo ou regex ou comandos de pesquisa específicos.
    * server=Ubuln69
    * User=Qoggn
    * Flag=flg5Rc

###### Data Classification: Event Types and Transactions
Um evento no Splunk é uma simples linah ou linha de dados. E o splunk atribui esses campos no mínimo: **Timestamp, Host, Source, Source type**. Esses são os campos padrão de um Evento do Splunk.
Se olharmos para um log do Apache abaixo, podemos ver os valores para esses campos que o Splunk atribuiria

172.26.34 - - [ 01/Jul/2017:12:05:27-0700 ] "GET /trade/app?action=logout HTTP/1.1" 200 2953
* Timestamp: 01/Jul/2017:12:05:27-0700
* Host: 172.26.34
* Source: GET
* Source type: Apache error log

**EVENT TYPE** 
É uma categoria de ventos que podem ser pesquisados com a mesma string de pesquisa. O tipo de evento é criado pelo usuário, ou seja, salvamos os resultados de pesquisa como tipos de evento.
Temos vários eventos e podemos criar um tipo de evento com esses eventos.

**Event Types**
Supomos que façamos uma pesquisa que retorna os eventos a seguir. Desejamos categorizar como eventos válidos ou inválidos.
Nesse caso, aqueles com nomes de usuário são considerados válidos, caso contrário são inválidos.
Quando atribuímos esse tipos de eventos, podemos criar relatórios e tabelas estatísticas sobre quantos logins válidos e inválidos ocorreram.

| Event                      | Event Type        |
|----------------------------|-------------------|
| 9:40 sjobs 10.10.1.15      | eventtype=valid   |
| 9:42 unknown 67.40.45.23   | eventtype=invalid |
| 9:44 bgates 10.10.3.64     | eventtype=valid   |
| 9:51 unknown 209.63.47.108 | eventtype=invalid |
| 952 unknown 106.14.237.12  | eventtype=invalid |

* | stats count by eventtype

| valid   | 2 |
| invalid | 3 |


**TRANSACTIONS** 
Representa uma série de eventos que acontecem ao longo dotempo para realizar um processo de negócios como uma reserva de hotel.
Um usuário reserva um quarto, insere as informações do cartão de crédito para despesas ocasionais, pode comprar filmes ou outras coisas no quarto e faz um check-out.
Isso é um agrupamento de eventos brutos relacionados em um único evento chamado transação. Esses eventos são vinculados por campos que eles têm em comum. 

###### Data Enrichment: Lookups and Workflow Actions
* CSV, External, Geo, KV Store.
Pesquisas e ações de fluxo de trabalho são categorias de objetos de conhecimento que estendem a utilidade de seus dados de várias maneiras. 
As pesquisas de campo permitem que possamos adicionar campos aos dados de fontes de dados externos, como tabelas estáticas, arquivos CSV, etc. ou comandos baseados em Python.
As ações de fluxo de trabalho permitem interações entre campos nos dados e outros aplicativos ou recursos da web, como uma pesquisa whois na web para um campo contendo um endereço IP e talvez queiramos descobrir o proprietário ou a região geográfica ou algo assim.

**LOOKUPS**
regions.csv

| regioncode | regionname  |
|------------|-------------|
| a1bf2g48   | US East     |
| d3de5c32   | US West     |
| d4cfg3gg   | Canada East |
| e4c23439   | Canada West |

* | lookup regions.csv regioncode OUTPUT regionname

Queremos que o Splunk corresponda a cada um dos códigos de região que encontrar nos dados e os substitua pelo nome da região.
Como os códigos da região não são claras, queremos trocar pelo nome da região que são dados amigáveis normalizados para os usuários de nossos dados.
Um workflow é a mesma coisa que um lookup, exceto externa. É simplesmente uma pesquisa externa. 

###### Data Normalization: Tags and Aliases
**TAGS**
Atribuem nomes a combinações específicas de campos e valores, eventos ou tipos de eventos.
As tags são componentes obrigatórios do Splunk CIM.

websrv_sf=208.172.3.16
websrv_slc=16.27.130.161
websrv_ny=48.152.17.101

Temos servidores web com diferentes hosts e IPs, mas queremos criar uma tag que contenha todos os servidores web e criamos a tag webservers. Após isso podemos apenas colocar na pesquisa Tag=werbservers e a pesquisa retornará todos os servidores web.

**ALIASES**
São nomes alternativos que atribuímos a um ou mais campos.
**Um campo pode ter mais de um alias, mas um alias só pode ser aplicado a um campo.** então temos uma espécie de **um-para-muitos**. Uma forma de fazer isso é usando:
* | rename < oldfieldname > as < newfieldname >
* | eval < newfieldname >=coalesce(< oldfieldname1 >, < oldfieldname2 >), então esses dois nomes de campos diferentes, talvez venha de sistemas diferentes, mas para nossa organização, eles significam a mesma coisa, então atribuímos a eles um novo nome de campo e unimos ou combinamos esses dois campos antigos nesse novo nome de campo.

###### Data Models
É uma hieraquia de consjuntos de dados e outros objetos de conhecimento.

|                                               Data Model                                               |
|--------------------------------------------------------------------------------------------------------|
|                          Events                           |          Searches           | Transactions |
|          Dataset1           |          Dataset2           |          Dataset3           |   Dataset4   |
|      KO      |      KO      |      KO      |      KO      |      KO      |      KO      |      KO      |

KO = Knowledge Object


### Knowledge Objects Included in the CIM
* **Two Types of Knowledge Objects**
    * Tags
    * Aliases

* Os modelos de dados Splunk CIM contêm tags:
    * Precisamos aplicar essas tags aos nossos dados para torná-los compatíveis
    * Depois de aplicar as tags, precisamos verificar que elas funcionam quando usamos a ferramenta Splunk Pivot

#### Process for Tags
1. **Identify:** identificar o modelo de dados e o conjuntos de dados relevantes para o evento
2. **Observe:** observar quais tags são necessárias para esse conjuntos de dados e se há outras restrições listadas no modelo de dados
3. **Apply:** criar tipos de eventos e marque-os com as tags apropriadas
4. **Verify:** verificar se tudo está funcionando

* Os modelos de dados no CIM incluem:
    * campos herdados
    * campos extraídos
    * campos calculados
* Alguns modelos de dados também incluem campos com valores esperados

#### Process for Fields
1. Compare os campos no modelo de dados com os campos nos dados.
    1. A combinação está perfeita? -> Sim -> End
    2. Não? então mapear os nomes de campo para os nome de campo CIM usando **Aliases, Extractions e Lookups** -> End.

No final, depois de normalizar os dados, valide-os em realção ao modelo de dados.

#### Two Ways to Validate Your Data
* Using the Pivot editor
* Searching the data model itself


### Demo: Splunk CIM
Primeiro instalar Splunk Common Information Model (CIM) add-on
Settings -> Data models 
Todos que estiverem com Splunk_SA_CIM são modelos de dados do Splunk CIM.
Entrando no Data Model Alerts podemos verificar que podemos usar a tag alert pela configuração **(`cim_Alerts_indexes`) tag=alert**

Também podemos ver os Data Models pelo diretório < pasta-de-instalação >/Splunk/etc/apps/Splunk_SA_CIM/default/datamodels.conf


## Configuring the Common Information Model Add-on
### Configuring and Employing the Common Information Model Add-on
* Podemos melhorar muito o desempenho de nosso CIM definindo restrições de índice.
    * **Whitelist** de índices específicos para restringir a carga de pesquisa do CIM
    * Através da interface web do Splunk:
        * Apps > manage apps > setup
        * Selecione o data model
        * selecione o índice que deseja colocar na whitelist na seção de index whitelist

O Splunk também recomenda:
* Acelerar os data models do CIM
    * Todos os data models incluídos no CIM têm a aceleração **desligada** por padrão

A aceleração de data models cria resumos de dados com base nas configurações de intervalo de resumo definidas. O modelo de dados pesquisa nesse resumo em vez de em todo o conjunto de dados.

#### Why Normalize Data
Dois casos de uso
* Desenvolvendo um aplicativo de Splunk
* Construindo um add-on para o Splunk
    * Podemos desenvolvê-lo para ingerir e analisar um tipo muito específico de dados. Se for um fornecedor que gera arquivos de log em um formato específico, convém desenvolver o aplicativo ou o complemento para normalizar esses dados para o restante do ambiente Splunk do cliente.
* Para nosso próprio dashboard
    * Também podemos querer normalizar nossos dados para garantir que todos os nossos dashboards estejam usando as mesmas estruturas de dados. É muito difícil construir um painel preciso, por exemplo, se os nomes dos campos forem todos diferentes ou se os dados não estiverem no formato correto.

#### Six Steps in the Data Normalization Pipeline
* Receber os dados (Get in data)
    * Forwarders
    * Monitors
    * File uploads
* Examinar os dados (Examine your data)
    * Determinar quais data models no CIM são relevantes para nossos dados
    * Usar a tabela de referência do CIM para achar campos relevantes
* Criar e tagear event-types (Create and tag event-types)
    * Olhamos para as tags que estão disponíveis no data model
    * Creamos event-types usando os dados e adicionamos tag à eles
* Verificar as tags (verify tags)
    * Verificamos as tags usando a ferramenta Pivot
* Normalizar os campos (Normalize fields)
    * Criamos aliases para os campos
    * Usamos extrações de campos (se os dados não tivessem nenhum nome de coluna)
    * Creamos lookups 
* Validar em relação ao mmodelo de dados


### Introducing Our Use Case
* Globomantics
    * Uma organização de e-commerce de médio porte
    * Precisa de insights sobre o tempo de atividade de seu e-commerce website
    * Eles têm três sistemas de origem que são normeados como System1, System2 e System3.
* System1
|        col1       |        col2       |        col3       |        col4       |        col5       |
|-------------------|-------------------|-------------------|-------------------|-------------------|
|  Email addresses  |     Usernames     |        URLs       |    IP adresses    | HTTP status codes |

* System2
|        user       |         --        |     dest_host     |      dest_ip      |         ?         |
|-------------------|-------------------|-------------------|-------------------|-------------------|
|     Usernames     |  Email addresses  |        URLs       |    IP adresses    | HTTP status codes |

* System3 (um log básico do Apache)
66.161.152.210 - kunde2702 [ 09/09/2019:20:24:16 -0600 ] "PUT /evolve" 100 50216 "https://www.internationalinterfaces.info/empower/integrate" "Mozilla/5.0 (Windows; U; Windows 98) AppleWebKit/533.39.8 (KHTML, like Gecko) Version/5.2 Safari/533.39.8"

* Remote IP: 66.161.152.210
* User Identifier: -
* User Id: kunde2702
* Timestamp: [ 09/09/2019:20:24:16 -0600 ]
* Resource requested: "PUT /evolve"
* HTTP status code: 100
* Bytes returned: 50216
* Request line: "https://www.internationalinterfaces.info/empower/integrate" "Mozilla/5.0 (Windows; U; Windows 98) AppleWebKit/533.39.8 (KHTML, like Gecko) Version/5.2 Safari/533.39.8"

* Globomantics deseja construir um dashboard que mostre:
    * Número de web requests com sucesso (HTTP 200)
    * Número de web requests com erros (HTTP not 200)
    * Web requests, destination IP, status e username

#### Which Fields Will We Need?
* Username
* IP Address
* HTTP Status Code

#### Transforming Columns
| New Column name          | Column name from System1 | Column name from System2 | Column name from System3 |
|--------------------------|--------------------------|--------------------------|--------------------------|
| Username                 |  col2                    | user                     |                          |
| Destination IP Address   |  col1                    | --                       |                          |
| HTTP Status Code         |  col5                    | httpst                   |                          |

#### Using the CIM Reference Tables
Olhando a documentação, faremos referência ao data model CIM **Web**.

### Demo: Making Data CIM Compliant in Splunk
* Carrega o arquivo system1 -> Host field value: system1 -> Index: main
* Carrega o arquivo system2 -> Host field value: system2 -> Index: main
* Carrega o arquivo system3 -> Host field value: system3 -> Index: main

* Settings -> Data Models -> Search/filter for **Web** -> Clica no Web -> (`cim_Web_indexes`) tag=web
* Apps -> Manage Apps -> Filter for **CIM** -> Set up -> Canto inferior esquerdo procurar por Web -> Indexes whitelist: main
    * Nós também podemos marcar Accelerate e o Splunk recomenda isso na maioria dos casos.
* Criar event-types e usar tags apropriadas, e nesse caso vamos usar a tag=web
    * Settings -> Event types -> Criar em New Event Type:
        * Destination App: search
        * Name: CIMWeb
        * Search string (apenas dados que carregamos recentemente): host=system1 OR host=system2 OR host=system3
        * Tag(s): web
    * Clicar em Save
    * Alterar as permissões de Private para Global **(isso não deve ser feito em produção)**
* Settings -> Data Models -> Search/filter for **Web** -> Clica no Web -> Pivot -> Clicar no conjunto de dados **Web** -> Se tudo der certo, ele trará todos os eventos para a tabela dinâmica.
* Settings -> Data Models -> Search/filter for **Web** -> Clica no Web -> Split Rows + -> dest
* Settings -> Fields -> Fields aliases -> New Field Alias:
    * Destination App: search
    * Name: sys1_alias
    * Apply to: host
    * named: system1
    * Field aliases e cliar em **Add another field** para criar mais campos
        * col4=dest
        * col5=status
        * col2=user
    * Clicar em Save

No system2 não temos o HTTP Status, então temos que apenas ignorar esse dado
* Settings -> Fields -> Fields aliases -> New Field Alias:
    * Destination App: search
    * Name: sys2_alias
    * Apply to: host
    * named: system2
    * Field aliases
        * dest_ip=dest
    * Clicar em Save

Agora **system3** iremos usar a ferramenta extratora de campo integrada do Splunk
* Página inicial -> Search & Reporting -> pesquisar por **host="system3"**
    * Vemos que o Splunk tem dificuldades em analisar muitos desses dados
    * Clica em Extract New Fields -> clicamos em um evento de amostra e usaremos o método de **regex**
    * Destaca o campo que quer -> Field Name: dest -> Add Extraction e após isso o Splunk interpretará essa extração e mostrará todos os campos que foram reconhecidos.
    * Destaca o campo de Http Status -> Field Name: status -> Add Extraction
        * Caso alguns dados não tenham sido reconhecidos e seja importante treinar mais o Splunk, basta clicar em outra amostra que não foi reconhecida e repetir o processo.
    * Destaca o campo de user -> Field Name: user -> Add Extraction
    * Clicar em Next
    * Permissions, nesse caso, foi escolhido All apps **não fazer isso em produção**
    * Clicar em Finish

Para validar se nosso exercício de conformidade CIM funcionou.
* Settings -> Data Models -> Search/filter for **Web** -> Clica no Web -> Pivot -> Clicar no conjunto de dados **Web** -> Split Rows + -> dest depois status e depois user

Vamos criar dois painéis para sucessos e erros
* Clicar em Single Value (42) -> Filter: + Add Filter: status -> Match: is: 200
    * Value -> Caption: Successful web requests
    * Save as... -> Dashboard Panel -> Panel Title: Successful web requests -> Save
* Clicar em Single Value (42) -> Filter: + Add Filter: status -> Match: is: 404
    * Value -> Caption: Not found
    * Color -> Use Colors: Yes
    * Save as... -> Existing -> Web -> Panel Title: Page not found
* Clicar em Statistics table -> dividir as linhas por user, dest e status
    * clicar em status e colorir até 201 é green e os demais é red
    * Save as... -> Existing -> Web -> Panel Title: Successful vs unsuccessful
* View Dashboard -> Edit -> Mover a janela do lado do outro



