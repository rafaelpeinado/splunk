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






