# Resumo do livro Data Pipelines Pocket Refference

## Capítulo 1: Introduction to Data Pipelines

**Foco da Discussão do Capítulo:**
Este capítulo introduz o conceito de **pipelines de dados**, definindo o que são, quem os constrói, por que são essenciais e como são implementados. Enfatiza que os pipelines são a **fundação para o sucesso** em análises de dados e _machine learning_, transformando dados brutos em valor.

**Principais Conceitos do Capítulo:**
* **Definição de Pipelines de Dados:** Mecanismos que movem dados de diversas fontes, os processam e combinam para entregar valor ao consumidor. Assim como o refino do petróleo, os dados precisam ser refinados para terem valor.
* **Construtores de Pipelines:** Inclui engenheiros de dados, líderes técnicos, engenheiros de _data warehouse_, engenheiros de análise, engenheiros de BI e líderes de análise.
* **Propósito dos Pipelines:** Fornecer contexto aos dados brutos, limpá-los, processá-los e combiná-los para uso em painéis (dashboards), modelos de _machine learning_ e _insights_ de negócio.
* **Considerações e Decisões Chave:** Aborda aspectos como ingestão de dados _batch_ (em lote) versus _streaming_ (em fluxo), e a decisão de construir soluções internamente ou comprar ferramentas prontas.
* **Natureza Contínua:** Pipelines não são construídos apenas uma vez; são **monitorados, mantidos e estendidos** para entregar e processar dados de forma confiável, segura e pontual.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo fornece a **visão fundamental e estratégica** para o engenheiro de dados, posicionando seu trabalho como **essencial** para o desbloqueio do valor dos dados em uma organização. O engenheiro de dados aprende que seu papel vai além da entrega inicial, exigindo a construção de **infraestrutura de suporte** que garante a confiabilidade e a evolução contínua dos dados. É o ponto de partida para entender as **decisões arquiteturais** e as complexidades que serão exploradas nos capítulos seguintes.

---

## Capítulo 2: A Modern Data Infrastructure

**Foco da Discussão do Capítulo:**
Descrever os **componentes essenciais de uma infraestrutura de dados moderna** e as **características dos dados e sistemas de origem** que influenciam o design dos pipelines. Enfatiza a **diversidade dos dados** e a importância da **qualidade e validação**.

**Principais Conceitos do Capítulo:**
* **Diversidade de Fontes de Dados:** Dados vêm de várias interfaces (bancos de dados, APIs, sistemas de mensagens, HDFS) e estruturas (JSON, dados bem estruturados, semistruturados como logs, não estruturados como texto livre ou imagens).
    * **Dados Semiestruturados (ex: JSON):** Vantagem de pares atributo-valor e aninhamento, mas sem garantia de estrutura uniforme entre objetos.
    * **Dados Não Estruturados (ex: texto livre, imagens):** Usados para PNL e visão computacional.
* **Volume de Dados:** As decisões de design dos pipelines devem considerar o volume, mas **alto volume não significa alto valor**. É um espectro, não uma definição binária.
* **Qualidade e Validade dos Dados:** Dados de origem raramente são perfeitos. É crucial **assumir o pior**, esperar o melhor e **validar os dados frequentemente**.
    * **Características de Dados "Sujeitos":** Registros duplicados/ambíguos, órfãos, incompletos/ausentes, erros de codificação de texto, formatos inconsistentes, dados mal rotulados.
    * **Melhores Práticas:** Limpar e validar dados no sistema mais adequado para isso (ex: pós-carregamento em ELT).
* **Latência e Largura de Banda do Sistema de Origem:** Extrair grandes volumes de dados de sistemas de origem pode causar sobrecarga (limites de taxa de API, _timeouts_, lentidão), exigindo consideração no design.
* **Componentes Chave da Infraestrutura:** Incluem _cloud data warehouses_ e _data lakes_ (impacto transformador na analítica), ferramentas de ingestão, ferramentas de transformação e modelagem, e plataformas de orquestração de _workflow_.
    * **Ferramentas de Ingestão:** Podem ter algumas capacidades de transformação, mas focam principalmente em extrair e carregar.
    * **Transformação vs. Modelagem:** **Transformação** é um termo amplo (converter _timestamp_, criar métricas). **Modelagem de dados** é um tipo específico de transformação que estrutura e define dados para análise.
    * **SQL para Modelagem:** Preferível a ferramentas _no-code_ por oferecer personalização e controle total sobre o processo de desenvolvimento.
