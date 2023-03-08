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



