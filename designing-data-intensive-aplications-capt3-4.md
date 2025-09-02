Armazenamento, Codificação e Evolução de Dados
--------------------------------------------------------------------------------
Capítulo 3: Armazenamento e Recuperação (Storage and Retrieval)
Este capítulo explora como os bancos de dados organizam os dados em disco para que possam ser encontrados e acessados eficientemente.
1. Estruturas de Dados que Impulsionam seu Banco de Dados:
    ◦ Logs "Append-Only": Fundamentalmente, muitas operações de escrita em sistemas de armazenamento são registradas em um log onde os novos dados são apenas anexados ao final.
    ◦ Índices (Indexes): São estruturas de dados auxiliares que permitem buscar rapidamente registros com base no valor de um campo. Sem índices, o banco de dados precisaria escanear todos os dados, o que é muito ineficiente para grandes volumes .
        ▪ Hash Indexes: Mapeiam chaves para posições de dados, ideais para acessos de chave-valor .
        ▪ SSTables (Sorted String Tables) e LSM-Trees (Log-Structured Merge-Trees):
            • Baseados em logs "append-only" que são divididos em segmentos.
            • Compaction (Compactação): Descarta chaves duplicadas no log, mantendo apenas a atualização mais recente para cada chave.
            • Permitem a fusão eficiente de segmentos ordenados .
            • Estratégias comuns de compactação incluem size-tiered e leveled compaction, usadas em bancos de dados como LevelDB, RocksDB, HBase e Cassandra .
        ▪ B-Trees:
            • É o algoritmo de indexação mais comumente usado .
            • Os dados são organizados em blocos de tamanho fixo (páginas) .
            • Eficientes para consultas de faixa (range queries), pois as chaves são mantidas em ordem .
            • As atualizações geralmente sobrescrevem os dados "in-place", mas a maioria das implementações utiliza o princípio "copy-on-write" para garantir a confiabilidade após falhas.
            • Os bancos de dados que suportam snapshot isolation (isolamento de snapshot) usam um mecanismo de Multi-Version Concurrency Control (MVCC), mantendo várias versões de um objeto lado a lado, para que transações possam ver um estado consistente do banco de dados em diferentes pontos no tempo .
        ▪ Índices Secundários (Secondary Indexes): Estruturas adicionais para buscar registros por outros campos além da chave primária .
            • Podem ser baseados em B-trees ou LSM-trees .
            • Exemplos incluem índices de texto completo e índices geoespaciais .
2. Processamento de Transações ou Análises? (OLTP vs. OLAP):
    ◦ OLTP: Caracteriza-se por consultas rápidas que leem ou escrevem um pequeno número de registros por chave, geralmente voltado para aplicações de usuário .
    ◦ OLAP: Envolve consultas que processam grandes volumes de dados para análise, frequentemente usando funções de agregação, e são típicas de sistemas de inteligência de negócios .
    ◦ Data Warehouse (Armazém de Dados): Para grandes empresas, tornou-se comum separar os sistemas OLTP dos sistemas de análise, movendo os dados para um data warehouse .
    ◦ ETL (Extract-Transform-Load): É o processo de extrair dados de bancos de dados de origem, transformá-los para um formato mais adequado à análise e carregá-los no data warehouse .
    ◦ Star Schema (Esquema em Estrela): Um modelo de dados comum em data warehouses .
        ▪ No centro, estão as Fact Tables (Tabelas de Fatos), onde cada linha representa um evento (ex: uma venda) .
        ▪ As Dimension Tables (Tabelas de Dimensão) são referenciadas por chaves estrangeiras na tabela de fatos e descrevem o "quem, o quê, onde, quando, como e porquê" do evento (ex: detalhes do produto, data) .
3. Armazenamento Orientado a Colunas (Column-Oriented Storage):
    ◦ Ao invés de armazenar todos os valores de uma linha juntos, este método armazena todos os valores de uma coluna juntos .
    ◦ É altamente eficiente para consultas analíticas que precisam acessar apenas algumas colunas de um grande número de linhas, pois evita ler dados irrelevantes .
    ◦ Compressão: Dados em colunas tendem a ser repetitivos, o que permite alta compressão . Uma técnica eficaz é a Bitmap Encoding (Codificação por Bitmap), que representa os valores de uma coluna como uma coleção de bitmaps, um para cada valor distinto .
    ◦ Múltiplas Ordens de Classificação: Alguns bancos de dados orientados a colunas armazenam os mesmos dados ordenados de várias maneiras diferentes para otimizar diferentes tipos de consultas .
    ◦ Agregados Materializados (Materialized Aggregates) e Data Cubes (Cubos de Dados): São caches de resultados de funções de agregação (COUNT, SUM, AVG) que são frequentemente usados. Eles pré-computam esses resultados para acelerar certas consultas, mas sacrificam alguma flexibilidade.

