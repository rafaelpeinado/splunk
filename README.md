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