* **Customização da Infraestrutura:** A maioria das organizações seleciona ferramentas e fornecedores para suas necessidades específicas e constrói o restante internamente.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Para o engenheiro de dados, este capítulo é crucial para **entender o ecossistema** em que opera. Ele orienta sobre como as **características dos dados de origem** (estrutura, volume, limpeza, latência) devem **informar as decisões de design** dos pipelines. O engenheiro aprende a **selecionar as ferramentas** adequadas (ingestão, transformação, orquestração), a **abordar a qualidade dos dados** proativamente e a considerar os **_trade-offs_ entre construir e comprar** soluções.

---

## Capítulo 3: Common Data Pipeline Patterns

**Foco da Discussão do Capítulo:**
Definir os **padrões mais comuns de pipelines de dados**, com foco na evolução e nos benefícios do **ELT (Extract, Load, Transform)** em relação ao ETL (Extract, Transform, Load). Explica o papel crucial dos **bancos de dados colunares** e introduz o subpadrão EtLT.

**Principais Conceitos do Capítulo:**
* **Padrões ETL e ELT:**
    * **ETL:** Extrair dados, transformar em um sistema separado e, em seguida, carregar no _data warehouse_.
    * **ELT:** Extrair dados, carregar dados brutos diretamente no _data warehouse_ e, em seguida, transformar os dados dentro do _warehouse_.
* **Separação de Extração e Carga:** A **ingestão de dados** combina as etapas de extração e carga, mas devem ser consideradas separadas devido à complexidade de coordenação.
* **Emergência do ELT sobre ETL:** O ELT tornou-se o padrão preferido devido à ascensão de **_cloud data warehouses_** com:
    * **Bancos de dados colunares altamente escaláveis:** Capazes de armazenar e executar transformações em massa em grandes conjuntos de dados de forma **custo-eficaz**.
    * **Eficiência de I/O:** Bancos de dados colunares (ex: Snowflake, Amazon Redshift) armazenam dados por coluna, minimizando a E/S de disco e a memória necessária para consultas analíticas que acessam poucas colunas de muitas linhas.
    * **Compressão Otimizada:** Blocos de dados com o mesmo tipo de dado por coluna permitem compressão mais eficiente.
* **Bancos de Dados Orientados a Linhas vs. Colunas:**
    * **Orientados a Linhas (ex: MySQL, Postgres):** Ótimos para OLTP (muitas leituras/escritas pequenas e frequentes de registros completos).
    * **Orientados a Colunas:** Ideais para análises (muitas leituras/escritas infrequentes de grandes volumes de dados, focando em poucas colunas).
* **Subpadrão EtLT (Extract-transform-Load-Transform):** Realiza **transformações limitadas e não contextuais** _após a extração, mas antes do carregamento_. Exemplos incluem deduplicação, análise de parâmetros de URL e **mascaramento de dados sensíveis** (por razões legais/de segurança).
* **ELT para Análise de Dados:** Permite que analistas sejam mais autônomos, focando engenheiros de dados na ingestão e infraestrutura.
* **ELT para Ciência de Dados e Produtos de _Machine Learning_ (ML):** O padrão ELT é adequado para alimentar modelos de ML, embora a etapa de transformação se concentre na preparação de dados para o modelo, incluindo **versionamento de dados** para treinamento e validação.
    * **Ciclo de Feedback:** Pipelines de ML devem incorporar coleta de feedback para melhoria contínua do modelo.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **fundamental para o engenheiro de dados moderno**, pois define o **padrão arquitetônico dominante (ELT)**. O engenheiro aprende a:
* **Priorizar ELT** para _data warehouses_ baseados em nuvem e bancos de dados colunares, aproveitando sua eficiência para transformação em massa.
* Aplicar o **subpadrão EtLT** para lidar com problemas de qualidade de dados ou requisitos de segurança **o mais cedo possível** no pipeline.
* Designar pipelines que suportem não apenas análises, mas também as **necessidades específicas de _machine learning_ e produtos de dados**, incluindo versionamento de dados e ciclos de feedback.
* Capacitar analistas de dados, permitindo que escrevam e implementem suas próprias transformações em SQL.

---

## Capítulo 4: Data Ingestion: Extracting Data

**Foco da Discussão do Capítulo:**
Fornecer um **guia prático para a etapa de extração** da ingestão de dados, detalhando como configurar o ambiente e extrair dados de diversas fontes comuns, como MySQL, PostgreSQL, MongoDB e APIs REST. Também aborda a ingestão de dados em _streaming_ usando Kafka e Debezium para CDC.

**Principais Conceitos do Capítulo:**
* **Data Ingestion:** Combinacão das etapas de **extração e carga**. Os exemplos de código são desacoplados, com a coordenação discutida no Capítulo 7.
* **Ambiente de Desenvolvimento Python:** Configuração de um ambiente virtual (`virtualenv`), instalação de bibliotecas Python (`configparser`, `boto3`, `pymysql`, `psycopg2`, `pymongo`, `dnspython`, `urllib.parse`) para conexão com fontes e armazenamento.
    * **`pipeline.conf`:** Arquivo para armazenar credenciais e informações de conexão de forma segura (evitar Git).
