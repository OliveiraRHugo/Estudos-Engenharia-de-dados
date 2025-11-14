# Estudos Apache Airflow

Bibliografias: Practical Guide to Apache Airflow 3 e Datacamp

## Introdução ao Apache Airflow

* **O que é Apache Airflow:** Uma ferramenta _open source_ para **escrever, agendar e gerenciar _workflows_ como código**.
* É ideal para definir ações que dependem umas das outras e devem ser realizadas em uma ordem específica. O Airflow é considerado o **líder da indústria em orquestração de dados e gerenciamento de _pipelines_**.
* **_Data Orchestration_:** Envolve a **coordenação, automação e monitoramento de _workflows_ de dados**, garantindo a execução suave de tarefas e a entrega oportuna de _insights_ valiosos.
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
---

## Capítulo 3: Reliability and scheduling

**Foco da Discussão do Capítulo:**
Este capítulo aborda a **confiabilidade e as diversas opções de agendamento** no Airflow 3. Ele explora como configurar DAGs para rodar em intervalos regulares, introduzindo o **agendamento orientado a eventos** e discute como lidar com a execução de tarefas e a recuperação de falhas.

**Principais Conceitos do Capítulo:**
* **Agendamento de _Pipelines_:** O Airflow usa três parâmetros para agendar um DAG: `start_date`, `schedule_interval` e `end_date` (opcional). No Airflow 3, os _timetables_ (`CronDataIntervalTimetable` e `CronTriggerTimetable`) diferenciam o `data_interval_start` (equivalente ao `logical_date`) e `data_interval_end` (o ponto no tempo após o qual o DAG roda).
* **Opções de Agendamento:**
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

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **essencial para o engenheiro de dados que busca criar _pipelines_ confiáveis e eficientes**. Ele fornece o conhecimento para:
* **Configurar o agendamento de DAGs** de acordo com os requisitos de negócio (tempo ou evento).
* Implementar **mecanismos de _retry_** para aumentar a resiliência.
* Adotar a **melhor prática de idempotência e atomicidade** no design de tarefas para garantir a consistência e a capacidade de recuperação.
* Compreender o **_backfilling_** para reprocessar dados históricos, o que é vital para a **confiabilidade dos dados e a otimização de recursos**.

---

## Capítulo 4: UI and dag versioning

**Foco da Discussão do Capítulo:**
Este capítulo foca na **interface do usuário (UI) do Airflow 3** e em como **gerenciar _pipelines_ em ambientes de produção**, introduzindo conceitos como _dag versioning_, _dag bundles_ e _backfilling_ a partir da UI. Ele também mostra como **conectar o Airflow a ferramentas externas** usando Airflow Connections.

**Principais Conceitos do Capítulo:**
* **UI Modernizada do Airflow 3:** A UI foi completamente redesenhada em React, incluindo novos recursos como **_dark mode_**, acesso mais fácil aos _logs_ e gráficos de ativos. A página inicial oferece uma visão geral da saúde dos componentes do Airflow, execuções de DAGs e atualizações de ativos. A página de DAGs permite ativar/desativar e criar execuções manuais. O **gráfico de DAGs** permite visualizar tarefas e suas dependências.
* **Airflow Connections:** Usadas para armazenar credenciais e detalhes de conexão a **ferramentas externas** (ex: bancos de dados, APIs, serviços de nuvem).
  * **Boas Práticas para Conexões e Segredos:**
    * Podem ser definidas na UI do Airflow, como **variáveis de ambiente** (formato URI ou JSON) ou através de um **_secrets backend_**. O AWS CLI é uma ferramenta fundamental para gerenciar credenciais na AWS.
    * **Evitar _hardcoding_ de credenciais** no código.
    * Utilizar **serviços de gerenciamento de segredos** (ex: AWS Secrets Manager, HashiCorp Vault) para buscar segredos externamente, em vez de armazená-los diretamente no banco de dados de metadados do Airflow. Airflow 1.10.10 introduziu o **_Secrets Backend_** para essa finalidade.
    * Para conexões AWS, a classe `AwsBaseHook` fornece uma interface genérica para serviços AWS usando a biblioteca `boto3`.
