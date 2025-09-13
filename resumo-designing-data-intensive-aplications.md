# Resumo do livro Designing Data-Intensive Applications

## Capítulo 1: Aplicações Confiáveis, Escaláveis e Manuteníveis

**Foco da Discussão do Capítulo:**
Define os **três pilares essenciais** para o design de qualquer sistema de dados moderno: confiabilidade, escalabilidade e manutenibilidade. Aborda os princípios para construir sistemas robustos e adaptáveis.

**Principais Conceitos do Capítulo:**
* **Confiabilidade:** Sistema funciona corretamente mesmo com **falhas** (hardware, software, humanas), prevenindo que falhas causem paradas. Prioriza **tolerância a falhas** sobre prevenção.
* **Escalabilidade:** Capacidade do sistema de lidar com o **crescimento da carga** (dados, tráfego, complexidade) eficientemente. Envolve a **medição de carga e desempenho** (ex: percentis de tempo de resposta).
* **Manutenibilidade:** Facilidade de **colaborar** no sistema ao longo do tempo (bugs, novas funcionalidades, adaptações). Inclui **operabilidade** (facilitar a operação), **simplicidade** (reduzir complexidade acidental via abstrações) e **evoluibilidade** (facilitar mudanças futuras).

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Um engenheiro de dados aplica esses princípios em **todas as fases de design e implementação**. É a base para **selecionar tecnologias**, **definir arquiteturas** (ex: _shared-nothing_ para escalabilidade) e **diagnosticar problemas**, garantindo que as soluções sejam duradouras e eficazes.

---

## Capítulo 2: Modelos de Dados e Linguagens de Consulta

**Foco da Discussão do Capítulo:**
Explora as diferentes **formas de estruturar e interagir com os dados**. Compara modelos relacionais, de documentos e gráficos, e suas linguagens de consulta, destacando quando cada um é mais adequado.

**Principais Conceitos do Capítulo:**
* **Modelos de Dados:**
    * **Relacional:** Dados organizados em tabelas com chaves estrangeiras; forte para **relacionamentos complexos** (_joins_).
    * **Documento:** Dados aninhados em documentos (ex: JSON); ideal para **estruturas hierárquicas** e esquema flexível (_schema-on-read_). Desafios com _joins_ complexos.
    * **Gráfico:** Dados representados como vértices (entidades) e arestas (conexões); flexível para **relações interconectadas**.
* **Linguagens de Consulta:**
    * **Declarativas (ex: SQL, Cypher, SPARQL):** O que buscar, não como. Otimizador de consultas decide a melhor forma de executar.
    * **Imperativas:** Especifica o passo a passo para buscar.
    * **MapReduce:** Estrutura para processamento paralelo, dividindo tarefas `map` e `reduce`.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O engenheiro de dados utiliza este conhecimento para **escolher o modelo de dados e o banco de dados mais apropriado** para cada caso de uso da aplicação. Isso impacta a flexibilidade do esquema, o desempenho de consultas e a facilidade de evolução do sistema.

---

## Capítulo 3: Armazenamento e Recuperação

**Foco da Discussão do Capítulo:**
Detalha como os bancos de dados **armazenam fisicamente os dados no disco** e os recuperam de forma eficiente. Compara as principais estruturas de índice e motores de armazenamento.

**Principais Conceitos do Capítulo:**
* **Motores de Armazenamento:**
    * **Log-structured (ex: SSTables, LSM-Trees):** Baseado em logs **append-only**. Eficiente para escritas sequenciais e compactação em segundo plano. Utiliza índices em memória esparsos.
    * **Page-oriented (ex: B-Trees):** Divide o banco de dados em **páginas de tamanho fixo** e as sobrescreve. Otimizado para leituras e atualizações no local. Usa Write-Ahead Log (WAL) para durabilidade.