* **Armazenamento de Arquivos em Nuvem (ex: Amazon S3):** Usado como destino intermediário para dados extraídos antes de serem carregados no _data warehouse_.
    * **IAM Roles:** Prática recomendada para gerenciamento de acesso seguro.
* **Extração de MySQL:**
    * **Extração Completa (`Full Extraction`):** Extrai todos os registros da tabela a cada execução. Simples, mas ineficiente para grandes volumes com muitas mudanças.
    * **Extração Incremental (`Incremental Extraction`):** Extrai apenas registros novos ou alterados desde a última execução, usando uma coluna `LastUpdated`. Mais eficiente, mas não captura exclusões e depende da confiabilidade do _timestamp_ da fonte.
    * **Replicação de Log Binário (`Binary Log Replication` - `binlog`):** Método de CDC para ingestão de alto volume e frequência. Mais complexo, mas registra cada operação (INSERT, UPDATE, DELETE). Requer configuração do _binlog_ em formato `ROW`.
        * **`python-mysql-replication`:** Biblioteca Python para ler o _binlog_.
        * **Log Position (`log_pos`):** Deve ser armazenado para controle do ponto de reinício da extração.
* **Extração de PostgreSQL:**
    * **Extrações Completas ou Incrementais com SQL:** Similar ao MySQL, usando `psycopg2` como biblioteca Python.
    * **Replicação via WAL (`Write-Ahead Log`):** Método de CDC. Complexo, mas pode ser simplificado com frameworks como Debezium.
* **Extração de MongoDB:**
    * Extração de documentos de uma coleção, geralmente por intervalo de datas. Documentos são semistruturados e podem ter campos ausentes, exigindo tratamento (ex: `doc.get("field", None)`).
    * **`PyMongo` e `dnspython`:** Bibliotecas Python para conexão.
* **Extração de REST API:** Padrão comum, envolve fazer requisições HTTP GET, analisar JSON e armazenar em CSV.
* **Ingestão de Dados em _Streaming_ com Kafka e Debezium:**
    * **Debezium:** Sistema distribuído _open source_ que captura mudanças em nível de linha de sistemas CDC e as transmite como eventos.
    * **Componentes do Debezium:** Conectores para fontes (MySQL, Postgres, MongoDB, etc.) e para destinos (Kafka, S3, Snowflake).

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é o **manual de operações para a coleta de dados**. O engenheiro de dados aprende a:
* **Configurar ambientes e ferramentas** para extrair dados de diversas fontes.
* **Escolher a estratégia de extração** (completa, incremental, CDC) com base no volume, frequência de mudança e necessidades de latência dos dados.
* Implementar código Python seguro e eficiente para extração, incluindo o uso de **parâmetros de vinculação** para evitar injeção de SQL.
* Lidar com a **diversidade de formatos de dados** (estruturados, semistruturados) e as particularidades de cada fonte (logs, APIs, documentos).
* Utilizar **_cloud file storage_ (S3)** como um _staging area_ para dados extraídos.
* Considerar e, se aplicável, implementar **CDC em escala** usando frameworks como Debezium para necessidades de ingestão de alto volume e baixa latência.

---

## Capítulo 5: Data Ingestion: Loading Data

**Foco da Discussão do Capítulo:**
Este capítulo completa a fase de ingestão de dados, detalhando como **carregar os dados extraídos para destinos finais**, como _cloud data warehouses_ (Amazon Redshift e Snowflake) e _data lakes_. Aborda a configuração de destinos, o uso de comandos de carga e as considerações para diferentes tipos de dados (completos, incrementais, CDC).

**Principais Conceitos do Capítulo:**
* **Objetivo da Carga:** Finalizar a ingestão de dados, movendo-os dos arquivos intermediários (CSV no S3) para o _data warehouse_ ou _data lake_.
* **Configuração do Destino (Amazon Redshift):**
    * **IAM Role:** Criação de um perfil IAM com permissão de leitura para S3 para que o Redshift possa acessar os arquivos. O ARN do perfil é usado para autorização.
    * **Conexão:** Detalhes de conexão (nome do banco de dados, usuário, senha, host, porta) armazenados em `pipeline.conf`.
    * **Melhores Práticas de Credenciais:** Recomenda-se autenticação IAM ou gerenciadores de segredos em produção para segurança.