* **_Dag Versioning_ e _Dag Bundles_ (Novidade no Airflow 3):**
  * O Airflow 3 **sempre versiona DAGs**.
  * Uma nova versão do DAG é criada cada vez que um DAG é executado e sua estrutura é alterada.
  * Cada execução de DAG é associada a uma versão do DAG, visível na UI.
  * **_Dag Bundles_** permitem que o Airflow obtenha código de DAGs de diferentes locais (ex: repositórios GitHub, armazenamento de _blob_).
  * O `GitDagBundle` é um _dag bundle_ que suporta versionamento, rastreando mudanças de código. Ao usar `GitDagBundle`, relançar uma execução anterior utiliza a versão do DAG daquele momento, não a mais recente. Isso é valioso para a **rastreabilidade e auditoria** em equipes de engenharia de dados.
* **_Backfilling_ via UI:** O Airflow 3 permite criar _backfills_ diretamente da UI ou da REST API, com opções para selecionar o intervalo de datas, tipo (faltante, com erro, ou todos) e concorrência. Os _backfills_ são executados pelo escalonador do Airflow.
* **Airflow Plugins:** Podem estender a funcionalidade da UI. No Airflow 2, usavam Flask ou Flask-AppBuilder (FAB). Airflow 3.0 suporta apps FastAPI e planeja suportar React no futuro.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **fundamental para o engenheiro de dados que gerencia _pipelines_ em ambientes de produção**. Ele capacita o engenheiro a:
* **Utilizar a UI moderna do Airflow 3** para monitorar e interagir com DAGs e ativos de forma eficiente.
* Implementar **melhores práticas para gerenciar Airflow Connections** e segredos, garantindo a **segurança e flexibilidade** na integração com sistemas externos.
* Aproveitar o **_dag versioning_ e os _dag bundles_** para rastrear mudanças no _workflow_, o que é crucial para **auditoria, colaboração e recuperação**.
* Realizar **_backfills_** para reprocessar dados históricos, mantendo a **confiança e a completude dos dados**.

---

## Capítulo 5: Airflow 3 architecture

**Foco da Discussão do Capítulo:**
Este capítulo oferece uma visão detalhada da **arquitetura do Airflow 3**, destacando suas diferenças em relação ao Airflow 2 e os benefícios do **decouplamento da execução de tarefas do banco de dados de metadados**. Ele também apresenta os componentes do Airflow 3 e as opções para executar tarefas em máquinas remotas.

**Principais Conceitos do Capítulo:**
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

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **vital para arquitetos de dados e engenheiros de plataforma/DevOps** que projetam e operam implantações de Airflow em escala. Ele permite ao engenheiro:
* Compreender as **mudanças arquiteturais significativas do Airflow 3** e seus impactos na segurança, escalabilidade e flexibilidade.
* **Escolher o _executor_ mais apropriado** com base nos requisitos de desempenho e ambiente de implantação.
* Alavancar as novas capacidades de **execução remota e suporte a múltiplas linguagens** para construir _pipelines_ mais robustos e otimizados, alinhados com **melhores práticas de segurança** em sistemas distribuídos.

---

## Capítulo 6: Moving to production

**Foco da Discussão do Capítulo:**
Este capítulo guia o leitor através do processo de **mover um _pipeline_ do Airflow para a produção**, focando no ambiente **Astro (plataforma DataOps da Astronomer)**. Ele aborda a implantação de código, configuração de conexões e variáveis, uso de _custom XCom backends_ e observabilidade com o Astro Observe.

