# Splunk 9: Installation and Configuration (by Thomas Henson)
## What Is Splunk?
### Overview
#### System Administrator
* Windows
* Linux
* Cloud

#### Visualization Tools
* Build tables
* Excel skills

#### Roles
* Analyst
* Architect
* Security

### What is Machine Data?
São gerados principalmente por máquinas. Normalmente em forma de dados estruturados ou semi-estruturados. Por exemplo, um servidor web e os logs que seriam dados de máquina.

#### Where Is The Data?
Em todo o mundo, os dados estão crescendo exponencialmente. Em 2022 temos 97 ZB de dados gerados. Em 2025, temos a expectativa de ter gerado 181 ZB. E é aí que o Splunk pode entrar para ajudar a entender esses dados.

#### Machine Data in the World
Podemos gerar dados em: 
* **Web services request:** em geral são logs como, por exemplo, logs do Apache, dos serviços, da web, do web service, qualquer tipo de dispositivo, dispositivo de rede etc, são todos dados que representam dados de máquina.
* **Security logs:** ver quem está tentando fazer login, quem está tentando acessar coisas que não precisam, o que está acontecendo com a rede, com a base de usuários.
* **Operation Intelligence:** como podemos ser pró-ativos antes que os aplicativos possam ficar inativos, não tendo que recriar erros em nossa programação para entender o que está acontecendo, mas ser capaz de fazer isso em tempo real.
* **Mobile device:** muitos dados gerados por máquina são criados a partir de dispositivos móveis
* **Internet of Things (IoT):** podemos colocar dispositivos e monitorar as coisas ao redor. 

#### Common Qualities of Machine Data
* **Semi structured:** quando falamos de dados semi-estruturados, onde veremos diferentes arquivos de log e diferentes partes de log. Não é um modelo de dado relacional.
* **Machine generated:** pode vir dos aplicativos, de um dispositivo de rede vindo de servidores, mas não é algo que o humano está digitando.
* **Often overlooked:** visão geral do que realmente está acontecendo

### Past, Present, and Future of Splunk
O Splunk é usado pela maioria da empresas da Fortune 500. 
* SIEM (Security Information Event Management): O gerenciamento de eventos de informações de segurança são análises de informações de segurança coletados de aplicações e hardware no ecossistema de TI.

#### Brief Splunk History
* Splunk foi fundado em 2003 com a ideia de ter um repositório centralizado para arquivos de log e a capacidade de monitorar aplicativos e infraestrutura.
* Oferta pública inicial em 2012 (IPO): continuaram a expandir os casos de uso fora da área de TI, mas especificamente em TI também, além da área de segurança, foi também para o monitoramente de desempenho do aplicativo analisado.
* Splunk vendeu mais de 2,5 bilhões de dólares: isso demonstra o quanto o Splunk está sendo aderido em TI.

Eles atuam em infraestrutura de Data Center, mas Cloud (AWS/Azure/GCP) que tiveram um grande crescimento, pois temos a capacidade de monitorar os aplicativos em cloud, infraestrutura em cloud, estações de trabalho também. Além de expressões regulares e arquivos de log.

#### Splunkbase
* **Splunk Enterprise**
* **Splunk Observability Cloud**
   * SaaS
* **Splunk Enterprise Security**
* **Splunk Machine Learning Toolkit**
   * Com o toolkit de Machine Learning do Splunk, podemos detectar outliers em TI, dados de operação que podemos prever o planejamento da capacidade de crescimento do data center.

### Splunk Architecture and Licenses
O Splunk é construído em três blocos de arquitetura:
* **Index:** não significa ser o mais importante, mas tem um trabalho muito importante onde pega esses dados brutos.
* **Search head:** isso é que vai lidar com o gerenciamento de pesquisa, vai pedir para enviar os resultados de volta para os usuários.
* **Forwarder:** responsável por enviar os dados para o indexador. Pode ser implantado remotamente em dispositivos para enviar os dados de volta à nossa instância corporativa principal do Splunk.

#### Como o Splunk é usado?
Temos um search head, por exemplo os logs do Apache e temos um painel configurado que nos mostrará todas as diferentes áreas do país em que as pessoas estão acessando nosso site. Abaixo do search head, ele será indexado onde contém todos os eventos e vai mantê-los e então nós temos os dispositivos, por exemplo três ou quatro servidores web diferentes por aí que estão encaminhando os logs e esses logs serão convertidos em eventos, que então pesquisamos.
Conforme esse cenário vai escalando, podemos ter milhões a bilhões de eventos diferentes e, portanto, também terá que dimensionar essa arquitetura.

#### Como o Splunk é licenciado?
* **Licensing in Splunk by ingest:** o que significa que quantos dados estamos indexando, trazendo e consumindo.
* **Splunk Free:** 
   * 500 MB por dia
* **Splunk Enterprise:** 
   * Variedades
   * 1 GB por dia
   * Licença Term vs. perpetual
      * Term quer dizer que teremos uma renovação anual
      * Perpetual onde podemos simplesmente assinar e vão reativar enquando continuamos a crescer