* **Índices:** Estruturas auxiliares para **acelerar a busca** de dados, como um catálogo de biblioteca.
    * **Índices Hash:** Mapeia chaves para offsets de dados em memória.
    * **Índices Secundários:** Permitem buscar por colunas não-primárias. Podem ser _clustered_ (dados armazenados no índice) ou _nonclustered_ (referência aos dados).
* **Bancos de Dados em Memória (_In-Memory_):** Mantêm dados na RAM para alto desempenho, com mecanismos de durabilidade (logs ou replicação).
* **OLTP vs. OLAP:** Distinção entre sistemas para **transações online** (muitas pequenas leituras/escritas por chave) e **análises online** (consultas complexas sobre grandes volumes). **Armazenamento colunar** é ideal para OLAP, otimizando leitura e compressão de colunas.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Um engenheiro de dados usa este conhecimento para **entender o desempenho de I/O**, **otimizar consultas** através da escolha de índices e modelos de armazenamento, e **dimensionar recursos** de armazenamento e memória. Fundamental para projetar sistemas de _data warehouse_ e _data lake_.

---

## Capítulo 4: Codificação e Evolução

**Foco da Discussão do Capítulo:**
Aborda como os dados são **serializados (codificados)** para armazenamento ou transmissão em rede, e como os **esquemas de dados podem evoluir** ao longo do tempo.

**Principais Conceitos do Capítulo:**
* **Formatos de Codificação:**
    * **Textuais (ex: JSON, XML):** Legíveis por humanos, mas ineficientes em espaço e tempo.
    * **Binários (ex: Thrift, Protocol Buffers, Avro):** Mais compactos e eficientes, mas exigem um esquema para interpretação.
* **Evolução de Esquemas:** A capacidade do sistema de lidar com **mudanças na estrutura dos dados**.
    * **Compatibilidade:** **_Backward compatibility_** (código novo lê dados antigos) e **_forward compatibility_** (código antigo lê dados novos) são cruciais.
    * **Abordagens:** Formatos como Thrift/Protocol Buffers usam **tags de campo** numéricas; Avro usa **esquemas do escritor e leitor** para resolver diferenças.
    * **Migração de Dados:** Reescrever dados para um novo esquema é caro; muitos DBs permitem alterações simples sem reescrita.
* **Fluxo de Dados em Serviços:**
    * **REST e RPC:** Padrões para comunicação cliente-servidor (chamadas de procedimento remoto).
    * **Mensageria Assíncrona:** Usa _brokers_ de mensagens para comunicação desacoplada e unidirecional.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O engenheiro de dados define **formatos de dados** para armazenamento e comunicação, garantindo que sejam eficientes e suportem a **evolução dos requisitos** do negócio sem _downtime_. Isso é vital para sistemas distribuídos e _pipelines_ de dados.

---

## Capítulo 5: Replicação

**Foco da Discussão do Capítulo:**
Explora a **manutenção de cópias de dados em múltiplas máquinas** para garantir alta disponibilidade, baixa latência e escalabilidade de leitura. O principal desafio é a gestão das mudanças nos dados replicados.

**Principais Conceitos do Capítulo:**
* **Motivos para Replicação:** Tolerância a falhas (disponibilidade), redução de latência (proximidade com o usuário) e escalabilidade de leitura (dividir carga de leitura).
* **Tipos de Replicação:**
    * **_Single-Leader_:** Um nó (_leader_) recebe todas as escritas e replica para os _followers_. Pode ser síncrona (garante durabilidade, mas latência) ou assíncrona (rápida, mas risco de perda de dados). Requer **_failover_** em caso de falha do líder.
    * **_Multi-Leader_:** Permite escritas em múltiplos líderes, melhor para ambientes distribuídos. Exige **resolução de conflitos** quando a mesma informação é modificada concorrentemente.
    * **_Leaderless_ (Estilo Dynamo):** Qualquer réplica aceita escritas de clientes. Utiliza **quoruns** (N, W, R) para durabilidade e visibilidade. Conflitos resolvidos via _last write wins_ ou fusão de versões no aplicativo.