**Principais Conceitos do Capítulo:**
* **Planejamento para Produção:** Requer um plano **meticuloso** para garantir que o _pipeline_ seja **robusto, escalável e eficiente**.
* **Implantação de Código no Astro:**
  * Astro é uma plataforma DataOps totalmente gerenciada e alimentada por Apache Airflow, oferecendo uma conta de avaliação gratuita.
  * O código pode ser implantado no Astro usando integração com GitHub.
* **Configuração de Implantação:**
  * **Instalação de Provedores:** Adicionar pacotes de provedores do Airflow (ex: `apache-airflow-providers-amazon[s3fs]`) ao arquivo `requirements.txt` do projeto. **É uma melhor prática fixar a versão dos pacotes** para evitar incompatibilidades.
  * **Definição de Conexões:** No Astro, as conexões do Airflow podem ser definidas na UI (no _Environment Manager_) ou através de variáveis de ambiente. Para conexões AWS, são necessários o Access Key ID e Secret Access Key. As credenciais podem ser referenciadas por suas chaves (ex: `AIRFLOW_CONN_CITIBIKE`). **Melhor prática:** Consultar a documentação do pacote de provedor relevante para opções de conexão.
  * **Definição de Variáveis de Ambiente:** Podem ser configuradas no Astro para controlar o comportamento do _pipeline_ (ex: `OBJECT_STORAGE_SYSTEM`, `OBJECT_STORAGE_CONN_ID`, `OBJECT_STORAGE_PATH_NEWSLETTER`).
  * **_Custom XCom Backend_:** Para dados maiores, é aconselhável configurar um _custom XCom backend_ que armazena os dados passados entre tarefas em um **serviço de armazenamento de objetos externo** (ex: S3) em vez do banco de dados de metadados do Airflow. Isso evita que o banco de metadados fique sobrecarregado. A configuração é feita através de variáveis de ambiente (ex: `AIRFLOW__CORE__XCOM_BACKEND`).
* **Observabilidade com Astro Observe:**
  * O Astro Observe oferece funcionalidades para monitoramento da saúde dos _pipelines_, incluindo alertas de falhas e SLAs (_Service Level Agreements_) de pontualidade e frescor dos dados.
  * Permite definir **_Data Products_** (agrupamentos de ativos) e configurar SLAs para eles, com a inferência automática de dependências _upstream_.
* **_Logging_ e Monitoramento (Boas Práticas Gerais):**
  * **Monitoramento Ativo e Supressivo:** `Active monitoring` verifica continuamente o status de um componente ou tarefa. `Suppressive monitoring` (ou _Heartbeat monitoring_) infere a saúde pela ausência de um estado ou mudança de estado (ex: se um processo não envia um _heartbeat_, assume-se que há um problema).
  * **Monitoramento de Componentes Core:** Scheduler, banco de dados de metadados, _triggerer_, _executors_/_workers_ e _web server_.
  * **Métricas para Monitorar:** `dag_processing.last_duration.[filename]` (tempo para processar um arquivo DAG), `dag_processing.last_run.seconds_ago.[filename]` (tempo desde a última checagem do _scheduler_), `dagrun.[dag_id].first_task_scheduling_delay` (atraso entre agendamento e execução). `airflow_executor_open_slots`, `airflow_executor_queued_tasks`, `airflow_executor_running_tasks` para desempenho do executor.
  * **_Logging_ Robusto:** Essencial para depurar, capturar erros/exceções, monitorar desempenho e conformidade. Registra detalhes relevantes (entrada, transformações, contagens de registros, _stack traces_, códigos de erro).
  * **Alertas:** Enviar notificações (e-mail, Slack) em caso de falhas ou SLAs violados.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **crítico para o engenheiro de dados que precisa implantar e manter _pipelines_ Airflow em ambientes de produção**. Ele oferece as ferramentas e as **melhores práticas para:**
* Configurar implantações em nuvem (Astro/AWS) de forma segura e eficiente.
* Otimizar o armazenamento e o desempenho de XComs.
* **Implementar monitoramento e observabilidade proativos (Astro Observe)** para garantir a pontualidade e a qualidade dos dados.
* Compreender os aspectos operacionais que garantem a **confiabilidade, escalabilidade e sustentabilidade** dos _pipelines_ de dados em larga escala.