--------------------------------------------------------------------------------
Capítulo 4: Codificação e Evolução (Encoding and Evolution)
Este capítulo aborda como os dados são serializados para armazenamento e transmitidos pela rede, e como os sistemas podem evoluir ao longo do tempo para lidar com mudanças no esquema de dados.
1. Compatibilidade de Dados:
    ◦ A evolução de aplicações e sistemas distribuídos exige compatibilidade entre diferentes versões de código e dados .
    ◦ Backward Compatibility (Compatibilidade Retroativa): Código mais novo pode ler dados escritos por código mais antigo . Isso é crucial durante uma rolling upgrade (atualização gradual), onde alguns nós rodam a versão antiga e outros a nova .
    ◦ Forward Compatibility (Compatibilidade Prospectiva): Código mais antigo pode ler dados escritos por código mais novo . É mais difícil de alcançar, mas permite que uma versão mais antiga do software continue funcionando enquanto a nova está sendo implantada.
2. Formatos para Codificação de Dados:
    ◦ Formatos Específicos da Linguagem (ex: Java Serialization):
        ▪ Geralmente são ineficientes e não portáteis para outras linguagens .
        ▪ Têm sérias vulnerabilidades de segurança ao desserializar dados não confiáveis .
        ▪ Não são bons para compatibilidade a longo prazo .
    ◦ Formatos de Texto (JSON, XML, CSV):
        ▪ Legíveis por humanos, facilitando a depuração e o entendimento .
        ▪ Ineficientes: Lento para analisar e requer mais espaço de armazenamento do que formatos binários .
        ▪ Ambiguidade: Podem ter ambiguidades na codificação de números (inteiros vs. ponto flutuante) e falta de suporte para strings binárias .
        ▪ Schema-on-Read (Esquema-on-Read): O esquema é flexível e interpretado no momento da leitura, comum em bancos de dados de documentos .
        ▪ Schema-on-Write (Esquema-on-Write): O esquema é rígido e imposto no momento da escrita, comum em bancos de dados relacionais .
    ◦ Formatos Binários com Esquema (Thrift, Protocol Buffers, Avro):
        ▪ Oferecem codificação mais compacta e parsing mais rápido .
        ▪ Thrift (Facebook) e Protocol Buffers (Google):
            • Usam field tags (tags de campo) (números) para identificar campos em vez de nomes, tornando o formato compacto .
            • Permitem adicionar novos campos (com novas tags) de forma backward compatible, e código antigo ignora tags não reconhecidas, garantindo forward compatibility .
            • Alterar tipos de dados pode ser arriscado, com perda de precisão ou truncamento .
        ▪ Apache Avro:
            • Desenvolvido para Hadoop, difere de Thrift/Protobuf .
            • O schema do escritor é sempre incluído nos dados (ou referenciado), e o schema do leitor é usado para decodificar os dados, permitindo a resolução automática do esquema .
            • Não usa tags de campo, mas sim nomes de campo para resolução, o que requer o schema do escritor para a decodificação .
            • Facilita a evolução do esquema de forma mais flexível .
3. Fluxo de Dados Através de Serviços (REST e RPC):
    ◦ Arquitetura Orientada a Serviços (SOA) ou Microservices: Decompõe uma aplicação grande em serviços menores que se comunicam pela rede .
    ◦ APIs RESTful: Tendem a ser mais simples, geralmente usando HTTP e JSON, e podem ser descritas por formatos como OpenAPI/Swagger .
    ◦ Remote Procedure Calls (RPCs):
        ▪ Tentam fazer com que uma requisição de rede se pareça com uma chamada de função local (transparência de localização) .
        ▪ Essa abstração é fundamentalmente falha, pois chamadas de rede são inerentemente imprevisíveis (atrasos, falhas parciais, perdas de pacotes), ao contrário das chamadas de função locais .
        ▪ Novas estruturas RPC (como Finagle, Rest.li, gRPC) lidam com essa imprevisibilidade usando futuros/promessas e streams .
    ◦ Frameworks de Atores Distribuídos: Integra um broker de mensagens com o modelo de ator. Para rolling upgrades, a compatibilidade de mensagens é essencial, pois versões antigas e novas podem trocar mensagens .