* **Problemas de _Replication Lag_:** Atraso na propagação de escritas em sistemas assíncronos, levando a inconsistências. Necessidade de garantias como **_read-after-write_** (ler suas próprias escritas) e **_monotonic reads_** (não ver o tempo voltar).
* **Logs de Replicação:** Utilizam logs de instruções (SQL), WALs ou logs lógicos para propagar as mudanças.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Um engenheiro de dados seleciona o modelo de replicação com base nos **requisitos de disponibilidade, latência e consistência** da aplicação. Compreender as trade-offs é crucial para garantir a **tolerância a falhas** e o comportamento esperado dos dados em ambientes distribuídos.

---

## Capítulo 6: Particionamento

**Foco da Discussão do Capítulo:**
Explora como **dividir grandes conjuntos de dados** em partes menores (partições ou _shards_) para escalabilidade e balanceamento de carga. Discute as estratégias de particionamento e como elas interagem com os índices.

**Principais Conceitos do Capítulo:**
* **Objetivo do Particionamento:** Distribuir dados e carga de consultas uniformemente entre múltiplos nós, evitando _hot spots_ (nós sobrecarregados).
* **Estratégias de Particionamento de Dados Chave-Valor:**
    * **Por Intervalo de Chaves:** Atribui faixas contínuas de chaves a cada partição. Bom para _range scans_, mas pode gerar _hot spots_ se a carga não for uniforme.
    * **Por Hash da Chave:** Usa o hash da chave para distribuir uniformemente. Dificulta _range scans_, mas distribui a carga de forma mais justa.
* **Particionamento e Índices Secundários:**
    * **Índices Particionados por Documento (_Local_):** Cada partição mantém seu próprio índice secundário. Escritas eficientes, mas leituras exigem **_scatter/gather_** (consultar todas as partições).
    * **Índices Particionados por Termo (_Global_):** Um índice global é particionado separadamente por termo. Leituras mais eficientes, mas escritas mais complexas e lentas (podem afetar múltiplas partições do índice).
* **Rebalanceamento de Partições:** Mover partições entre nós para **redistribuir a carga** à medida que o cluster muda (adicionar/remover nós ou dados). Pode ser com número fixo ou dinâmico de partições.
* **Roteamento de Requisições:** Como o cliente descobre em qual nó o dado reside.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O engenheiro de dados define a **estratégia de particionamento** para garantir a escalabilidade horizontal de um sistema de dados. A escolha impacta o desempenho de leituras e escritas, a complexidade de manutenção e a capacidade de lidar com o crescimento dos dados.

---

## Capítulo 7: Transações

**Foco da Discussão do Capítulo:**
Detalha as **transações como um mecanismo para simplificar a manipulação de falhas e concorrência** em bancos de dados. Explora as propriedades ACID e os diferentes níveis de isolamento.

**Principais Conceitos do Capítulo:**
* **Conceito de Transação:** Agrupamento de operações de leitura/escrita em uma **unidade lógica única** que **commit** (todas sucedem) ou **abort** (todas são desfeitas).
* **Propriedades ACID:**
    * **Atomicidade:** "Tudo ou nada".
    * **Consistência:** Garante que o banco de dados permanece em um **"estado bom"** (válido de acordo com as regras da aplicação).
    * **Isolamento:** Transações concorrentes **não interferem** umas nas outras, agindo como se fossem sequenciais.
    * **Durabilidade:** Dados confirmados **não são perdidos**, mesmo com falhas de hardware.
* **Níveis de Isolamento (Fraquezas e Consequências):**
    * **_Read Committed_:** Evita **_dirty reads_** (ler dados não confirmados) e **_dirty writes_** (sobrescrever dados não confirmados).
    * **_Snapshot Isolation_ (ou _Repeatable Read_):** Cada transação lê um _snapshot_ consistente do BD, evitando leituras inconsistentes de longo prazo (não-repeatable reads). Implementado com **MVCC** (Multi-Version Concurrency Control).
    * **_Serializable Snapshot Isolation_ (SSI):** Isolamento forte (serializabilidade) com bom desempenho, detecta conflitos de serialização.