---

## Capítulo 7: Inference execution

**Foco da Discussão do Capítulo:**
Este capítulo explora um caso de uso específico para o Airflow 3: **execução de inferência**, especialmente em contextos de IA generativa (GenAI). Ele demonstra como um DAG pode ser usado como _backend_ para solicitações de inferência, permitindo execuções simultâneas com as mesmas entradas.

**Principais Conceitos do Capítulo:**
* **_Inference Execution_ (Novidade no Airflow 3):** O Airflow 3 permite executar o mesmo DAG várias vezes no exato mesmo momento (fornecendo `None` como _logical date_). Isso habilita casos de uso de GenAI, como usar um DAG como _backend_ para solicitações de inferência.
* **Casos de Uso de IA/ML com Airflow:** Embora o capítulo foque em inferência, o Airflow é amplamente utilizado para orquestrar:
  * **Processamento de dados** para modelos de ML.
  * **Treinamento de modelos** de ML, incluindo a recuperação e processamento de dados, criação de _features_, treinamento de modelos de _deep learning_ e promoção de ativos para produção.
  * **Distribuição de artefatos de modelos**.
* **Arquitetura para Inferência (ex: Padrão _Poll_):** Pode envolver um DAG `AssetWatcher` monitorando uma fila de mensagens para informações de um formulário de _website_, o que aciona o DAG para gerar uma saída (ex: _newsletter_ personalizada).
* **_MLOps Practices_ (Boas Práticas para ML _Workflows_):**
  * **Separação de Código:** É uma **melhor prática** separar o código substantivo que não interage com o Airflow do código que interage com ele. Isso permite ciclos mais rápidos de depuração e testes unitários sem a necessidade de serviços externos ou _mocking_ extensivo.
  * **Segurança e Auditabilidade:** Implementar _workflows_ de ML de forma segura e auditável.
  * **Determinação de Mudanças de Dados:** Implementar lógica para determinar se os dados de entrada mudaram antes de acionar o treinamento do modelo.
  * **Promoção de Ativos:** Gerenciar a promoção de modelos treinados para produção.
  * **Telemetria:** Embora fora do escopo do livro, a coleta de telemetria especializada sobre o desempenho de modelos aprendidos e dados é importante.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **relevante para engenheiros de dados e engenheiros de ML** que trabalham com sistemas de IA. Ele capacita o engenheiro a:
* Alavancar as **novas capacidades de inferência do Airflow 3** para construir _backends_ de IA/ML escaláveis e reativos.
* Aplicar **melhores práticas de MLOps** para orquestrar o ciclo de vida de modelos, desde o processamento de dados até o treinamento e a implantação, garantindo a **eficiência, segurança e auditabilidade** em ambientes de produção.

---

## Capítulo 8: Upgrading: Airflow 2 to 3

**Foco da Discussão do Capítulo:**
Este capítulo foca na **migração do Airflow 2 para o Airflow 3**, detalhando as **principais mudanças que quebram a compatibilidade (_breaking changes_)** e as **ferramentas de atualização** disponíveis. Ele oferece um guia prático para verificar o código do DAG e a configuração do Airflow antes e durante o processo de _upgrade_.

**Principais Conceitos do Capítulo:**
* **Planejamento do _Upgrade_:**
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

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **indispensável para engenheiros de plataforma e engenheiros de dados que estão atualizando ambientes de Airflow existentes**. Ele capacita o engenheiro a:
* **Planejar e executar uma migração de Airflow de forma eficiente e segura**.
* Identificar e resolver **_breaking changes_** no código do DAG e na configuração.
* Utilizar **ferramentas de automação (ruff, `airflow config lint`)** para agilizar o processo de _upgrade_, minimizando o esforço manual e garantindo a **continuidade e a estabilidade** dos _pipelines_ de dados.