--------------------------------------------------------------------------------
Glossário de Conceitos (Capítulos 3 e 4 - Adições e Revisões)
Aqui estão os conceitos-chave apresentados nestes capítulos, adicionados e/ou revisados em relação ao glossário anterior:
• Abstração (Abstraction) : Uma ferramenta para remover complexidade acidental, ocultando detalhes de implementação por trás de uma interface limpa e fácil de entender.
• Aplicações Intensivas em Dados (Data-Intensive Applications) : Aplicações onde o volume, a complexidade ou a velocidade de mudança dos dados são o principal desafio, e não o poder da CPU.
• Atraso de Replicação (Replication Lag) : O tempo que leva para uma alteração de dados no líder ser aplicada e visível em um seguidor.
• B-Tree : Uma estrutura de dados de índice que organiza dados em páginas (blocos de tamanho fixo) e é otimizada para pesquisas de faixa e acesso eficiente ao disco.
• Bitmap Encoding (Codificação por Bitmap) : Uma técnica de compressão usada em armazenamento orientado a colunas, que representa os valores de uma coluna como uma coleção de bitmaps, um para cada valor distinto.
• Captura de Dados de Mudança (Change Data Capture - CDC) : Mecanismo para observar todas as alterações de dados feitas em um banco de dados e extraí-las para outros sistemas, geralmente como um fluxo de eventos.
• Carga (Load) : O que é medido no seu sistema para determinar a escalabilidade, como requisições por segundo, taxa de leituras/escritas, número de usuários ativos, etc.
• Compatibilidade (Compatibility) : A capacidade de diferentes versões de código e dados coexistirem.
    ◦ Compatibilidade Retroativa (Backward Compatibility) : Código mais novo pode ler dados escritos por código mais antigo.
    ◦ Compatibilidade Prospectiva (Forward Compatibility) : Código mais antigo pode ler dados escritos por código mais novo.