* **Problemas de Concorrência:**
    * **_Lost Update_:** Duas transações modificam o mesmo valor, e uma mudança é perdida. Prevenido por operações atômicas ou _compare-and-set_.
    * **_Write Skew_:** Transações tomam decisões baseadas em um estado que se torna inválido quando as transações são combinadas.
    * **_Phantoms_:** Linhas que aparecem/desaparecem em consultas de intervalo.
* **Serializabilidade:** Nível mais forte de isolamento, garantindo que a execução concorrente é equivalente a alguma ordem serial. Pode ser via **execução serial real**, **_Two-Phase Locking (2PL)_** ou **SSI**.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Essencial para projetar sistemas que manipulam dados críticos com **integridade e consistência**. O engenheiro de dados deve escolher o **nível de isolamento** adequado para balancear correção, desempenho e complexidade, especialmente em sistemas distribuídos.

---

## Capítulo 8: Os Problemas com Sistemas Distribuídos

**Foco da Discussão do Capítulo:**
Revela a **natureza intrinsecamente complexa e caótica dos sistemas distribuídos**, destacando os desafios e falhas que não existem em sistemas de máquina única.

**Principais Conceitos do Capítulo:**
* **Falhas Parciais:** Sistemas distribuídos podem ter **partes funcionando e outras falhando** inesperadamente, ao contrário de um sistema único que "funciona ou falha".
* **Redes Não Confiáveis:** A rede é inerentemente imprevisível: pacotes podem ser **perdidos, atrasados, duplicados ou reordenados**. A detecção de falhas é incerta. O **argumento _end-to-end_** sugere que a confiabilidade deve ser verificada nas extremidades do sistema.
* **Relógios Não Confiáveis:** Relógios em máquinas diferentes **não são perfeitamente sincronizados** e podem sofrer desvios (_drift_). Isso dificulta a ordenação causal de eventos. **_Process Pauses_** (ex: coleta de lixo) também afetam a percepção do tempo.
* **Conhecimento, Verdade e Mentiras:** É difícil ter uma "verdade" global em sistemas distribuídos. Nós podem ter **visões inconsistentes** do estado do sistema (_split brain_). Mecanismos como **_fencing tokens_** ajudam a prevenir que nós falhos causem corrupção.
* **Falhas Bizantinas:** Comportamento arbitrário e malicioso de nós (mentir, enviar dados contraditórios), o que a maioria dos sistemas de dados não tolera.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é um **alerta** para o engenheiro de dados. Ele molda o **raciocínio sobre garantias e trade-offs**, forçando a projetar sistemas com a premissa de que **tudo pode falhar**. É fundamental para entender os limites das abstrações e escolher ferramentas que lidem com essas realidades.

---

## Capítulo 9: Consistência e Consenso

**Foco da Discussão do Capítulo:**
Aborda o desafio de fazer com que **múltiplos nós concordem sobre o estado dos dados** e as **garantias de consistência** que um sistema distribuído pode oferecer, bem como o problema fundamental do consenso.

**Principais Conceitos do Capítulo:**
* **Garantias de Consistência:**
    * **Consistência Eventual:** Réplicas eventualmente convergem para o mesmo valor, sem garantia de tempo.
    * **Linearizabilidade:** O sistema se comporta como se houvesse **uma única cópia atômica dos dados**, e todas as operações são ordenadas numa única linha do tempo. Garante a **visibilidade imediata** de escritas.
    * **Causalidade:** Preserva a ordem de eventos onde um **"acontece antes"** do outro. A **consistência causal** é mais forte que eventual, mas mais eficiente que linearizabilidade.