* **Carregamento de Dados para Redshift:**
    * **Comando `COPY`:** Mecanismo principal para carregar dados do S3 para tabelas Redshift. Suporta autorização via IAM role.
    * **Ordem das Colunas:** Por padrão, a ordem dos campos no arquivo CSV deve corresponder à ordem das colunas na tabela de destino. Pode ser especificada.
    * **Carga via Python:** Usando a biblioteca `psycopg2` para executar o comando `COPY` a partir de um script Python.
    * **Cargas Incrementais vs. Completas:**
        * **Carga Completa:** `TRUNCATE` a tabela de destino antes de `COPY` para garantir apenas os dados mais recentes.
        * **Carga Incremental:** `APPEND` os novos registros. Mantém o histórico completo, permitindo análises de versões anteriores (ex: status de pedido).
* **Carregamento de Dados Extraídos de um Log CDC:**
    * Dados do CDC incluem `INSERT`, `UPDATE` e `DELETE`.
    * **Coluna `EventType`:** É necessário adicionar uma coluna para o tipo de evento na tabela de destino para lidar com registros deletados ou atualizados.
    * A lógica para usar esses eventos (ex: ignorar `delete` para estado atual) pertence à fase de transformação (Capítulo 6).
* **Configuração do Destino (Snowflake):**
    * **Integração de Armazenamento (`Storage Integration`):** Cria um objeto no Snowflake que referencia o bucket S3, usando um perfil IAM.
    * **Formato de Arquivo (`FILE FORMAT`):** Define como o Snowflake deve interpretar o arquivo de origem (ex: CSV delimitado por pipe).
    * **_Stage_:** Um local no Snowflake que aponta para um local de armazenamento externo (ex: S3 bucket).
* **Carregamento de Dados para Snowflake:**
    * **Comando `COPY INTO`:** Mecanismo para carregar um ou múltiplos arquivos de um _stage_ para uma tabela.
    * **Snowpipe:** Serviço de integração de dados do Snowflake para carregamento contínuo de dados assim que chegam ao _stage_.
* **Uso de Armazenamento de Arquivos como _Data Lake_:** S3 (ou equivalentes de outras nuvens) pode servir como um _data lake_ para armazenar dados brutos.
* **Frameworks _Open Source_ (ex: Singer):** Ferramenta com "taps" (fontes de extração) e "targets" (destinos de carga) pré-construídos, permitindo contribuições da comunidade.
* **Alternativas Comerciais (ex: Stitch, Fivetran):** Produtos hospedados em nuvem que oferecem centenas de conectores pré-construídos e orquestração de _jobs_ _no-code_.
    * **_Build vs. Buy_:** Decisão complexa. Pode-se usar uma mistura de código personalizado para ingestões de alto volume e ferramentas comerciais para conectores pré-construídos.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é uma **ferramenta prática** para o engenheiro de dados. Ele capacita o engenheiro a:
* **Implementar a etapa de carga** em _cloud data warehouses_ (Redshift, Snowflake) de forma eficiente e segura.
* **Gerenciar diferentes tipos de carga** (completa, incremental, CDC) e suas implicações para a integridade histórica dos dados.
* **Configurar acessos e formatos** (IAM roles, _storage integrations_, `FILE FORMAT`) para garantir que os dados sejam carregados corretamente.
* Avaliar a utilização de **_frameworks open source_ ou soluções comerciais** para otimizar o esforço de desenvolvimento e manutenção da ingestão, entendendo os _trade-offs_ de custo e personalização.

---

## Capítulo 6: Transforming Data

**Foco da Discussão do Capítulo:**
Detalhar a **etapa de transformação de dados** no padrão ELT, distinguindo entre **transformações não contextuais** e **modelagem de dados** com lógica de negócios. Explora os fundamentos da modelagem de dados, estratégias para lidar com diferentes padrões de ingestão (dados atualizados, incrementais, somente _append_, CDC) e as melhores práticas para reutilização de lógica.

**Principais Conceitos do Capítulo:**
* **Transformação vs. Modelagem de Dados:**
    * **Transformação:** Termo amplo (o 'T' em ELT/ETL). Pode ser simples (conversão de _timestamp_) ou complexa (criação de métricas com lógica de negócio).
    * **Modelagem de Dados:** Um tipo específico de transformação que **estrutura e define dados** (geralmente em tabelas de _data warehouse_) para otimizar análises e relatórios. Inclui **medidas** (o que medir) e **atributos** (como filtrar/agrupar).
    * **Granularidade:** O nível de detalhe de um modelo de dados (ex: diário, horário). Deve ser a menor unidade necessária, mas não menos.