---

## Capítulo 9: The future of Airflow

**Foco da Discussão do Capítulo:**
Este capítulo explora o **futuro do Apache Airflow além da versão 3.0**, destacando os marcos de desenvolvimento, as prioridades futuras e as propostas de melhoria (AIPs) aceitas. Ele também aborda o **processo de desenvolvimento liderado pela comunidade** e como os usuários podem se envolver.

**Principais Conceitos do Capítulo:**
* **Marcos do Airflow:** O Airflow 3 representa um marco significativo, com duas mudanças fundamentais: a **expansão da consciência de dados através de ativos** e a **nova interface de execução de tarefas**. Essas mudanças abrem caminho para futuras melhorias em áreas como facilidade de uso, consciência de dados e execução de tarefas em qualquer lugar, a qualquer hora e em qualquer linguagem.
* **_Airflow Improvement Proposals (AIPs)_:** Descrevem as funcionalidades aceitas e planejadas para futuras versões (ex: Airflow 3.1+).
* **_Asset Partitions_ (AIP-76):** Uma funcionalidade planejada que permitirá definir **partições no nível do ativo**. Isso significa que partes do seu dado podem ser processadas incrementalmente e independentemente do agendamento do ativo. Isso pode **melhorar o desempenho** para grandes conjuntos de dados e facilitar o **rastreamento da linhagem de dados**. Será possível combinar partições baseadas em tempo e segmento.
* **_Multi-team deployment of Airflow components_ (AIP-67):** Outra proposta aceita para futuras versões.
* **Desenvolvimento Liderado pela Comunidade:** O Airflow é um projeto _open source_ liderado pela comunidade da Apache Software Foundation (ASF), com milhares de membros e contribuidores. As decisões de desenvolvimento e lançamento são governadas pelas regras Apache.
* **Como se Envolver:** Os usuários podem se envolver na comunidade do Airflow por meio do Airflow Slack, listas de e-mail e contribuindo para o projeto (ex: melhorias na documentação, mudanças de código em pacotes de provedores, abertura de _pull requests_).
* **O Futuro do Astro:** A Astronomer (empresa por trás do Astro) continuará a aprimorar sua plataforma DataOps unificada baseada em Apache Airflow.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo oferece ao engenheiro de dados uma **visão estratégica e um roteiro para o futuro do Airflow**. Ele permite:
* Entender as **direções de desenvolvimento da plataforma**, preparando-se para futuras funcionalidades (como _asset partitioning_) que impactarão o design e a eficiência dos _pipelines_.
* Incentivar o **engajamento com a comunidade _open source_**, o que é valioso para crescimento profissional, colaboração e influência no desenvolvimento da ferramenta.
* Garantir que os **_pipelines_ sejam projetados com uma visão de futuro**, aproveitando as inovações que virão para construir soluções de dados mais eficientes, escaláveis e com maior consciência de dados.

---

## Capítulo 10: Resources

**Foco da Discussão do Capítulo:**
Este capítulo final serve como um guia consolidado de **recursos para aprendizado contínuo sobre o Airflow**, engajamento com a comunidade e onde encontrar respostas para dúvidas. Ele visa inspirar os leitores a aprofundar seus conhecimentos e se tornarem membros ativos da comunidade global do Airflow.

**Principais Conceitos do Capítulo:**
* **Recursos de Aprendizado:**
  * **Documentação Oficial do Apache Airflow:** Contém instruções detalhadas para as funcionalidades mais recentes, exemplos de código e páginas de referência (Configuração, API, CLI, Variáveis de Ambiente, Templates).
  * **Astronomer Academy:** Oferece cursos completos em vídeo e certificações em Airflow.
  * **_Data Pipelines with Apache Airflow_ (2ª edição):** Um livro abrangente que aborda conceitos do Airflow e melhores práticas, incluindo testes de DAGs, ML e GenAI.
  * **_Airflow podcast_:** Um recurso de áudio para aprender mais sobre Airflow.