* **Teorema CAP:** Durante uma **partição de rede**, um sistema deve escolher entre **linearizabilidade (Consistência)** e **Disponibilidade total**, não podendo ter ambos.
* **Consenso:** Problema fundamental de fazer **múltiplos nós concordarem sobre um único valor** (ex: eleição de líder).
    * **_Two-Phase Commit (2PC)_:** Protocolo para _commit_ atômico em transações distribuídas, mas caro e pode **amplificar falhas**.
    * **Algoritmos de Consenso (Paxos, Raft, Zab):** Projetados para garantir acordo mesmo com falhas (geralmente via _total order broadcast_).
    * **_Total Order Broadcast_:** Garante que mensagens são entregues de forma confiável na **mesma ordem** a todos os nós. Essencial para sistemas linearizáveis e eleição de líderes.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O engenheiro de dados lida com a **complexidade da consistência e do consenso** para garantir a **integridade dos dados** em ambientes distribuídos. Escolhe entre os modelos de consistência (_eventual_, _linearizável_, _causal_) e algoritmos de consenso para balancear o desempenho, a disponibilidade e a segurança dos dados.

---

## Capítulo 10: Processamento em Lote

**Foco da Discussão do Capítulo:**
Examina o **processamento de grandes volumes de dados de entrada delimitados** para produzir novas saídas. Introduz os princípios e ferramentas para o processamento de dados em larga escala.

**Principais Conceitos do Capítulo:**
* **Processamento em Lote:** Trabalhos que leem um **conjunto finito de dados** de entrada e produzem um novo conjunto de dados de saída, sem modificar a entrada.
* **Filosofia Unix:** Ferramentas pequenas e especializadas que se conectam via _pipes_, com entradas imutáveis e saídas que podem ser entradas para outras ferramentas. Facilita a composição e a depuração.
* **MapReduce:** Modelo de programação para processamento em lote em **clusters distribuídos**.
    * **Mappers:** Processam blocos de entrada em paralelo, emitindo pares chave-valor.
    * **Reducers:** Agrupam pares por chave e agregam resultados após a fase de **_shuffle_** (particionamento e ordenação).
    * **Tolerância a Falhas:** O _framework_ gerencia a reexecução de tarefas falhas.
    * **Sistema de Arquivos Distribuído (ex: HDFS):** Armazena dados de entrada e saída de forma durável e replicada.
* **_Joins_ em Lote:** Podem ser feitos no lado do _reduce_ (_sort-merge join_) ou do _map_ (_broadcast hash join_).
* **Além do MapReduce:** **Motores de fluxo de dados** (ex: Spark, Flink) otimizam o processamento em lote, reduzindo a materialização de estados intermediários no disco e permitindo _pipelines_ mais eficientes.
* **Saídas de Processos em Lote:** Dados derivados são geralmente **imutáveis**, facilitando a recuperação de bugs e a auditoria (_human fault tolerance_).
* **Processamento de Grafos Iterativo (ex: Pregel):** Modelos para algoritmos de grafos que exigem iterações e troca de mensagens entre vértices.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Um engenheiro de dados projeta e implementa **_pipelines_ de dados para ETL**, análises e construção de índices/modelos usando processamento em lote. Entende as trade-offs entre ferramentas e a importância da **imutabilidade para robustez e recuperabilidade**.

---

## Capítulo 11: Processamento de Fluxos

**Foco da Discussão do Capítulo:**
Explora o **processamento de dados contínuos e ilimitados (streams)** em tempo real, contrastando com o processamento em lote de dados finitos. Aborda a captura de mudanças e a manutenção de consistência em sistemas heterogêneos.

