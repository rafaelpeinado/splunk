# Splunk 9: Performing Basic Splunk Searches
## Introduction to Search in Splunk
### Searching Machine Data
Como podemos pesquisar dados?
O processo do Splunk é carregar nossos dados, pesquisamos os dados para encontrar as respostas para as perguntas que temos e, então, podemos criar relatórios, mas o que é tão importante em pesquisar em nossos dados gerados por máquina e pesquisa empresarial?
Podemos analisar histórico de pesquisa do navegador e outras informações que estavam acontecendo no firewall.

Na atividade iremos explorar:
* Quantas páginas são acessadas na web por hora?
* Quais navegadores são usados?
* Pesquisar nos logs de acesso os endereços de IP

Revisando a estrutura do Splunk: 
* **Index:** é o que estrutura os dados que chegam. 
* **Search Head:** conforme as informações chegam isso vai entrar no cabeçalho de pesquisa, então é indexado para que possa ser pesquisado rapidamente.
* **Forwarder:** temos nossa instância principal do Splunk, mas nossos dados serão enviados de lá, ou seja, se tivermos um log de servidor ou firewall, ou qualquer informação que desejamos obter dos dados gerados pela máquina, é aí que o encaminhador pode ser executado nesses ambientes e enviá-lo para a instância principal do Splunk.

### Demo: Splunk Search Interface
> index="pluralsight"| timechart count by "Domínio da Conta" limit=20

### Splunk Data Sets
#### Splunk Data Options
* Semi-estruturado: ele possui certa estrutura, mas não são dados tabulares como se fossem extraídos de um banco de dados.
   * Access log files
* Machine generated: são dados vindos de máquinas. A maioria dos dados gerados são gerados por máquina
   * Local logs como Windows, Linux ou MAC
* Often overlooked: dados que não usamos para análise

Os dados podem vir de todas as partes como:
* Servers
* Cloud
* Workstations
* Regex and logs

#### Recursos Adicionais
* **Buttercup**
   * Splunk Generated Data
* **Eventgen**
   * Splunkbase


## Understanding the Basics of Splunk Search
### Splunk Roles in Search
#### Splunk Roles
Todos os usuários do Splunk estão dentro de uma função que lhes dá diferentes funcionalidades.
São basicamente três:
* **User** pode criar e editar pesquisas pertencentes ao próprio usuário
* **Power** pode editar objetos e alertas compartilhados
* **Admin** pode executar a maioria das funções de pesquisa do Splunk

### Demo: Creating Splunk Search Roles
#### Splunk Search Interface
Demonstração da interface do Splunk.

### Data Storage in Splunk
#### Index
O Splunk guarda os dados em um **index**. Os dados ingeridos passam pelo processo de indexação que converte os dados em eventos pesquisáveis.

Há três etapas no processo de indexação dos dados pelos quais o Splunk passa:
* **Creates metadata:** cria metadados desse ambiente e, assim que seus dados brutos chegarem, eles criará metadados, ajudará a pesquisá-los com mais facilidade.
* **Compresses data:** comprime os dados, pois desejamos o obter o máximo de compatação possível, não apenas para economizar espaço de armazenamento, mas também para acelerar essas pesquisas.
* **Index for data:** vai indexar esses dados para que sejam fáceis de encontrar, para que sejam facilmente pesquisáveis no ambiente Splunk SPL. 

Para que os dados cheguem eles são indexados e colocados com um par chave-valor. Portanto, ele não apenas terá aquel par de valor-chave exclusivo, mas também receberá um registro de data e hora (timestamp), e esse é o processo para o Splunk fazer os índices.
No exemplo do vídeo, a data e hora do Timestamp é diferente da coluna Date, que é a data real. Isso acontece, pois esse é o carimbo de data/hora de quando ele encontrou e quando foi ingerido. Então, dependendo de quanto tempo leva entre nosso ambiente e de que estamos fazendo o upload desses arquivos arquivados, então haverá uma grande discrepância no carimbo de data/hora e com a data real.

Os dados serão realmente armazenados em um sistema de arquivos que o Splunk chama de bucket.

* **What is a Bucket?**
   * **Splunk file system:** é um sistema de arquivos do Splunk, ou seja, é aqui que os dados entram e são armazenados pela idade dos dados.
   * **Stored by age of data:** à medida que os dados são eliminados ou quando os dados começam a ficar um pouco mais antigos, eles serão classificados em sistema de bucket diferente, mas é a idade dos dados que determina qual bucket e onde ele ficará.
Teremos vários buckets em um índice, mas isso é transparente para o usuário, mas no back-end, queremos entendê-los, porque precisamos pensar em algumas coisas sempre que falamos sobre ciclo de vida.
Resumindo, quando os dados chegam, os buckets são como um sistema de arquivos que o Splunk indexou para que possamos fazer pesquisas mais rápidas.