• Compactação (Compaction): O processo de combinar segmentos de log (como em SSTables) e descartar chaves duplicadas, mantendo apenas a atualização mais recente para cada chave, para economizar espaço e melhorar o desempenho de leitura.
• Complexidade Acidental (Accidental Complexity) : Complexidade que não é inerente ao problema que o software resolve, mas que surge apenas da forma como ele é implementada.
• Concorrência (Concurrency) : Múltiplas operações acontecendo ao mesmo tempo.
• Confiabilidade (Reliability) : A capacidade de um sistema continuar a funcionar corretamente (executando a função correta no nível de desempenho desejado) mesmo diante de adversidades (faltas de hardware ou software, ou até mesmo erro humano).
• Conflitos de Escrita (Write Conflicts) : Ocorre em sistemas multi-líder ou sem líder quando a mesma peça de dados é modificada concorrentemente em diferentes nós, levando a um estado inconsistente.
• Consistência Eventual (Eventual Consistency) : Uma garantia fraca onde as réplicas de dados eventualmente convergirão para o mesmo valor, mas não há garantia de quando isso acontecerá. Leituras podem retornar valores antigos.
• Consistência Linearizável (Linearizability) : Uma garantia forte de recência para leituras e escritas, fazendo com que o sistema pareça haver apenas uma cópia dos dados e todas as operações são atômicas e executadas em uma ordem bem definida.
• Consistência Monotônica (Monotonic Reads) : Uma garantia de consistência que assegura que um usuário nunca verá "o tempo voltar para trás" — ou seja, nunca lerá dados mais antigos depois de ter lido dados mais novos.
• Consistência de Leitura-do-Próprio-Escrito (Read-Your-Writes Consistency) : Uma garantia de consistência que garante que, se um usuário fizer uma escrita e depois tentar lê-la, ele sempre verá o valor mais recente que escreveu.
• Consistência de Prefixo Consistente (Consistent Prefix Reads) : Garante que leituras em um sistema distribuído vejam um prefixo consistente do histórico total de operações, preservando a ordem causal.
• Column-Oriented Storage (Armazenamento Orientado a Colunas) : Um método de armazenamento de dados onde os valores de cada coluna são agrupados, otimizado para consultas analíticas que acessam poucas colunas de muitas linhas.
• Cypher : Uma linguagem de consulta declarativa para modelos de grafo de propriedades, usada por bancos de dados como o Neo4j.
• Data Cube (Cubo de Dados) : Um agregado materializado de dados multidimensional que pré-calcula somas, contagens ou outras funções para diferentes combinações de dimensões, acelerando consultas analíticas.
• Data Locality (Localidade de Dados) : Uma otimização de desempenho que consiste em colocar várias peças de dados no mesmo lugar se elas são frequentemente necessárias ao mesmo tempo.
• Data Warehouse (Armazém de Dados) : Um banco de dados separado projetado para consultas analíticas e relatórios, contendo dados históricos e consolidados de várias fontes.
• Datalog : Uma linguagem de programação lógica declarativa para consultar dados em grafos.
• Denormalização (Denormalization) : A introdução de alguma redundância ou duplicação em um conjunto de dados normalizado para acelerar as leituras.
• Derived Data (Dados Derivados) : Um conjunto de dados que é criado a partir de outros dados através de um processo repetível, que pode ser executado novamente se necessário. Índices, caches e visões materializadas são exemplos.
• Deterministic (Determinístico) : Descreve uma função que sempre produz o mesmo resultado se receber a mesma entrada. Não pode depender de fatores imprevisíveis como números aleatórios, hora do dia ou comunicação de rede.
• Dimension Table (Tabela de Dimensão) : Em um esquema em estrela de data warehouse, uma tabela que contém atributos descritivos sobre um evento (ex: nome do produto, data).
• Distributed (Distribuído) : Rodando em vários nós conectados por uma rede. Caracterizado por falhas parciais, onde algumas partes do sistema podem estar quebradas enquanto outras ainda funcionam.
• Durability (Durabilidade) : A promessa de que, uma vez que uma transação foi confirmada com sucesso, os dados escritos não serão perdidos, mesmo em caso de falha de hardware ou do banco de dados.
• ETL (Extract-Transform-Load) : O processo de extrair dados de um banco de dados de origem, transformá-los em um formato mais adequado para consultas analíticas e carregá-los em um data warehouse ou sistema de processamento em lote.
• Evoluibilidade (Evolvability) : A facilidade com que um sistema pode ser modificado e adaptado a requisitos futuros e casos de uso não previstos.
• Fact Table (Tabela de Fatos) : Em um esquema em estrela de data warehouse, a tabela central que armazena medições ou eventos, geralmente com chaves estrangeiras para tabelas de dimensão.
• Failover (Failover) : O processo de transferir a função de líder de um nó para outro em sistemas replicados com um único líder.
• Faltas (Faults) : Um componente do sistema que se desvia de sua especificação (ex: um disco com defeito, um bug de software, erro humano).
• Faltas de Hardware (Hardware Faults): Falhas em componentes físicos como discos, RAM, ou problemas de energia.
• Faltas de Software (Software Faults) : Bugs, interações ruins entre processos, ou recursos compartilhados.
• Falhas (Failures) : Quando o sistema como um todo para de fornecer o serviço exigido ao usuário, geralmente como resultado de uma falta não tolerada.
• Fencing (Fencing Tokens): Técnica para garantir que um nó que erroneamente acredita ser o líder não cause danos ao sistema, permitindo escritas apenas na ordem crescente de tokens.
• Field Tags (Tags de Campo) : Números usados em protocolos de serialização binária (como Thrift e Protocol Buffers) como identificadores compactos para campos, em vez de seus nomes.
• Follower (Seguidor) : Uma réplica que não aceita escritas diretamente dos clientes, mas apenas processa as alterações de dados que recebe de um líder.
• Grafo de Propriedades (Property Graph Model) : Modelo de dados de grafo onde vértices e arestas podem ter propriedades e rótulos para descrever relacionamentos.
• Hot Spots (Hot Spots) : Partições com uma carga desproporcionalmente alta de requisições ou dados, levando a um desequilíbrio de carga.
• Índice (Index): Uma estrutura de dados auxiliar que permite localizar rapidamente registros com base no valor de um campo.
• Índice Secundário (Secondary Index) : Um índice adicional mantido ao lado do armazenamento de dados primário, que permite buscar eficientemente registros que correspondem a uma condição específica.
    ◦ Índice Secundário Particionado por Documento (Document-Partitioned Secondary Index / Local Index) : Onde os índices secundários são armazenados na mesma partição que a chave primária e o valor do documento.
    ◦ Índice Secundário Particionado por Termo (Term-Partitioned Secondary Index / Global Index) : Onde os índices secundários são particionados separadamente, usando os valores indexados.