**Principais Conceitos do Capítulo:**
* **Processamento de Fluxos:** Lida com dados que chegam constantemente (_unbounded data_). A saída também é contínua.
* **Mensageria Baseada em Logs (ex: Apache Kafka):** Trata streams como **logs append-only e particionados**. Cada mensagem tem um _offset_ sequencial. Permite que múltiplos consumidores leiam de forma independente e reprocessam dados facilmente.
* **Captura de Dados de Mudança (_Change Data Capture - CDC_):** Técnica para replicar **todas as mudanças** de um banco de dados de registro para outros sistemas em tempo real. Crucial para manter a consistência entre diferentes sistemas.
* **_Event Sourcing_:** Abordagem onde todas as **alterações de estado são armazenadas como uma sequência de eventos imutáveis**. Simplifica auditoria, depuração e reconstrução do estado. Distingue **comandos** (pedidos) de **eventos** (fatos).
* **Imutabilidade:** Dados, uma vez escritos, **não são modificados**. Princípio central para processamento de lote e fluxo, facilitando recuperação de falhas. **Compactação de logs** retém apenas a versão mais recente dos registros.
* **_Command Query Responsibility Segregation (CQRS)_:** Separa as preocupações de escrita (comandos, eventos) das de leitura (consultas, views) para otimização e flexibilidade.
* **Tolerância a Falhas em Fluxos:** Usam micro-lotes, _checkpointing_, transações e operações **idempotentes** para garantir processamento _exactly-once_ (ou _effectively-once_), mesmo com falhas.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O engenheiro de dados projeta **_pipelines_ de dados em tempo real** para integração de dados, CDC, processamento de eventos e construção de _views_ derivadas. Utiliza logs de eventos e o conceito de imutabilidade para construir sistemas **tolerantes a falhas e auditáveis**, otimizando para baixa latência.

---

## Capítulo 12: O Futuro dos Sistemas de Dados

**Foco da Discussão do Capítulo:**
Reflete sobre como **integrar diversos sistemas de dados** e construir aplicações mais **robustas, corretas e evoluíveis**. Propõe abordagens para o design de sistemas de dados futuros.

**Principais Conceitos do Capítulo:**
* **Integração de Dados:** Reconhece que **nenhuma ferramenta única** satisfaz todas as necessidades. A integração é feita combinando **sistemas de registro** (_systems of record_) com **dados derivados** (índices, caches, views materializadas, modelos de ML).
* **_Unbundling Databases_ (_Desagregação_):** Separação das funcionalidades de um BD tradicional em **componentes especializados** que podem ser combinados. Segue a **filosofia Unix** (ferramentas pequenas e bem definidas).
    * **Sincronização de Escritas:** Logs de eventos assíncronos com escritas idempotentes são mais robustos que transações distribuídas heterogêneas.
* **Design de Aplicações Orientado a Fluxo de Dados:** A arquitetura "_database inside-out_" onde o código da aplicação é uma **função de derivação**, transformando um _dataset_ em outro. Promove imutabilidade e facilita reprocessamento.
* **Observação do Estado Derivado:** Dados derivados são o ponto de encontro entre os caminhos de escrita e leitura. Índices e caches movem o trabalho de processamento do caminho de leitura para o de escrita (pré-computação).
* **Busca pela Correção:** Construir sistemas **confiáveis e corretos** com semânticas bem definidas é primordial.
    * **Argumento _End-to-End_:** Algumas garantias devem ser verificadas e aplicadas nas **extremidades do sistema**, não apenas em componentes intermediários.
    * **Integridade e Pontualidade (_Timeliness_):** Integridade (ausência de corrupção) e pontualidade (dados atualizados) são cruciais, alcançadas com logs de eventos, funções determinísticas e IDs de requisição idempotentes.
    * **Unicidade em Mensageria Baseada em Logs:** O log garante ordem total, permitindo decisões determinísticas e escaláveis sobre unicidade.
* **Responsabilidade Ética:** A importância de construir sistemas que **tratam dados pessoais com humanidade e respeito**, dado o impacto da tecnologia na sociedade.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo orienta o engenheiro de dados a adotar uma **visão holística** da arquitetura de dados. Incentiva a **composição de sistemas especializados** e o uso de _dataflows_ para integração e evolução, priorizando a **correção _end-to-end_** e a ética no manuseio de dados. É a reflexão sobre o **propósito e impacto** do trabalho do engenheiro de dados.