* **Quando Transformar?** Transformações não contextuais (EtLT) podem ocorrer _durante_ a ingestão (extração/carga) se lidam com qualidade de dados (duplicatas) ou segurança (mascaramento) e são desconectadas da lógica de negócio. Transformações com lógica de negócio (modelagem) devem ser _após_ a ingestão.
* **Transformações Não Contextuais:**
    * **Deduplicação de Registros:** Remover duplicatas (causadas por ingestão sobreposta, erros na fonte ou _backfills_). Usar `GROUP BY` e `HAVING` para identificar, e `DISTINCT` ou funções de janela (`ROW_NUMBER() OVER(PARTITION BY ...)`) para remover.
    * **Análise de URLs:** Extrair componentes de URLs (domínio, caminho, parâmetros UTM) para colunas individuais. Geralmente feito com Python (`urllib.parse`) ou funções SQL específicas do _data warehouse_.
* **Modelagem de Dados Atualizados (_Fully Refreshed Data_):**
    * Dados que representam o **estado mais recente** da fonte (ex: truncar e recarregar). Não contêm histórico completo.
    * **Fatos e Dimensões (Kimball):** O livro introduz brevemente os conceitos de tabelas de fatos (medidas) e dimensões (atributos) para análise.
    * **Agregação:** Modelos podem ser agregados para reduzir o volume de dados e acelerar consultas, se a granularidade permitir.
    * **_Slowly Changing Dimensions (SCD) Tipo II_:** Para manter o **histórico completo** de mudanças em entidades (ex: clientes), adiciona um novo registro com validade (`ValidFrom`, `Expired`) para cada alteração. Permite análises de ponto no tempo (`point-in-time`) unindo com a data da transação.
* **Modelagem de Dados Ingeridos Incrementalmente:**
    * Dados contêm o estado atual e histórico. Desafio: como alocar eventos passados a atributos que mudaram.
    * **Alocação de Ponto no Tempo (`Point-in-Time - PIT`):** Alocar eventos (ex: pedidos) aos atributos de dimensão (ex: país do cliente) que eram válidos no momento do evento. Requer uma CTE (_Common Table Expression_) para encontrar o `MAX(LastUpdated)` da dimensão _naquele ponto no tempo_.
* **Modelagem de Dados Somente _Append_ (_Append-Only Data_):**
    * Dados imutáveis (eventos), como visualizações de página.
    * **Atualização do Modelo:** Pode ser por _refresh_ completo (truncar e recarregar) ou _refresh_ incremental (processar apenas novos registros desde o último carregamento).
    * **Desafios de _Refresh_ Incremental:** Evitar duplicação ou contagem incorreta em modelos agregados. Uma abordagem robusta envolve copiar dados antigos, inserir novos, e então consolidar.
* **Modelagem de Dados CDC (_Change Data Capture_):**
    * Dados incluem tipo de evento (INSERT, UPDATE, DELETE).
    * **Estado Atual:** Pode-se modelar o estado atual excluindo `EventType = 'delete'` e selecionando o `MAX(LastUpdated)` para cada ID.
    * **Análise de Mudanças:** Modelar a duração entre eventos (ex: tempo de _Backordered_ para _Shipped_).

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é o **coração do trabalho do engenheiro de dados para criação de valor**. Ele capacita o engenheiro a:
* **Liderar a transformação e modelagem de dados** em SQL ou Python, aplicando a lógica de negócio para criar **produtos de dados utilizáveis**.
* **Gerenciar a complexidade** de diferentes padrões de ingestão, garantindo que a **história dos dados seja preservada** (SCD Tipo II) ou que a **visão de ponto no tempo** seja precisa.
* **Otimizar modelos de dados** para granularidade e desempenho.
* Colaborar com analistas para **refatorar e reutilizar lógica** de modelos de dados, garantindo uma "fonte única de verdade" e reduzindo a manutenção.
* **Entender as capacidades de ferramentas** como dbt para gerenciamento de modelos e dependências.

---

## Capítulo 7: Orchestrating Pipelines

**Foco da Discussão do Capítulo:**
Explicar a importância da **orquestração de pipelines**, o processo de agendar e gerenciar a ordem e as dependências das tarefas. Apresenta o **Apache Airflow** como a principal plataforma de orquestração, detalhando sua configuração, construção de DAGs e recursos avançados.

