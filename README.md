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