### Demo: Bucket Management
* **Index Data Options:**
   * Hot
   * Warm
   * Cold

Settings -> DATA -> Indexes -> Clica no index para editar -> Temos as opções: Home Path, Cold Path e Thawed Path.

### Bucket Management in Splunk
Sobre a infraestrutura do Splunk temos: 
* **Creating indexes** 
   * Bucket allocation: onde esses diferentes buckets estão armazenados e quais são algumas das coisas que queremos pensar com eles?
* **Performance requirements**
   * Quantos dados temos? 
   * Quantos usuários estão acessando este sistema?
   * E quais são esses requisitos de desempenho para eles?
* **Storage requirements**
   * Decidir o quanto de armazenamento faz uma grande diferença dentro dessa infraestrutura, pois podemos estar ingerindo 1TB ou 1PB por dia.
* **Size Splunk [cluster](https://www.opservices.com.br/o-que-e-um-cluster/)**
   * O tamanho do cluster é importante, não apenas de um requisito de armazenamento, mas também de desempenho e usuários. Quão grande será esse cluster e qual é o seu crescimento para o futuro?


#### Bucket Lifecycle
* **Hot Bucket:** geralmente são dados de 24h e esses são realmente os dados mais quentes que estão chegando no ambiente, pois como é novo é mais provável que será o mais requisitado.
* **Warm Bucket:** tem cerca de 3 meses
* **Cold Bucket:** pode ser qualquer dado que tenha mais dque 3 meses
* **Frozen Bucket:** dados de 1 ano ou mais. São dados que podem estar fora do Splunk, mas precisamos guardá-los. Frozen buckets é como se fosse o próprio ambiente, onde talvez tenhamos compliance. Por exemplo, se trabalhamos com seguros ou finanças, pode estar sob a lei Sarbanes e Oxley. Então precisamos manter o compliance com a SEC (Securities and Exchange Commission) que devemos cumprir ou sobre assistência médica com o HIPAA (Health Insurance Portability and Accountability Act). Então qualquer informação que tenha que ser guardada por um certo período de tempo. 

Sendo assim, para buckets **Hot** ou **Warm** temos mais desempenho, mas, por outro lado, temos mais custos, pois precisamos de mais armazenamento e maior taxa de transferência.
Enquanto os buckets **Cold**, temos menos desempenho e economizamos nos custos.
Já os buckets **Frozen** temos ainda menos desempenho, porque os dados estão congelados. Por isso é algo que podemos realmente arquivar.


#### Splunk SmartStore
Foi lançado em 2018 para simplificar a Arquitetura do Splunk e introduzir armazenamento de objetos para o arquivamento de longo prazo.
Ele funciona um pouco diferente do que os exemplos anteriores.
Temos o **Hot Tier Cache** (Não temos mais Hot e Warm, pois está tudo em um) em que temos alto desempenho. E todo o resto é **Cold Cache** que tem menor desempenho. Geralmente esses dados saem do Hot Tier Cache para o Cold Cache em cerca de 7 a 10 dias. Porém isso tem que ser bem analisado de acordo com a demanda, pois quando dos dados vão para o Cold Cache o desempenho cai muito.


## Using Field Searches for Splunk
### Search Bar and Timeline
#### Navigating the Search Bar
Quando estamos navegando no side bar temos 6 opções diferentes
* Search Bar: é a barra de pesquisa geral, onde podemos fazer digitação livre para nossas informações, mas há uma sintaxe para isso.
* Time range: não apenas pesquisar que é o importante, mas devemos saber o que estamos procurando de certo intervalo de tempo.
* Job: assim temos informações sobre quanto tempo esse trabalho levou. É bom para saberse estamos construindo buscar eficientes
* Timeline: temos a linha do tempo rápida e são gráficos de visualização rápida onde realmente podemos detalhar
* Saved searches: temos a possiblidade de guardar pesquisas para pesquisas que precisamos repetir ou pesquisar constantemente. Ou se temos algo que precisamos compartilhar com outros usuários.
* Modes: que podem nos ajudar a aumentar a quantidade de dados que estamos vendo em nossas pesquisas e a rapidez ocm que esses trabalhos são processados.

#### Why Not Search All Results?
Conforme os dados começam a escalar, traremos mais módulos. Por exemplo, se trouxermos nossos servidores ou infraestrutura ou informações de aplicativos, obteremos cada vez mais resultados e eventos em nossa área que estamos procurando para extrair dados. Então o que queremos fazer é ser o mais restrito possível para termos um resultado de pesquisa mais rápido. Mas também é útil para nossos usuários, porque à medida que atraímos mais usuários, também começa a escalonar o aplicativo Splunk.

#### Splunk Search Modes
* **Fast:** esse é o mais rápido e por isso do nome, mas a descoberta de campo está desativada para pesquisas de eventos. Nenhum evento ou dados de campo para pesquisas de estatísticas. Não está nos dando muitas informações completas que podemos precisar para outras áreas.

* **Default:** tão inteligente é um híbrido entre o nosso modo mais detalhado e o modo rápido. A descoberta de campo está ativada para pesquisas de eventos. Nenhum evento ou dados de campo para pesquisas de estatísticas. Mas se usarmos um comando transformador seremos automaticamente alternados para o módo rápido dessa perspectiva para poder fazer essas consultas.

* **Verbose:** é o mais detalhado. É a informação que está dando mais e também é a mais lenta quando falamos sobre a eficácia e eficiência de consulta, mas todos os eventos e dados de campo. Portanto, se estivermos procurando um elemento-chave, o Fireffox ou algo assim, ele irá pesquisar em todo o conteúdo e descobrir onde o Firefox foi retirado.

### Search Field Operators
#### Field Operators 
Caracteres usados nas queries do Splunk para analisar o resultado das buscas. As funções comuns são wildcards, Booleans e operadores de comparação.


#### Splunk Field Operators
* **Field expression**
   * field=value
   * field!=value
   * field < value
   * field > value
   * field <= value
   * field >= value
* **Boolean operators**
   * field **NOT** value: field não contêm o valor
   * field=value1 **OR** field=value2: field contém pelo menos um valor ou ambos
   * field=value1 **AND** field=value2: field contém os dois valores
* **Wildcards:** é muito poderoso quando estamos procurando por certas informações e queremos pesquisar em resultados de pesquisa maiores.
   * Host="thenson-desktop": só terá resultado onde Host="thenson-desktop" 
   * Host="thenson*": poderá trazer resutados onde Host="thenson-desktop", Host="thenson-server", Host="thenson-mobile"

### Demo: Splunk Field Operators
**Pesquisa equal**
Podemos pesquisar com aspas duplas ou sem elas, por exemplo:
> index="dev_web"
> index=dev_web

**Pesquisa not equal**
> index!=dev_web
> index="dev_web" clientip!="134.209.30.2"

**Pesquisas Booleanas**
> index=dev_web clientip="134.209.30.2" OR clientip="208.113.197.84"

**Pesquisas Wildcards**
> index=dev_web clientip="1*"
> index=dev_web clientip="2*"
> index=dev_web clientip="1*" OR clientip="2*"

### Splunk Field Sidebar
#### Splunk Field Sidebar
Localizado na parte esquerda da tela de pesquisas do Splunk. Extrai os SELECTED FIELDS e INTERESTING FIELDS para resultos de pesquisas rápida. Também oferece reportes rápidos como os valores do topo (top values) e valores raros (rare values).

* **Fields assigned**
   * Selected fields: são os principais resultados da pesquisa
   * Interesting fields: onde podemos ver os diferentes campos disponíveis neles.
* **Quick field numbers**
* **Text fields α**
* **Numeric fields #**

### Demo: Splunk Field Sidebar
Demonstra como, por exemplo, pegar os top field values sem preciar conhecer SPL (Search Processing Language).
Podemos promover um field de INTERESTING FIELDS para SELECTED FIELDS clicando no field e selecionando Selected Yes. Essa mudança não muda o resultado da pesquisa.
Ao colocar um field como selected, ele aparece destacado na parte inferior de cada item do resultado da pesquisa.
Podemos observar que quando o field estiver acompanhado de # é um valor numérico e quando estiver α é um texto ou string.

**Top values by time**
> index="dev_web"| timechart count by clientip limit=10

**Top values**
> index="dev_web"| top limit=20 clientip
> index="dev_web"| top limit=10 clientip
> index="dev_web"| top limit=5 clientip

### Splunk Result Field
Os campços de resultados são clicáveis

#### Splunk Results Field
Parte principal do Splunk Search onde os eventos são exibidos a partir dos resultados da pesquisa.

* **Events displayed**
   * **Formatting:** podemos formatá-los, colocá-los em diferentes áreas, vê-lops como um arquivo de dados bruto ou até mesmo separá-los, puxar campos selecionados, todos exibidos.
   * **Per event:** podemos olhar cada evento individual e os valores e até mesmo detalhar ainda mais a pesquisa.
* **Displayed fields clickable**
   * **Add**
   * **Exclude**
   * **New**

#### More **Results Display** Options
* **Raw events:** examinamos os eventos brutos e algumas das exibições que temos
* **Patterns:** podemos procurar padrões
* **Statistics:** é capaz de construir estatísticas
* **Visualizations:** podemos mudar para vários tipos de visualizações

### Demo: Splunk Results Field
Podemos visualizar com Raw, List e Table.

### Best Practicies in Splunk Search
**Obs: Infraestrutura will define Splunk Search performance**
#### Splunk Search Tips
* Use os critérios masi restritos disponíveis, ou seja, se estamos buscando informações sobre segurança, vamos buscar as informações que só estão em Segurança. Além disso, é preciso tomar cuidado com wildcards.
* Timeline tem um impacto importante nas pesquisas.
* Indexar conjuntos de dados e planejar a escala, ou seja, não devemos usar o mesmo índice para tudo e planejar a escala é o futuro, porque à medida que o futuro avança, podemos distribuir diferentes index e podemos realmente criar algumas consultas mais eficientes.


## Building SPL Queries in Splunk
### What is SPL?
#### SPL
**S**earch **P**rocessing **L**anguage **(SPL)** que engloba todos os comandos de pesquisa e suas funções, argumentos e cláusulas. Sua sintaxe foi originalmente baseada no pipeline Unix e SQL. O escopo do SPL inclui pesquisa, filtragem, modificação, manipulação, inserção e exclusão de dados.

##### Example SQL
**SELECT** *
**FROM** dev_web
**Where** IP_Address=192.101.101

##### Example SPL
**Index="dev_web"** IP_Address=192.101.101

### Demo: Search Processing Language Editor
Administrator -> Preferences -> SPL Editor
Essas mudanças não são globais, são apenas para o usuário que está realizando as mudanças.

### Building SPL Queries
#### Chaining Commands (Pipe Commands)
Comandos de Processamento de Pesquisa (Search Processing Commands) usam o pipe | para encadear comandos de pesquisas 

#### Example SPL
Index="dev_web" IP_Address=192.101.101 | command 1 argument

* **SPL Keywords**
   * AS: é um modificador
   * BY: é muito bom para contar
   * OVER: usado para quando precisar passar por um campo e filtrá-lo
   * WHERE: muito semelhante ao SQL e é bastante usado em campos booleanos

Exemplo: source="WinEventLog:*" **| where** EventCode > 1000
Where Keyword: comando do Splunk usado para retornar resultados em que o comando "where" é true.

### Demo: SPL Chaining Commands
index="pluralsight" clientip!="134.209.30.2"

A configuração **No Event Sampling**, basicamente é usado para quando temos grandes conjuntos de dados dimensionados.

index="pluralsight" clientip!="134.209.30.2" | where bytes > 1565

index="pluralsight" clientip!="134.209.30.2" | where bytes = 1565 OR bytes=1762

### SPL Filtering & Modifying Search Results
Host=* | fields [ fieldname1 ], [ fiedname2 ]
**Fields** adiciona ou remove o campo listado.

Host=* | search [ keyword ]
**Search** permite pesquisar texto bruto ao usar comandos de alteração.

Host=* | dedup [ fieldname ]
**Dedup** remove campos duplicados dos resultados da pesquisa

Host=* | rename [ fieldname ]
**Rename** permite que os nomes dos campos sejam alterados nos resultados da pesquisa.


### Demo: SPL Search & Rename
* index="pluralsight" clientip!="134.209.30.2" | fields - clientip
   * exclui esse campo

* index="pluralsight" clientip!="134.209.30.2" | fields + clientip, bytes
   * exibe apenas os dois campos

* index="pluralsight" clientip!="134.209.30.2" | search chrome

* index="pluralsight" clientip!="134.209.30.2" | search firefox

* index="pluralsight" clientip!="134.209.30.2" | search firefox | rename useragent as Browser


### SPL Ordering Search Results
Host=* | head [ number ]
**Head** retorna os 10 primeiros eventos, a menos que seja especificado por outro número.

Host=* | tail [ num ]
**Tail** retorna os 10 últimos eventos, a menos que seja especificado por outro número.

Host=* | sort [ fieldname ]
**Sort** ordena o campo por ordem crescente.

Host=* | reverse
**Reverse** coloca todos os resultados da pesquisa em ordem inversa.

Host=* | tables [ fieldname ]
**Tables** é um comando SPL que cria uma tabela com campos específicos.

### Demo: SPL Sort, Tail, & Head
* index="pluralsight" method=GET
* index="pluralsight" method=GET | tail
* index="pluralsight" method=GET | tail 5
* index="pluralsight" method=GET | head
* index="pluralsight" method=GET | sort bytes
* index="pluralsight" method=GET | sort bytes desc