**Principais Conceitos do Capítulo:**
* **Orquestração de Pipelines:** Garante que as etapas (tarefas) em um pipeline sejam executadas na **ordem correta** e que as **dependências** entre elas sejam gerenciadas adequadamente.
* **DAGs (_Directed Acyclic Graphs_):** Estruturas que definem a sequência e as dependências das tarefas em um pipeline. São **direcionados** (fluxo em uma direção) e **acíclicos** (sem loops) para garantir um caminho de execução.
* **Apache Airflow:** Uma das plataformas de orquestração de _workflow_ mais populares.
    * **Componentes do Airflow:**
        * **Banco de Dados Airflow:** Armazena metadados de execução (histórico de tarefas/DAGs, configurações). Por padrão SQLite, mas PostgreSQL ou MySQL são recomendados para escala.
        * **Servidor Web e UI:** Interface visual para monitorar e gerenciar DAGs. Inclui vistas em árvore e grafo.
        * **Scheduler:** Determina quais tarefas estão prontas para serem executadas.
        * **Executors:** Executam as tarefas. `SequentialExecutor` (padrão, não para produção), `CeleryExecutor`, `DaskExecutor`, `KubernetesExecutor` para escala.
* **Construção de DAGs Airflow:**
    * **Definição do DAG:** Em um script Python, definindo sua estrutura e dependências.
    * **Operadores (_Operators_):** Abstrações para diferentes tipos de tarefas (ex: `BashOperator` para scripts shell/Python, `PostgresOperator` para SQL em Postgres/Redshift).
    * **Propriedades do DAG:** `dag_id` (nome), `schedule_interval` (agendamento), `start_date`, `end_date`.
    * **Dependências de Tarefas:** Definidas com operadores `>>` (ex: `t1 >> t2`).
    * **_ELT Pipeline DAG_:** Exemplo de DAG que orquestra extração (BashOperator para Python), carga (BashOperator para Python) e transformação (PostgresOperator para SQL).
* **Configurações Avançadas de Orquestração:**
    * **Sensores (`ExternalTaskSensor`):** Usados para coordenar múltiplos DAGs, esperando que um DAG externo (ou uma tarefa específica dentro dele) seja concluído com sucesso.
        * `mode='reschedule'`: Libera o _worker slot_ enquanto espera.
        * `timeout`: Limite de tempo para a espera.
    * **Gerenciamento de Complexidade:** Desafios em pipelines complexos com muitas tarefas ou DAGs com dependências externas.
    * **Validação de Dados:** Adicionar tarefas de validação aos DAGs é uma boa prática (Capítulo 8).
* **Opções Gerenciadas do Airflow:** Provedores de nuvem oferecem Airflow como serviço (ex: Cloud Composer), reduzindo o esforço de _self-hosting_.
* **Outros Frameworks de Orquestração:** Luigi, Dagster, Kubeflow Pipelines, e dbt (para orquestração de modelos de dados).

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **indispensável** para o engenheiro de dados que constrói e gerencia _data pipelines_. Ele fornece o **conhecimento e as ferramentas para automatizar** o fluxo de trabalho de dados:
* **Projetar e implementar DAGs** no Airflow para orquestrar as etapas de E, L e T dos pipelines.
* Gerenciar **dependências de tarefas e DAGs** para garantir a execução correta e a consistência dos dados.
* **Configurar o ambiente Airflow** para escala e monitoramento.
* Integrar **tarefas de validação de dados**.
* Tomar decisões sobre a **escolha do orquestrador** e como integrar Airflow com outras ferramentas (ex: dbt).
* Entender a importância de **separar a lógica da orquestração** para facilitar a manutenção.

---

## Capítulo 8: Data Validation in Pipelines

**Foco da Discussão do Capítulo:**
Enfatizar a **importância crítica da validação de dados** em pipelines para garantir a qualidade e a validade dos dados. Discute os princípios de **"validar cedo, validar frequentemente"**, apresenta um **framework de validação simples** e explora exemplos práticos de testes de validação.

**Principais Conceitos do Capítulo:**
* **Necessidade de Validação:** Mesmo pipelines bem projetados podem falhar. **Dados não testados não são seguros** para uso em análises.
* **Validação Proativa:** **Assumir o pior** sobre a qualidade dos dados de origem. Engenheiros de dados devem liderar na escrita de verificações de validação não contextuais e fornecer infraestrutura para validação mais específica.
* **Fontes de Problemas de Qualidade:** Ingestão de dados inválidos de sistemas de origem (duplicatas, registros órfãos, incompletos, inconsistências de formato, erros de codificação).
* **Validar Cedo, Validar Frequentemente:** Não esperar até o final do pipeline para validar dados, para facilitar a determinação da origem dos problemas. Também não validar apenas uma vez no início.
* **Framework de Validação Simples (em Python/SQL):**
    * **Conceito:** Um script Python que executa **dois scripts SQL** e compara seus resultados numéricos com um **operador de comparação** (ex: `equals`, `greater_equals`). O teste passa ou falha.
    * **`validator.py`:** Exemplo de implementação em Python, conectando-se a um _data warehouse_ (ex: Redshift via `psycopg2`).
    * **Saída:** Retorna um código de status de saída (0 para sucesso, -1 para falha) que pode ser consumido programaticamente.
    * **Estrutura do Teste:** Dois arquivos SQL (cada um retornando um valor numérico único) e um operador de comparação. Ex: `COUNT(*)` de tabelas.