• Isolation (Isolamento): No contexto de transações, descreve o grau em que transações em execução concorrente podem interferir umas com as outras.
    ◦ Read Committed (Leitura Confirmada) : Nível de isolamento que previne leituras sujas e escritas sujas, mas permite leitura de desvio (read skew) e leituras não repetitivas.
    ◦ Snapshot Isolation (Isolamento de Snapshot) : Nível de isolamento que previne leituras de desvio (read skew) e leituras não repetitivas, usando Multi-Version Concurrency Control (MVCC).
• Joins (Junções) : Operação para combinar registros que têm algo em comum, tipicamente uma referência de uma tabela para outra.
• Last Write Wins (LWW - Última Escrita Vence) : Um algoritmo de resolução de conflitos que descarta todas as escritas conflitantes, exceto aquela com o timestamp mais recente. Pode levar à perda de dados.
• Leader (Líder) : Em um sistema replicado, a réplica designada que é permitida a fazer alterações.
• Leituras Monotônicas (Monotonic Reads): Uma garantia de consistência que assegura que um usuário nunca verá "o tempo voltar para trás" — ou seja, nunca lerá dados mais antigos depois de ter lido dados mais novos.
• Linguagem de Consulta Declarativa (Declarative Query Language) : Você especifica o que quer obter, não como o sistema deve alcançá-lo (ex: SQL, Cypher).
• Linguagem de Consulta Imperativa (Imperative Query Language) : Você especifica como o sistema deve executar uma operação, passo a passo.
• Log (Log): Um arquivo "append-only" (apenas escrita no final) para armazenar dados. Usado em WAL, logs de replicação e logs de eventos.
    ◦ Log de Write-Ahead (WAL - Write-Ahead Log): Um arquivo "append-only" usado para tornar um motor de armazenamento resiliente a falhas, garantindo que as modificações sejam registradas antes de serem aplicadas às estruturas de dados principais.
    ◦ Logical Log (Log Lógico) : Um formato de log que descreve as alterações de dados em um nível de abstração mais alto (ex: inserção de linha X, atualização de coluna Y), desacoplado dos detalhes do motor de armazenamento.
• LSM-Tree (Log-Structured Merge-Tree) : Uma estrutura de dados de armazenamento que otimiza o desempenho de escrita agrupando escritas em memória e depois as escrevendo sequencialmente em discos, mantendo a ordenação.
• Manutenibilidade (Maintainability) : A facilidade com que diversas pessoas (engenharia e operações) podem trabalhar produtivamente no sistema ao longo do tempo.
• Materialize (Materializar): Realizar uma computação eagermente e escrever seu resultado, em vez de calculá-lo sob demanda. Ex: visões materializadas, data cubes, estado intermediário em processamento em lote.
• MapReduce : Um modelo de programação para processamento em lote (batch processing) de grandes conjuntos de dados em clusters distribuídos, popularizado pelo Hadoop.
• Modelo de Documento (Document Model) : Um modelo de dados onde os dados são armazenados em documentos autocontidos, como JSON ou XML, que podem conter estruturas aninhadas.
• Modelo Relacional (Relational Model) : Um modelo de dados onde os dados são organizados em tabelas (relações), consistindo de linhas (tuplas) e colunas (atributos), com relacionamentos definidos por chaves.
• Modelo Triple-Store (Triple-Store Model) : Um modelo de dados onde as informações são armazenadas como triplas (sujeito, predicado, objeto), frequentemente associado ao Resource Description Framework (RDF) da Web Semântica.
• Monitoramento (Monitoring / Telemetria) : Coleta e análise de métricas de desempenho e taxas de erro para observar a saúde e o comportamento de um sistema.
• Multi-Version Concurrency Control (MVCC) : Uma técnica de controle de concorrência que mantém várias versões de um objeto, permitindo que leitores acessem versões consistentes sem bloquear escritores, e vice-versa.
• Mismatch Objeto-Relacional (Object-Relational Mismatch) : A dificuldade em traduzir objetos de uma linguagem de programação orientada a objetos para o modelo relacional de um banco de dados, e vice-versa.
• Normalização (Normalization) : O processo de organizar as colunas e tabelas de um banco de dados relacional para minimizar a redundância de dados.
• Nonrepeatable Read (Leitura Não Repetível) : Uma anomalia de transação onde uma transação lê diferentes valores para o mesmo objeto em leituras sucessivas.
• OLAP (Online Analytic Processing) : Padrão de acesso caracterizado por consultas que processam grandes volumes de dados para análise, frequentemente usando funções de agregação.
• OLTP (Online Transaction Processing) : Padrão de acesso caracterizado por consultas rápidas que leem ou escrevem um pequeno número de registros, geralmente indexados por chave.
• Operabilidade (Operability) : A facilidade para as equipes de operações manterem o sistema funcionando sem problemas (inclui monitoramento, ferramentas, gerenciamento de configuração).
• Page (Página) : Um bloco de tamanho fixo de dados em um B-tree, a unidade básica de armazenamento e acesso.
• Parâmetros de Carga (Load Parameters) : Métricas que descrevem a carga de um sistema, como taxa de requisições, proporção de leitura/escrita, e número de usuários.
• Particionamento (Partitioning / Sharding) : O processo de dividir um grande conjunto de dados (um banco de dados) em subconjuntos menores e independentes chamados partições, que podem ser armazenadas em diferentes nós.
    ◦ Particionamento por Faixa de Chaves (Partitioning by Key Range) : Atribuir um intervalo contínuo de chaves a cada partição.
    ◦ Particionamento por Hash de Chave (Partitioning by Hash of Key) : Usar uma função hash para determinar qual partição uma chave pertence, visando distribuir os dados uniformemente.