### Splunk Documentation and Community Tour
* [Splunk Documentation](https://docs.splunk.com/Documentation)
* [Splunk User Groups](https://usergroups.splunk.com/) - pode ter informações sobre palestras
* [Splunk Community](https://www.splunk.com/en_us/community.html)
* [Splunkbase](https://splunkbase.splunk.com/)
   * Dell EMC Isilon Add-on for Splunk Enterprise: fornece painéis e relatórios e também permite entender e obter relatórios importantes para monitorar a infraestrutura.


## Installing Splunk Enterprise
### Overview
* Configurando um ambiente de desenvolvimento Splunk
* Instalar Splunk no Linux, Windows e plataformas em nuvem
* Percorrer a instalação do Splunk
* Explicar como Splunk Cloud SaaS é ofertado

### Building Splunk Environments
Trabalharemos com Globmantics, que é uma empresa global que está trabalhando para desenvolver produtos em IA e análise, espaço e robótica.

* **Landon Reed**
   * Globmantics IT Operations fictício
   * Ele tem experiência em Linux/AWS
   * Sua atividade principal é fazer testes no Splunk
* **Desenvolver o próprio site da comunidade**
* **Lançamento da Proof of concept (POC) Splunk Enterprise**

### Setting Up Splunk
#### Registration Required!
* Criar uma conta Splunk
* Download do Software
* Cloud trial
* Acesso a comunidade
* Splunkbase
* Outros recursos

Teremos todos os 3 pilares em uma única máquina, ou seja, teremos indexer, Search Head e Forwarder.

#### Splunk License
* Limitação na ingestão de dados, sendo 500 MB/dia.
* Limitação de tempo: em 60 dias serão convertidos para free license

#### Ports
Default port para login 8000
Management port 8090: gerenciamento de comunicações
URL -> http://localhost:8000: login com usuário e senha

### Splunk in the Cloud
Diferenças entre executar o Splunk no cloud e a oferta do Splunk no cloud.

#### Why Splunk Cloud
* Organizações estão com estratégia public cloud first 
* Organizações multi-cloud com grandes investimentos em public cloud 
* Oportunidade de descarregar algumas tarefas administrativas ou estender a equipe do Splunk.

#### SaaS
* **AWS Regions**
   * entender as regiões que tem disponibilidade
* **Splunk as a Service**
   * oferta de serviço gerenciado
   * diferente de apenas ser executado em cloud, então se entrarmos no AWS e ativarmos o nosso ambiente Splunk, podemos fazer isso com bastante facilidade
* **On-prem Management**
   * Usar forwarders

#### You Know Your Environment Workflows, Strategy & KPIs
Cabe à organização decidir como você deseja usar o Splunk. 

#### Splunk Cloud License
* Data ingest limitation de 5GB/dia
* Limitado por período de 15 dias no Splunk Enterprise SaaS offering trial
* Limitado há algumas funcionalidades limitadas em comparação com o Splunk Enterprise


## Navigating and Configuring the Splunk Enterprise
### Getting Data into Splunk
#### Data is the heart of Splunk
* **Data Sources**
   * Loacl Machine
   * Forwarders: podem ser aplicados em qualquer coisa, como aplicativos, infraestrutura, dados de firewall
   * Upload Sources
   * Example Data

### Demo: Getting Data into Splunk
Clicando em settings, temos a seção de dados para data input, forwarding and receiving, indíces, reports, source types, ingest actions.
Clicando em Add Data temos 3 opções:
   * Upload: nesse caso vamos usar o upload de dados que já temos
   * Monitor
   * Forward

* Set Source Type: podemos configurar como queremos estruturar os dados
* Input Settings: Vamos mudar o índice

Start searching gerou uma pesquisa automática com o comando
> source="access_test.log" host="test" index="pluralsight" sourcetype="access_combined_wcookie"

Depois INTERESTING FIELDS > clientip > Top values by time que gerou o seguinte comando
> source="access_test.log" host="DESKTOP-B0L94SK" index="pluralsight" sourcetype="access_combined_wcookie"| timechart count by clientip limit=10

Ao clicar na seta da lista de eventos, podemos selecionar um dado que queremos filtar e selecionar **Add to search**.

### Navigating Splunk Search
#### How to Search in Splunk
* **Basic Search:** olhamos pelo quadro que temos na página inicial
* **Splunk Processing Language:** é semelhante a SQL e temos sintaxes específicas na barra de pesquisa
* **Splunkbase:** aplicativos configurados pré-construídos para que possa ser algo tão simples quanto um painel já construído ou algo que será integrado
* **APIs:** podemos usar APIs usando SQL, construindo aplicativos personalizados

##### Logs
* **Basics Splunk Search**
   * By time date
   * By fields
   * Sorting fields

#### How to Search in Splunk
* Reports & Dashboards
* Splunkbase
* Splunk APIs


### Demo: Adding More Data into Splunk
Selecionar a opção Monitor > Local Events Logs > add all > index pluralsight
> source="WinEventLog:*" host="test"

### Demo: Installing Splunk Apps
* Dell PowerScale Add-on for Splunk
* Splunk Machine Learning Toolkit

### Splunk Roles in Search
**Splunk Roles**
Todos os usuários do Splunk se encaixa em uma função que dá a esse usuário diferentes funcionalidades no Splunk.

#### Search Roles
* **User** pode criar e editar as próprias pesquisas daquele usuário específico
* **Power** pode editar objetos e alertas compartilhados
* **Admin** pode performar em todas as funções de pesquisa do Splunk

### Demo: Splunk User Roles
Em settings > USERS AND AUTHENTICATION > Roles
Em settings > USERS AND AUTHENTICATION > Users



 