* **Ferramentas Úteis:**
  * **Astro:** Plataforma DataOps unificada e gerenciada para rodar Airflow em produção na nuvem.
  * **Astro CLI:** Ferramenta gratuita para rodar Airflow em contêineres na máquina local.
* **Envolvimento na Comunidade:**
  * **Airflow Slack:** O primeiro passo para se envolver como usuário e contribuinte.
  * **GitHub (apache/airflow):** Para abrir _issues_ (bugs) ou contribuir com código e documentação.
  * **_Ask Astro_:** Um _chatbot_ conversacional com conhecimento avançado de Airflow e Astronomer.
* **Outras Boas Práticas Mencionadas:**
  * **Boas Práticas de Código:**
    * **_Clean DAGs_:** Escrever DAGs claros e fáceis de entender. Evitar código excessivamente complicado ou difícil de ler.
    * **_Code formatters_ e _static checkers_:** Usar ferramentas para manter a consistência do estilo de código e identificar problemas.
    * **_Functional Data Engineering_:** Abordagem que promove a escrita de código funcional para _pipelines_, com tarefas que recebem entradas e produzem saídas sem efeitos colaterais.
    * **_Test-driven development (TDD)_:** Escrever testes antes do código de produção.
  * **Tratamento Eficiente de Dados:**
    * **Limitar a quantidade de dados processados:** Fazer a filtragem e a agregação o mais cedo possível no _pipeline_.
    * **Carregamento/processamento incremental:** Processar apenas dados novos ou alterados para otimizar desempenho.
    * **_Cache intermediate data_:** Armazenar resultados intermediários para evitar recomputações.
    * **_Offload work to external/source systems_:** Usar ferramentas externas (ex: Spark) para o processamento de dados pesado, enquanto o Airflow orquestra. O Airflow é uma ferramenta de orquestração, não de processamento de dados em si.
    * **Não armazenar dados em sistemas de arquivos locais:** Usar armazenamento persistente (S3, GCS, bancos de dados) para dados intermediários e finais.
  * **Gerenciamento de Recursos:**
    * **Gerenciar concorrência usando _pools_:** Limitar o número de tarefas que podem ser executadas simultaneamente.
    * **Detectar tarefas de longa duração usando SLAs e alertas:** Definir SLAs para monitorar a duração das tarefas e enviar alertas em caso de violações.
  * **Melhores Práticas de Implantação:**
    * **CI/CD (Continuous Integration/Continuous Deployment):** Essencial para automação, eficiência, confiabilidade e velocidade na implantação de _pipelines_ ETL.
    * **_Repository structures_ (Mono-repo vs. Multi-repo):** Escolha de estrutura de repositório para o código do DAG. _Mono-repo_ é poderoso para estratégias de "trabalho na cabeça".
    * **Teste (DAGs, Provedores, Airflow):** Testar DAGs, provedores e a própria instalação do Airflow.
    * **Evitar SPOFs (_Single Points of Failure_):** No Airflow 2, um `CeleryExecutor` ou `KubernetesExecutor` é recomendado para evitar SPOFs e escalar. O Airflow 3, com sua nova arquitetura, decopla ainda mais os componentes.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é o **complemento final para o desenvolvimento profissional de um engenheiro de dados**. Ele fornece:
* Um **roteiro para aprendizado contínuo**, garantindo que o engenheiro se mantenha atualizado com as últimas inovações do Airflow.
* **Diretrizes para se envolver ativamente na comunidade**, o que pode impulsionar o crescimento da carreira e a colaboração.
* Uma **visão consolidada das melhores práticas** abordadas ao longo dos livros, abrangendo desde o design de código até a operação em produção, garantindo que o engenheiro possa construir, implantar e manter **_pipelines_ de dados de alta qualidade, confiáveis e eficientes** em qualquer ambiente.

---
