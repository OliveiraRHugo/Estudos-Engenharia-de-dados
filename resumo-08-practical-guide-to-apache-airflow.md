# Estudos Apache Airflow

Bibliografias: Practical Guide to Apache Airflow 3, Datacamp e documentação do apache airflow

## Introdução ao Apache Airflow

* **O que é Apache Airflow:** Uma ferramenta _open source_ para **escrever, agendar, gerenciar e monitorar _workflows_ como código**.
* É ideal para definir ações que dependem umas das outras e devem ser realizadas em uma ordem específica.
* **_Data Orchestration_:** Envolve a **coordenação, automação e monitoramento de _workflows_ de dados**, garantindo a execução suave de tarefas e a entrega oportuna de _insights_ valiosos.

## Principais Conceitos
### DAG (Directed Acyclic Graph, Gráfico direcionado Acíclico)
* É um gráfico que representa uma coleção de tarefas executadas em uma ordem específica, e de forma sequencial ou não, a depender da dependência entre as tarefas
* O DAG representa o fluxo de trabalho a ser automatizado, garantindo a sua execução no momento correto
* DAG's são definidos em arquivos python, e salvos na pasta DAG_FOLDER do diretório em que o Apache Airflow foi instalado.
* O Airflow irá executar o código contido no arquivo do DAG, criando dinamicamente os objetos do tipo DAG.
* Você pode ter quandos DAG's quiser e com quantas tarefas quiser, mas a boa prática é que cada DAG deva representar um fluxo de trabalho e que é melhor haver várias tarefas pequenas do que uma grande tarefa que faz tudo.
* Durante a sua execução, o Airflow considerará apenas arquivos onde as strings "airflow" e "DAG" apareçam no conteúdo do arquivo python (.py)
* Um DAG Principal deve ser definido sempre no contexto global, enquanto sub dags atrelados ao primeiro dag podem ser definidos numa função do DAG principal (caso haja dependência entre os DAG's)
#### Argumentos padrão para definição de DAG's
* É costume criar um dicionário com os argumentos e os valores a serem passados para uma instância de um DAG
* Estes argumentos definem como irão se comportar todas as tarefas atreladas àquele DAG
  
```{python}
default_args = {
    'start_date': datetime(2016, 1, 1),
    'owner': 'Airflow'
}

dag = DAG('meu_primeiro_dag', default_args=default_args)
op = DummyOperator(task_id='dummy', dag=dag)
print(op.owner) # Airflow
```
#### Utiliação do gerenciador de contexto para instanciar DAG's
* DAG's podem ser instanciados utilizando gerenciador de contextos para automaticamente atribuir novos operadores instanciados ao DAG instanciado com o gerenciador de contexto
```{python}
with DAG('meu_primeiro_dag', start_date=datetime(2016, 1, 1)) as dag:
    op = DummyOperator('op')

op.dag is dag # True
```
### Operators, Operadores
* Enquanto um DAG representa um fluxo de trabalho, Operadores determinam as tarefas a serem executadas e que compõem este fluxo
* Cada operador deve representar apenas 1 tarefa do fluxo de trabalho
* O DAG irá garantir que os operadores sejam executados na ordem adequada, para aqueles que estejam atrelados em alguma dependência, e para aqueles onde não há dependências, eles serão executados de forma independente.
* No entanto, se dois operadores precisam compartilhar uma informação, como um nome de arquivo ou um conjunto de dados, estes operadores devem ser combinados em um único operador. Caso não seja possível, deve-se utilizar o XCom do Airflow.
#### Operadores mais comumente utilizados
* BashOperator - Executa um comando de terminal linux (bash)
* PythonOperator - Executa uma função Python
* EmailOperator - Envia um e-mail
* SimpleHttpOperator - Envia uma requisição HTTP
* Operadores de SGBD's que executam comandos SQL: MySqlOperator, SqliteOperator, PostgresOperator, MsSqlOperator, OracleOperator, JdbcOperator, etc...
* Sensor - Aguarda por um certo período de tempo, arquivo, eventos, linhas de um banco de dados, etc…
* Outros operadores de ferramentas de terceiros, como docker, etc...
#### Atribuindo operadores a um DAG
* Podemos atribuir um operador a um DAG de diversas formas, explícita, deferida ou inferida
```{python}
dag = DAG('my_dag', start_date=datetime(2016, 1, 1))

# atribuição explícita
explicit_op = DummyOperator(task_id='op1', dag=dag)

# atribuição deferida (
deferred_op = DummyOperator(task_id='op2')
deferred_op.dag = dag

# atribuição inferida (operadores associados devem estar no mesmo DAG)
inferred_op = DummyOperator(task_id='op3')
inferred_op.set_upstream(deferred_op)
```
## Principais Abordagens de desenvolvimento de pipelines no Airflow
* **Abordagem Orientada a Tarefas (_Task-Oriented Approach_):** A maneira tradicional de criar DAGs no Airflow, onde tarefas individuais realizam ações e suas dependências são definidas. No Airflow 3, ainda é válido usar operadores tradicionais como `BashOperator`, `PythonOperator` e `SQLExecuteQueryOperator`, que estão contidos em pacotes de provedores adicionais.
* **Abordagem Orientada a Ativos (_Asset-Oriented Approach_):** Uma **nova e significativa mudança de paradigma no Airflow 3**, onde os _pipelines_ são definidos com base nos **objetos de dados que produzem** (ativos). Um ativo é identificado por um nome único e pode ter um URI (Uniform Resource Identifier). Essa abordagem é **orientada a dados**, com os objetos de dados no centro tanto no código quanto na UI do Airflow.
---

## Escrevendo seu primeiro pipeline

**Principais Conceitos do Capítulo:**
* **Planejamento de _Pipelines_:** Antes de codificar, é crucial ter um **plano arquitetônico meticuloso**, compreendendo as estruturas de dados de entrada e saída. Diagramas de projeto são fundamentais para comunicação e estratégia.
* **Ambiente de Desenvolvimento Local:** A maneira mais fácil de iniciar um ambiente de desenvolvimento Airflow local e conteinerizado é usando o **Astro CLI**. Ele roda o Airflow em contêineres e utiliza a imagem Astro Runtime.
### Escrita de _Pipelines_ (Abordagem Orientada a Ativos)
  * Usa o decorador `@asset`.
  * Cada função decorada com `@asset` cria um DAG subjacente com uma única tarefa que executa a função e atualiza um objeto _asset_ do Airflow.
  * As **dependências entre DAGs definidos por `@asset` são estabelecidas com base na materialização do ativo**.
  * **Passagem de Dados entre Ativos/Tarefas (_XCom_):** Para prototipagem, passar pequenas quantidades de dados via XCom (Armazenado no banco de dados de metadados do Airflow) é aceitável. **Para produção, é fortemente recomendado usar um _custom XCom backend_** que armazena os dados em armazenamento de objetos externo (ex: S3, GCS), passando apenas uma referência (URI) para o banco de dados de metadados do Airflow.
  * **Airflow Object Storage:** Uma funcionalidade que fornece uma abstração para interagir com diferentes sistemas de armazenamento de arquivos (local ou baseado em nuvem como S3, GCS, Azure Blob Storage), facilitando a transição para a produção.
  * **Contexto do Airflow:** Um dicionário contendo informações sobre o ambiente e a execução atual do DAG, incluindo o `task instance (ti)` para puxar valores do XCom e a data de execução para _filenames_ (garantindo **idempotência**).
### Escrita de _Pipelines_ (Abordagem Orientada a Tarefas)
  * Usa o decorador `@dag`.
  * Contém qualquer número de tarefas definidas usando `@task` e/ou classes de operadores tradicionais (ex: `PythonOperator`, `BashOperator`, `EmptyOperator`, `SQLExecuteQueryOperator`).
  * **Inferência de Dependências:** Ao usar decoradores `@task`, o Airflow infere automaticamente as dependências com base nas entradas das tarefas.
  * **Melhor Prática:** É mais fácil depurar múltiplos **pequenos tarefas** do que grandes tarefas monolíticas.
* **Idempotência:** Garante que múltiplas execuções de uma tarefa ou DAG produzam o mesmo resultado. O `execution_date` pode ser usado em _filenames_ para garantir que a reexecução sobrescreva o arquivo existente, tornando a tarefa idempotente.
* Uma task, ou tarefa, nada mais é do que uma instância de um operator, e normalmente é armazenada numa variável de um script python
  ```{python}
  
  ```
---

## Agendamento de execução e confiabilidade
### Agendamento de _Pipelines_
* O Airflow usa três parâmetros para agendar um DAG: `start_date`, `schedule_interval` e `end_date` (opcional).
* No Airflow 3, os _timetables_ (`CronDataIntervalTimetable` e `CronTriggerTimetable`) diferenciam o `data_interval_start` (equivalente ao `logical_date`) e `data_interval_end` (o ponto no tempo após o qual o DAG roda).

### Opções de Agendamento
  * **Cron (_Time-based_):** Usa notação cron (ex: `"0 0 * * *"`) ou atalhos (ex: `"@daily"`) para agendamentos regulares baseados em tempo.
  * **Timedelta:** Agendamentos regulares a cada X período de tempo (ex: `timedelta(days=1)`).
  * **Orientado a Ativos (_Asset-based_):** DAGs são agendados para rodar assim que um ativo upstream é atualizado.
  * **Orientado a Eventos (_Event-driven scheduling_):** **Novidade no Airflow 3**, permite agendar um _workflow_ para iniciar assim que uma mensagem aparece em uma fila de mensagens. Isso é crucial para _workflows_ totalmente _data-aware_ baseados em eventos de sistemas externos, eliminando a necessidade de _sensors_ ou operadores _deferrable_ no início de um DAG.
  * **API (_API-driven_):** Rodar DAGs via requisições REST API `POST`.
  * **Contínuo:** Garante que um DAG sempre tenha uma execução ativa.
* **_Retries_ (_Tentativas_):** É possível configurar o número de tentativas para tarefas individuais (usando o decorador `@task(retries=4)`) ou globalmente (com a configuração `[core].default_task_retries`).
* **Idempotência:** A propriedade de uma tarefa produzir o mesmo resultado mesmo se executada múltiplas vezes. É uma **melhor prática** crucial para projetar tarefas, pois falhas são inevitáveis e o Airflow pode reexecutar tarefas em caso de falha. O uso do `execution_date` em _filenames_ é um exemplo de tarefa idempotente.
* **Atomicidade:** As tarefas devem ser projetadas para serem atômicas, ou seja, ou elas são bem-sucedidas e produzem um resultado adequado, ou falham de uma maneira que não afeta o estado do sistema. Isso simplifica a recuperação de falhas.
* **Backfilling:** A capacidade do Airflow de executar tarefas para todos os intervalos passados até o momento atual. Pode ser desabilitado configurando `catchup=False` no DAG. É útil para analisar _datasets_ históricos.
---
## Interface e gerenciamento
### UI do Airflow 3
* A página inicial oferece uma visão geral da saúde dos componentes do Airflow, execuções de DAGs e atualizações de ativos.
* A página de DAGs permite ativar/desativar e criar execuções manuais.
* A página do gráfico de DAGs permite visualizar tarefas e suas dependências.
* A página Airflow Connections armazena as credenciais e os detalhes de conexão a **ferramentas externas** (ex: bancos de dados, APIs, serviços de nuvem).
### Boas Práticas para Conexões e Segredos
   * Podem ser definidas na UI do Airflow, como **variáveis de ambiente** (formato URI ou JSON) ou através de um **_secrets backend_**.
   * **Evitar _hardcoding_ de credenciais** no código.
   * Utilizar **serviços de gerenciamento de segredos** (ex: AWS Secrets Manager, HashiCorp Vault) para buscar segredos externamente, em vez de armazená-los diretamente no banco de dados de metadados do Airflow. Airflow 1.10.10 introduziu o **_Secrets Backend_** para essa finalidade.
### _Dag Versioning_ e _Dag Bundles_ (Novidade no Airflow 3)
  * O Airflow 3 **sempre versiona DAGs**.
  * Uma nova versão do DAG é criada cada vez que um DAG é executado e sua estrutura é alterada.
  * Cada execução de DAG é associada a uma versão do DAG, visível na UI.
  * **_Dag Bundles_** permitem que o Airflow obtenha código de DAGs de diferentes locais (ex: repositórios GitHub, armazenamento de _blob_).
  * O `GitDagBundle` é um _dag bundle_ que suporta versionamento, rastreando mudanças de código. Ao usar `GitDagBundle`, relançar uma execução anterior utiliza a versão do DAG daquele momento, não a mais recente. Isso é valioso para a **rastreabilidade e auditoria** em equipes de engenharia de dados.
* **_Backfilling_ via UI:** O Airflow 3 permite criar _backfills_ diretamente da UI ou da REST API, com opções para selecionar o intervalo de datas, tipo (faltante, com erro, ou todos) e concorrência. Os _backfills_ são executados pelo escalonador do Airflow.
* **Airflow Plugins:** Podem estender a funcionalidade da UI. No Airflow 2, usavam Flask ou Flask-AppBuilder (FAB). Airflow 3.0 suporta apps FastAPI e planeja suportar React no futuro.
---

## Arquitetura do Apache Airflow 3
* **Mudança Arquitetural Chave no Airflow 3:** A principal diferença é que, no Airflow 2, todos os componentes interagiam diretamente com o banco de dados de metadados do Airflow. No Airflow 3, a **execução de tarefas é decoplada**; os _workers_ interagem com um **novo componente: o API Server**.
* **Benefícios da Nova Arquitetura:**
  * **Segurança Aprimorada:** O API Server atua como intermediário, removendo o acesso direto do código do usuário ao banco de dados de metadados, o que é uma **grande melhoria de segurança**. Isso resolve uma **antipattern** do Airflow 2 de acessar o banco de dados diretamente de tarefas, que poderia levar à corrupção da instância.
  * **Execução Remota de Tarefas:** Permite que os _workers_ sejam executados em máquinas remotas.
  * **Suporte a Múltiplas Linguagens:** Possibilita a definição de tarefas em linguagens diferentes de Python, através de um novo _Task Execution Interface_ (Task SDK). O Airflow 3 fornece um Task SDK em Python e um experimental em Golang.
* **Componentes do Airflow 3:**
  * **Scheduler(s):** O coração do Airflow, responsável por monitorar DAGs e tarefas, criar DAG runs e instâncias de tarefas. Seus _executors_ configurados determinam como e onde as instâncias de tarefas são executadas.
  * **Triggerer(s):** Componente para _deferrable operators_ e agendamento orientado a eventos.
  * **Dag processor(s):** Processa arquivos DAG do diretório DAGs.
  * **Worker(s):** Executam o código das tarefas.
  * **API server(s):** **Novo componente no Airflow 3**, que serve três APIs:
    1.  Uma API para _workers_ interagirem com as instâncias de tarefas usando o _Task Execution Interface_ (Task SDK), atuando como um intermediário para o banco de dados de metadados.
    2.  Uma API interna para a UI do Airflow, fornecendo atualizações em componentes dinâmicos da UI.
    3.  A API REST pública do Airflow.
  * **Airflow metadata database:** Armazena metadados do Airflow (DAGs serializados, status de tarefas, XComs, conexões, variáveis, etc.).
* **_Executors_:** Determinam como as tarefas são executadas.
  * **_Local Executors_ (SequentialExecutor e LocalExecutor):** Limitados a uma única máquina, mais fáceis de configurar. `SequentialExecutor` é para testes/demonstração (lento, roda sequencialmente), `LocalExecutor` permite paralelismo em uma única máquina.
  * **_Remote Executors_ (CeleryExecutor e KubernetesExecutor):** Permitem escalar tarefas em múltiplas máquinas. CeleryExecutor usa um sistema de enfileiramento (Redis, RabbitMQ, AWS SQS). KubernetesExecutor é acoplado ao Kubernetes, rodando tarefas em contêineres.
  * **_Hybrid Executor_ (Kubernetes Local Executor):** Combina local e remoto.
  * **AstroExecutor:** Um executor otimizado para implantações em nuvem do Astro.
* **Execução Remota (_Remote Execution_):** No Airflow 3, com o novo _Task Execution Interface_, é possível rodar tarefas em máquinas remotas. Exemplos incluem `EdgeExecutor` (para Airflow _open source_) e `Remote Execution Agent` (para Astro).
---

## Colocando um pipeline em produção
* **Planejamento para Produção:** Requer um plano **meticuloso** para garantir que o _pipeline_ seja **robusto, escalável e eficiente**.

### Configuração de Implantação
  * **Instalação de Provedores:** Adicionar pacotes de provedores do Airflow (ex: `apache-airflow-providers-amazon[s3fs]`) ao arquivo `requirements.txt` do projeto. **É uma melhor prática fixar a versão dos pacotes** para evitar incompatibilidades.
  * **Definição de Conexões:** No Astro, as conexões do Airflow podem ser definidas na UI (no _Environment Manager_) ou através de variáveis de ambiente. Para conexões AWS, são necessários o Access Key ID e Secret Access Key. As credenciais podem ser referenciadas por suas chaves (ex: `AIRFLOW_CONN_CITIBIKE`). **Melhor prática:** Consultar a documentação do pacote de provedor relevante para opções de conexão.
  * **Definição de Variáveis de Ambiente:** Podem ser configuradas no Astro para controlar o comportamento do _pipeline_ (ex: `OBJECT_STORAGE_SYSTEM`, `OBJECT_STORAGE_CONN_ID`, `OBJECT_STORAGE_PATH_NEWSLETTER`).
  * **_Custom XCom Backend_:** Para dados maiores, é aconselhável configurar um _custom XCom backend_ que armazena os dados passados entre tarefas em um **serviço de armazenamento de objetos externo** (ex: S3) em vez do banco de dados de metadados do Airflow. Isso evita que o banco de metadados fique sobrecarregado. A configuração é feita através de variáveis de ambiente (ex: `AIRFLOW__CORE__XCOM_BACKEND`).
## _Logging_ e Monitoramento (Boas Práticas Gerais)
  * **Monitoramento Ativo e Supressivo:** `Active monitoring` verifica continuamente o status de um componente ou tarefa. `Suppressive monitoring` (ou _Heartbeat monitoring_) infere a saúde pela ausência de um estado ou mudança de estado (ex: se um processo não envia um _heartbeat_, assume-se que há um problema).
  * **Monitoramento de Componentes Core:** Scheduler, banco de dados de metadados, _triggerer_, _executors_/_workers_ e _web server_.
  * **Métricas para Monitorar:** `dag_processing.last_duration.[filename]` (tempo para processar um arquivo DAG), `dag_processing.last_run.seconds_ago.[filename]` (tempo desde a última checagem do _scheduler_), `dagrun.[dag_id].first_task_scheduling_delay` (atraso entre agendamento e execução). `airflow_executor_open_slots`, `airflow_executor_queued_tasks`, `airflow_executor_running_tasks` para desempenho do executor.
  * **_Logging_ Robusto:** Essencial para depurar, capturar erros/exceções, monitorar desempenho e conformidade. Registra detalhes relevantes (entrada, transformações, contagens de registros, _stack traces_, códigos de erro).
  * **Alertas:** Enviar notificações (e-mail, Slack) em caso de falhas ou SLAs violados.
---

## Casos de Uso de IA/ML com Airflow
o Airflow é amplamente utilizado para orquestrar:
  * **Processamento de dados** para modelos de ML.
  * **Treinamento de modelos** de ML, incluindo a recuperação e processamento de dados, criação de _features_, treinamento de modelos de _deep learning_ e promoção de ativos para produção.
### Distribuição de artefatos de modelos
* **Arquitetura para Inferência (ex: Padrão _Poll_):** Pode envolver um DAG `AssetWatcher` monitorando uma fila de mensagens para informações de um formulário de _website_, o que aciona o DAG para gerar uma saída (ex: _newsletter_ personalizada).
### _MLOps Practices_ (Boas Práticas para ML _Workflows_)
  * **Separação de Código:** É uma **melhor prática** separar o código substantivo que não interage com o Airflow do código que interage com ele. Isso permite ciclos mais rápidos de depuração e testes unitários sem a necessidade de serviços externos ou _mocking_ extensivo.
  * **Segurança e Auditabilidade:** Implementar _workflows_ de ML de forma segura e auditável.
  * **Determinação de Mudanças de Dados:** Implementar lógica para determinar se os dados de entrada mudaram antes de acionar o treinamento do modelo.
  * **Promoção de Ativos:** Gerenciar a promoção de modelos treinados para produção.
  * **Telemetria:** Embora fora do escopo do livro, a coleta de telemetria especializada sobre o desempenho de modelos aprendidos e dados é importante.
---

## Migrando do Airflow 2 para o 3
### Planejamento do _Upgrade_
  * **Versão Mínima:** Para fazer o _upgrade_ do Airflow 2 para o Airflow 3, a versão mínima do Airflow 2 deve ser **2.6.3** (Astro Runtime 8.7.0) devido a restrições de migração do banco de dados.
  * **Recomendação:** É altamente recomendado fazer o _upgrade_ para a versão mais recente do Airflow 2 antes de migrar para o Airflow 3 para se beneficiar de e resolver todos os avisos de descontinuação (_deprecation warnings_).
  * **Airflow 1:** Se ainda estiver usando Airflow 1, o _upgrade_ para Airflow 2 deve ser feito primeiro, pois o suporte ao Airflow 1 terminou em junho de 2021.
* **_Breaking Changes_ Importantes no Airflow 3:**
  * **Remoção de Acesso Direto ao Banco de Dados de Metadados:** **Não é mais possível acessar diretamente o banco de dados de metadados do Airflow a partir de tarefas**. Em vez disso, deve-se usar a **Airflow REST API** para interagir e recuperar informações sobre a instância do Airflow. **Melhor prática:** Acessar o banco de dados diretamente de tarefas é uma **antipattern**.
  * **Mudanças no Agendamento:** O Airflow 3 introduz mudanças relacionadas ao agendamento de DAGs, incluindo a forma como os _timetables_ (`CronDataIntervalTimetable` e `CronTriggerTimetable`) operam.
  * **Contexto do Airflow:** Algumas variáveis do `Airflow context` foram removidas ou alteradas (ex: `conf`, `dag_run.is_backfill`, `dag_run.external_trigger`).
  * **REST API:** O Airflow 3 vem com uma nova versão da Airflow REST API (versão 2) e removeu a API experimental descontinuada do Airflow 1.
  * **_Flask-AppBuilder (FAB)_:** A dependência do FAB para componentes core do Airflow foi removida no Airflow 3 para melhorar a segurança e a mantenebilidade. A UI agora é baseada em React e o gerenciador de autenticação padrão é `SimpleAuthManager`.
  * **_Airflow Providers_:** Alguns provedores que eram pré-instalados no Airflow 2 podem não estar no Airflow 3, exigindo instalação via `pip`.
  * **Suporte a Python:** O suporte ao Python 3.8 foi removido no Airflow 3.
* **Como Fazer o _Upgrade_:**
  * **Verificação de Código de DAGs:** Usar a ferramenta **ruff** com as regras **AIR30** (para mudanças obrigatórias) e **AIR31** (para mudanças recomendadas) para verificar o código Python do DAG. Ruff é um linter de código que pode até ajustar o código automaticamente (`ruff check --fix`).
  * **Verificação de Configuração do Airflow:** Usar o comando `airflow config lint` para verificar o arquivo `airflow.cfg` em busca de alterações de configuração necessárias (ex: parâmetros movidos de seção).
---