* **Uso em Airflow DAGs:**
    * Integrar o `validator.py` como uma tarefa (`BashOperator`) em um DAG Airflow.
    * `set -e;`: Adicionado ao comando Bash para **interromper a execução do DAG** se o teste de validação falhar (status de saída diferente de zero).
    * **Níveis de Severidade:** O `validator.py` pode ser estendido para aceitar um nível de severidade (`halt` para parar o DAG, `warn` para apenas notificar). A decisão depende do risco e das circunstâncias.
* **Extensão do Framework:**
    * **Notificações:** Enviar mensagens para Slack ou e-mail em caso de falha (usando _webhooks_).
    * **Execução em Lote:** Armazenar testes em um arquivo de configuração para executar múltiplos testes de uma vez.
* **Exemplos de Testes de Validação:**
    * **Registros Duplicados:** Verificar se não há duplicatas em uma tabela (comparar `COUNT(*)` de duplicatas com zero).
    * **Mudança Inesperada na Contagem de Linhas:** Usar estatísticas (ex: **z-score**) para verificar se a contagem de linhas do dia anterior está dentro de um intervalo de confiança histórico (ex: 90%).
    * **Flutuações de Valor de Métrica:** Similar ao teste de contagem de linhas, verificar se o valor de uma métrica (ex: receita total) está fora das normas históricas.
* **Contexto:** Testes de validação de métricas complexas dependem muito do contexto de negócio e são melhor definidos por analistas de dados.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **essencial para a garantia de qualidade** dos dados. O engenheiro de dados:
* **Integra validação como uma prática fundamental** em todos os pipelines.
* **Constrói ou adapta frameworks de validação** (preferencialmente com SQL e Python) para testar a qualidade dos dados em diferentes estágios do pipeline.
* **Configura a orquestração (Airflow) para executar testes** e reagir a falhas (parar o pipeline ou enviar alertas).
* Utiliza **técnicas estatísticas** (ex: z-score) para identificar anomalias nos dados.
* Colabora com analistas para **definir validações contextuais** e garantir que os dados servidos sejam confiáveis.

---

## Capítulo 9: Best Practices for Maintaining Pipelines

**Foco da Discussão do Capítulo:**
Abordar as **melhores práticas para a manutenção de pipelines de dados**, com foco em como lidar com **mudanças nos sistemas de origem** e **escalar a complexidade** crescente. Discute a importância de abstrações, contratos de dados, governança, reutilização de lógica de modelos e ferramentas como dbt.

**Principais Conceitos do Capítulo:**
* **Manutenção como Desafio:** Sistemas de origem não são estáticos; mudanças de esquema ou lógica de negócio podem quebrar pipelines ou causar imprecisões.
* **Lidando com Mudanças em Sistemas de Origem:**
    * **Abstração:** Criar uma camada de abstração entre o sistema de origem e o processo de ingestão. O proprietário da fonte deve manter ou estar ciente dessa abstração.
    * **Contratos de Dados:** Acordo escrito entre o proprietário da fonte e a equipe de ingestão, detalhando dados extraídos, método, frequência e contatos. Devem ser armazenados em locais conhecidos (ex: Git, documentação interna), formatados de forma padronizada e, se possível, integrados ao processo de desenvolvimento para sinalizar mudanças proativamente.
    * **Limites do _Schema-on-Read_:** Mudar de _schema-on-write_ para _schema-on-read_ (onde o esquema é determinado na leitura) pode adicionar flexibilidade, mas **aumenta a complexidade** na etapa de carga e exige um **catálogo de dados** e **governança de dados robusta** para definir e manter a estrutura dos dados dinamicamente.
* **Escalando a Complexidade:**
    * **Fatores Não Técnicos:** Padronizar sistemas internos e criar consciência do impacto em _pipelines_ entre engenheiros de software.
    * **Abstrações Próprias:** Se os proprietários da fonte não padronizam, o engenheiro de dados deve considerar construir suas próprias abstrações (ex: implementar CDC _streaming_ com Debezium).
    * **Reutilização da Lógica de Modelos de Dados:** No estágio de transformação, evitar a duplicação de lógica de negócio entre modelos.
        * **Princípio DRY ("Don't Repeat Yourself"):** Escrever a lógica de cálculo uma vez em um modelo base e derivar outros modelos dele. Isso garante uma **fonte única de verdade**, simplifica a manutenção (um bug corrigido em um só lugar) e melhora o desempenho do _data warehouse_.
    * **Frameworks de Desenvolvimento de Modelos de Dados (ex: dbt):** Ferramentas como dbt (_open source_, em Python) permitem definir referências entre modelos diretamente no SQL (`ref()`), criando um DAG dinâmico de dependências e garantindo a ordem de execução correta. Pode ser orquestrado por Airflow ou dbt Cloud.
        * **Modularidade:** dbt facilita a construção de modelos de dados modulares, em contraste com a complexidade dos "monolitos distribuídos" em Airflow.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é um **guia essencial para a sustentabilidade** do trabalho do engenheiro de dados. Ele capacita o engenheiro a:
* **Mitigar riscos de mudanças em sistemas de origem** através de abstrações e **contratos de dados**.
* Tomar decisões informadas sobre a abordagem de esquema (_schema-on-write_ vs. _schema-on-read_) e as **implicações de governança**.
* **Projetar arquiteturas de transformação modulares**, promovendo a **reutilização de lógica** e garantindo a consistência.
* Alavancar **ferramentas modernas (dbt)** para gerenciar a complexidade das dependências de modelos de dados, seja de forma autônoma ou integrada a orquestradores como Airflow.
* Desenvolver **habilidades não técnicas** de colaboração com equipes de software para padronizar sistemas.

---

## Capítulo 10: Measuring and Monitoring Pipeline Performance

**Foco da Discussão do Capítulo:**
Este capítulo aborda a prática essencial de **medir e monitorar o desempenho dos pipelines de dados**, tratando o desempenho do pipeline como um produto de dados em si. Descreve como coletar, armazenar, transformar e servir métricas chave de pipeline, garantindo a confiabilidade e a transparência para _stakeholders_.

**Principais Conceitos do Capítulo:**
* **Importância do Monitoramento:** Pipelines não são "defina e esqueça". Medir e monitorar o desempenho é crucial para garantir a **confiabilidade** e atender às expectativas dos _stakeholders_.
* **Métricas Chave de Pipeline:** Focar em **duas a três métricas principais** que tenham propósitos únicos, evitando excesso.
* **Preparando o _Data Warehouse_:** O _data warehouse_ é o melhor local para armazenar dados de log dos pipelines.
    * **`dag_run_history`:** Tabela para armazenar o histórico de execução de DAGs do Airflow (ID, `dag_id`, `execution_date`, `state`, `runtime`).
    * **`validation_run_history`:** Tabela para armazenar os resultados dos testes de validação.
* **Log e Ingestão de Dados de Desempenho:**
    * **Histórico de Execução de DAGs (Airflow):** Extrair dados incrementalmente do banco de dados do Airflow (ex: PostgreSQL `dag_run` table) e carregá-los para `dag_run_history` no _data warehouse_.
        * **Melhor Prática:** Idealmente, usar uma API (se robusta, como no Airflow 2.0) em vez de acessar diretamente o banco de dados da aplicação.
    * **Log no Validador de Dados:** Aprimorar o script `validator.py` (Capítulo 8) para registrar os resultados de cada teste de validação diretamente na tabela `validation_run_history` [238,  outra granularidade).
    * **Mudança no Tempo de Execução de DAGs (`DAG Runtime Change Over Time`):** Rastrear o tempo médio de execução dos DAGs ao longo do tempo para identificar riscos de dados desatualizados.
    * **Volume e Taxa de Sucesso de Testes de Validação:** Medir quantos testes de validação são executados e sua taxa de sucesso. O volume deve ser proporcional à complexidade do pipeline.
        * **`test_composite_name`:** Combinação dos nomes dos scripts e operador para agrupar resultados de testes.
* **Orquestração de um Pipeline de Desempenho:** Criar um **novo DAG Airflow** para agendar e orquestrar as etapas de ingestão e transformação dos dados de desempenho.
* **Transparência do Desempenho:** Compartilhar _insights_ com a equipe de dados e _stakeholders_ para construir confiança e senso de propriedade. Usar **ferramentas de visualização** (ex: Tableau, Looker) e **compartilhar resumos** regularmente.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo eleva a prática da engenharia de dados a um nível de **maturidade operacional**. O engenheiro de dados:
* **Desenvolve a capacidade de auto-monitoramento** para seus próprios sistemas, tratando os dados de desempenho como um produto de dados vital.
* **Projeta a infraestrutura (tabelas de log)** e os **_pipelines_ de ETL** para coletar e transformar métricas de desempenho.
* **Automatiza o monitoramento** com DAGs Airflow dedicados para desempenho.
* Torna-se responsável por **comunicar a saúde dos pipelines** de forma transparente, contribuindo para a confiança do negócio e a melhoria contínua da equipe. É fundamental para a **confiabilidade e escalabilidade de longo prazo** de toda a infraestrutura de dados.