• Percentile (Percentil) : Uma forma de medir a distribuição de valores contando quantos valores estão acima ou abaixo de um determinado limite.
• Qualidade do Serviço (Quality of Service - QoS) : Garantias sobre o desempenho e disponibilidade de um serviço.
• Quórum (Quorum) : Um número mínimo de réplicas que devem confirmar uma operação (leitura ou escrita) para que ela seja considerada bem-sucedida em um sistema distribuído sem líder.
• Rebalanceamento (Rebalancing) : O processo de mover partições entre nós em um cluster quando nós são adicionados ou removidos, ou quando a distribuição de dados se torna desigual.
• Redundância (Redundancy) : Duplicação de componentes ou dados para aumentar a tolerância a falhas.
• Relacionamento "Happened-Before" (Happened-Before Relationship) : Uma relação causal entre duas operações onde uma operação deve ter ocorrido antes da outra, ou uma depende da outra.
• Remote Procedure Call (RPC) : Um modelo de comunicação em sistemas distribuídos que tenta fazer uma chamada a um serviço remoto parecer uma chamada de função local.
• Replicação (Replication) : Manter cópias dos mesmos dados em vários nós (réplicas) para alta disponibilidade, baixa latência ou escalabilidade de leitura.
    ◦ Replicação Assíncrona (Asynchronous Replication) : O líder processa uma escrita e replica para os seguidores sem esperar a confirmação de todos os seguidores; mais rápida, mas com risco de perda de dados.
    ◦ Replicação Baseada em Líder (Leader-Based Replication) : Uma arquitetura de replicação onde um nó (o líder) aceita todas as escritas e as propaga para outros nós (os seguidores).
    ◦ Replicação Multi-Líder (Multi-Leader Replication) : Uma arquitetura de replicação onde vários nós podem aceitar escritas de clientes, replicando-as de forma assíncrona para outros líderes.
    ◦ Replicação Sem Líder (Leaderless Replication) : Uma arquitetura de replicação onde qualquer réplica pode aceitar escritas diretamente dos clientes, sem um único líder coordenador.
• Replicação Síncrona (Synchronous Replication) : Uma escrita é considerada bem-sucedida somente após todos os seguidores confirmarem que a receberam. Maior durabilidade, mas menor performance.
• Resolução de Conflitos (Conflict Resolution) : O processo de determinar qual versão de dados prevalece quando ocorrem escritas conflitantes em um sistema distribuído.
• Rolling Upgrade (Atualização Gradual) : O processo de implantar uma nova versão de software em um cluster, atualizando um pequeno número de nós por vez, para evitar tempo de inatividade.
• Scatter/Gather : Um padrão de consulta em bancos de dados particionados onde a consulta é enviada para todas as partições, e os resultados são coletados e combinados.
• Schema (Esquema) : Uma descrição da estrutura dos dados, incluindo seus campos e tipos de dados.
    ◦ Schema Evolution (Evolução de Esquema) : O processo de adaptar um schema de dados ao longo do tempo para atender a novos requisitos ou mudanças nas necessidades da aplicação.
    ◦ Schema Resolution (Resolução de Esquema) : O processo, como no Avro, de reconciliar as diferenças entre o esquema do escritor e o esquema do leitor para traduzir os dados.
    ◦ Schema do Escritor (Writer's Schema) : O esquema usado para codificar os dados.
    ◦ Schema do Leitor (Reader's Schema) : O esquema esperado para os dados durante a decodificação.
    ◦ Schema-on-Read (Esquema-on-Read) : O esquema é implícito na estrutura dos dados e é interpretado no momento da leitura (comum em bancos de dados de documentos "schemaless").
    ◦ Schema-on-Write (Esquema-on-Write) : O esquema é explícito e imposto no momento da escrita (comum em bancos de dados relacionais).
• Secondary Index (Índice Secundário) : Veja "Índice Secundário".
• Serialization (Serialização / Encoding) : A conversão de um objeto ou estrutura de dados na memória em uma sequência de bytes para armazenamento ou transmissão.
    ◦ Deserialization (Desserialização / Decoding) : O processo inverso da serialização, convertendo uma sequência de bytes de volta em uma estrutura de dados na memória.
• Service-Oriented Architecture (SOA) : Um estilo arquitetural que estrutura uma aplicação como uma coleção de serviços, que são acoplados de forma loose e comunicam-se pela rede.
• Simplicidade (Simplicity) : A qualidade de ser fácil de entender para novos engenheiros, frequentemente alcançada removendo a complexidade acidental.
• Sistemas Distribuídos (Distributed Systems) : Sistemas que operam em múltiplas máquinas conectadas por uma rede.
• Sistemas Elásticos (Elastic Systems) : Sistemas que podem automaticamente adicionar ou remover recursos de computação conforme a carga muda.
• Sistemas Shared-Nothing (Shared-Nothing Systems) : Uma arquitetura onde cada máquina tem sua própria CPU, memória e discos, e a comunicação entre elas ocorre exclusivamente através da rede.
• Skew (Desequilíbrio) : Distribuição desigual de dados ou carga de trabalho entre as partições ou nós. Pode se referir a um desequilíbrio de carga (hot spots) ou a uma anomalia de tempo em transações (read skew, write skew).
• Snapshot Isolation (Isolamento de Snapshot) : Veja "Isolation".
• Split Brain : Uma situação de falha em um sistema distribuído onde dois (ou mais) nós acreditam erroneamente serem o líder, o que pode levar a inconsistências e perda de dados.
• SPARQL : Uma linguagem de consulta declarativa para modelos de dados RDF (triple-stores).
• SSTable (Sorted String Table) : Um formato de arquivo onde os dados são armazenados como um log "append-only" e as entradas são ordenadas por chave.
• Star Schema (Esquema em Estrela) : Um modelo de dados dimensional comum em data warehouses, otimizado para consultas analíticas.
• Telemetria (Telemetry) : Dados de monitoramento coletados de um sistema para entender seu desempenho e diagnosticar problemas.
• Tolerância a Falhas (Fault-tolerance) : A capacidade de um sistema se recuperar automaticamente se algo der errado (ex: falha de máquina, link de rede).
• Transação (Transaction) : Uma sequência de operações de leitura e escrita que é tratada como uma única unidade lógica, garantindo atomicidade, consistência, isolamento e durabilidade (ACID).
• Vértices (Vertices) : Em modelos de grafo, são os nós ou entidades.
• Vetores de Versão (Version Vectors) : Um mecanismo para detectar escritas concorrentes em sistemas distribuídos sem líder, rastreando as versões dos dados que cada nó "viu".
• Write Skew (Desvio de Escrita) : Uma anomalia de transação em que duas transações leem um conjunto de dados e, com base nisso, tomam decisões que se tornam inconsistentes quando ambas as transações são confirmadas.
• XA Transactions (Transações XA) : Um padrão para implementar o protocolo de Two-Phase Commit (2PC) em tecnologias heterogêneas, usado para transações distribuídas.
