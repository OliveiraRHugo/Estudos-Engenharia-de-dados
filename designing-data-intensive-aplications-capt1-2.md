# Fundamentos de Sistemas de Dados: Confiabilidade, Escalabilidade e Modelos 

---

## Capítulo 1: Aplicações Confiáveis, Escaláveis e Manuteníveis

Todo sistema com alta dependência de dados deve possuir **três qualidades essenciais** :

1.  **Confiabilidade:** 
    *   **O que significa?** Um sistema confiável continua a **funcionar corretamente** mesmo quando as coisas dão errado. Ele não apenas evita falhas, mas também se recupera delas.
    *   **Faltas vs. Falhas:** Uma **falta** é quando um componente individual para de funcionar (um disco que falha, um bug de software) [2]. Uma **falha** é quando o sistema, como um todo, para de fornecer o serviço ao usuário. Nosso objetivo é **construir mecanismos de tolerância a falhas para que as faltas não causem falhas**.
    *   **Tipos de faltas:**
        *   **Faltas de Hardware:** Discos pifam, RAM dá problema, falta de energia. Em grandes datacenters, isso acontece **o tempo todo**. A solução comum é a **redundância**, como RAID para discos ou fontes de alimentação duplas para servidores.
        *   **Faltas de Software:** Bugs em cascata, interações ruins entre serviços, falhas em recursos compartilhados. São mais difíceis de lidar porque geralmente são **sistemáticas** (um input ruim sempre causa o mesmo bug).
        *   **Erros Humanos:** As pessoas cometem erros. Pode ser uma configuração errada, um código mal implantado. Para mitigar isso, precisamos de **boas práticas**, como testes abrangentes, recuperação rápida de erros (rollback), monitoramento detalhado (telemetria) e treinamento.
2.  **Escalabilidade:**
    *   **O que significa?** É a capacidade do sistema de **lidar com o crescimento** (em volume de dados, volume de tráfego ou complexidade) de uma forma razoável.
    *   **Pensando na carga:** Para discutir escalabilidade, precisamos definir **parâmetros de carga**. Por exemplo, quantas requisições por segundo, qual a proporção de leituras/escritas, quantos usuários ativos, o "fan-out" (quantos seguidores um post alcança) .
    *   **Estratégias para escalar:**
        *   **Escalar Verticalmente (Scale Up):** Comprar uma máquina mais potente .
        *   **Escalar Horizontalmente (Scale Out):** Distribuir a carga em várias máquinas (um **sistema distribuído**) .
    *   **Sistemas Elásticos:** Aqueles que adicionam recursos automaticamente quando a carga aumenta .
    *   **Importante:** Não existe uma "receita mágica de escalabilidade". A arquitetura será **altamente específica para a sua aplicação**, dependendo dos tipos de problemas que você precisa resolver (volume de leituras, escritas, dados, requisitos de latência, etc.) . Mas todas são construídas a partir de **blocos de construção comuns** .
3.  **Manutenibilidade:** 
    *   **O que significa?** O software deve ser projetado de forma que **muitas pessoas diferentes** (engenheiros, operações) possam trabalhar nele produtivamente ao longo do tempo . A maioria do custo do software está na manutenção, não no desenvolvimento inicial .
    *   **Princípios de Design:**
        *   **Operabilidade:** Facilite a vida da equipe de operações para manter o sistema funcionando. Isso inclui bom monitoramento, ferramentas de gerenciamento, automação de tarefas rotineiras, e documentação clara .
        *   **Simplicidade:** Torne o sistema **fácil de entender** para novos engenheiros, removendo o máximo de **complexidade acidental** possível . Complexidade acidental não é inerente ao problema que o software resolve, mas surge da implementação . Uma boa **abstração** é a melhor ferramenta para isso .
        *   **Evoluibilidade:** Facilite as **mudanças futuras** no sistema para se adaptar a novos requisitos ou casos de uso não previstos. Também conhecida como extensibilidade ou modificabilidade . Sistemas simples e bem abstratos são mais fáceis de evoluir .

---

## Capítulo 2: Modelos de Dados e Linguagens de Consulta

Agora que entendemos os pilares de um bom sistema, vamos mergulhar no coração de como os dados são organizados e acessados. O modelo de dados que você escolhe tem um impacto **profundo** no que sua aplicação pode e não pode fazer .

1.  **O Coração dos Dados:**
    *   Seu software no aplicativo usa **estruturas de dados** (objetos, listas, maps) .
    *   Para armazená-los, você os expressa em um **modelo de dados** (tabelas relacionais, documentos JSON, grafos) .
    *   O banco de dados então representa esse modelo em **bytes** na memória ou disco .
    *   A escolha do modelo é fundamental! 
2.  **Modelo Relacional vs. Modelo de Documento:**
    *   **Modelo Relacional (SQL):** 
        *   Dados organizados em **tabelas, linhas e colunas** .
        *   Relacionamentos entre dados são feitos com **chaves estrangeiras** e **joins** .
        *   **Mismatch Objeto-Relacional:** A famosa "incompatibilidade" entre objetos de código e tabelas relacionais, que ORMs tentam mitigar .
        *   **Normalização:** Um esquema **normalizado** evita duplicação de dados, geralmente levando a mais tabelas e mais joins .
        *   **Esquema-on-Write:** O esquema é **rígido e explícito**, imposto no momento da escrita . Alterações de esquema podem ser dolorosas, especialmente em bancos de dados como MySQL em tabelas grandes .
    *   **Modelo de Documento (NoSQL):** 
        *   Dados armazenados em **documentos autocontidos** (ex: JSON, XML) .
        *   Bom para dados com **estrutura de árvore** (relacionamentos um-para-muitos), onde o documento inteiro é frequentemente lido de uma vez .
        *   **Joins:** Geralmente têm suporte **fraco ou nenhum** para joins, o que significa que o código da sua aplicação pode ter que fazer múltiplas consultas para emular um join .
        *   **Desnormalização:** É comum ter dados duplicados dentro de documentos para acelerar leituras .
        *   **Localidade de Dados:** Armazenar um documento inteiro como uma string contínua pode ser um ganho de performance se você sempre precisar de grandes partes dele .
        *   **Esquema-on-Read:** O esquema é **implícito** e flexível. Você pode escrever dados com estruturas diferentes, e o esquema é inferido ou validado no momento da leitura . Isso facilita a evolução do esquema .
    *   **Convergência:** Bancos de dados relacionais modernos estão adicionando suporte a tipos de dados JSON/XML, e bancos de dados de documentos estão adicionando funcionalidades de consulta mais avançadas, borrando as linhas entre eles .
3.  **Modelos de Dados Semelhantes a Grafos:** 
    *   Para dados **altamente conectados**, os modelos de grafo brilham .
    *   **Grafo de Propriedades:** 
        *   Dados representados como **vértices (nós)** e **arestas (conexões)** .
        *   Cada **vértice** tem um ID único e um conjunto de **propriedades** (pares chave-valor) .
        *   Cada **aresta** tem um ID único, um **vértice de início (cauda)**, um **vértice de fim (cabeça)**, um **rótulo** que descreve o relacionamento, e também pode ter **propriedades** .
        *   Muito flexível: qualquer vértice pode se conectar a qualquer outro, e as arestas podem ter rótulos diferentes para diferentes tipos de relacionamentos .
    *   **Modelo Triple-Store (RDF):** 
        *   Dados representados como **triplas** (sujeito, predicado, objeto) .
        *   Frequentemente associado à **Web Semântica** .
4.  **Linguagens de Consulta:** 
    *   **Linguagens Declarativas (SQL, Cypher, SPARQL, Datalog):** 
        *   Você descreve **o que** você quer obter (quais condições os resultados devem atender, como devem ser transformados), mas **não como** o banco de dados deve fazer isso .
        *   Vantagens: Mais concisas, fáceis de trabalhar e, o mais importante, permitem que o **otimizador de consulta do banco de dados** decida a melhor forma de executar a consulta, sem exigir mudanças no seu código quando o banco de dados melhora sua performance .
    *   **Linguagens Imperativas (código em linguagens de programação, mappers/reducers no MapReduce):** 
        *   Você descreve **como** o banco de dados deve realizar a operação, passo a passo .
        *   Desvantagens: Menos espaço para o banco de dados otimizar a consulta .
    *   **MapReduce como Linguagem de Consulta:** Pode ser usado, mas é mais de baixo nível, exigindo a escrita de funções coordenadas (mapper e reducer) .

---

## Glossário de Conceitos (Capítulos 1 e 2)

Aqui estão os conceitos-chave apresentados nos dois primeiros capítulos, em formato de glossário, para você guardar debaixo do braço:

*   **Abstração (Abstraction)** : Uma ferramenta para remover complexidade acidental, ocultando detalhes de implementação por trás de uma interface limpa e fácil de entender.
*   **Aplicações Intensivas em Dados (Data-Intensive Applications)** : Aplicações onde o volume, a complexidade ou a velocidade de mudança dos dados são o principal desafio, e não o poder da CPU.
*   **Atraso de Replicação (Replication Lag)** : O tempo que leva para uma alteração de dados no líder ser aplicada e visível em um seguidor.
*   **Captura de Dados de Mudança (Change Data Capture - CDC)** : Mecanismo para enviar o conteúdo de um banco de dados para um sistema externo, geralmente através de um log de alterações, para análise offline ou construção de índices customizados.
*   **Carga (Load)** : O que é medido no seu sistema para determinar a escalabilidade, como requisições por segundo, taxa de leituras/escritas, número de usuários ativos, etc.
*   **Compatibilidade (Compatibility)** : A capacidade de diferentes versões de código e dados coexistirem.
    *   **Compatibilidade Retroativa (Backward Compatibility)** : Código mais novo pode ler dados escritos por código mais antigo.
    *   **Compatibilidade Prospectiva (Forward Compatibility)** : Código mais antigo pode ler dados escritos por código mais novo.
*   **Complexidade Acidental (Accidental Complexity)** : Complexidade que não é inerente ao problema que o software resolve, mas que surge apenas da forma como ele é implementado.
*   **Concorrência (Concurrency)** : Múltiplas operações acontecendo ao mesmo tempo.
*   **Confiabilidade (Reliability)** [1]: A capacidade de um sistema continuar a funcionar corretamente (executando a função correta no nível de desempenho desejado) mesmo diante de adversidades (faltas de hardware ou software, ou até mesmo erro humano).
*   **Conflitos de Escrita (Write Conflicts)** : Ocorre em sistemas multi-líder ou sem líder quando a mesma peça de dados é modificada concorrentemente em diferentes nós, levando a um estado inconsistente.
*   **Consistência Eventual (Eventual Consistency)** : Uma garantia fraca onde as réplicas de dados eventualmente convergirão para o mesmo valor, mas não há garantia de quando isso acontecerá. Leituras podem retornar valores antigos.
*   **Consistência Linearizável (Linearizability)** : Uma garantia forte de recência para leituras e escritas, fazendo com que o sistema pareça haver apenas uma cópia dos dados e todas as operações são atômicas e executadas em uma ordem bem definida.
*   **Consistência Monotônica (Monotonic Reads)** : Uma garantia de consistência que assegura que um usuário nunca verá "o tempo voltar para trás" — ou seja, nunca lerá dados mais antigos depois de ter lido dados mais novos.
*   **Consistência de Leitura-do-Próprio-Escrito (Read-Your-Writes Consistency)** : Uma garantia de consistência que garante que, se um usuário fizer uma escrita e depois tentar lê-la, ele sempre verá o valor mais recente que escreveu.
*   **Consistência de Prefixo Consistente (Consistent Prefix Reads)** : Garante que leituras em um sistema distribuído vejam um prefixo consistente do histórico total de operações, preservando a ordem causal.
*   **Cypher** : Uma linguagem de consulta declarativa para modelos de grafo de propriedades, usada por bancos de dados como o Neo4j.
*   **Datalog** : Uma linguagem de programação lógica declarativa para consultar dados em grafos.
*   **Desnormalização (Denormalization)** : A introdução de alguma redundância ou duplicação em um conjunto de dados normalizado para acelerar as leituras.
*   **Durabilidade (Durability)** : A promessa de que, uma vez que uma transação foi confirmada com sucesso, os dados escritos não serão perdidos, mesmo em caso de falha de hardware ou do banco de dados.
*   **Engenharia de Recursos (Feature Engineering)** : Processo de usar o conhecimento do domínio para extrair recursos (características) de dados brutos que tornam os algoritmos de aprendizado de máquina mais eficazes.
*   **Esquema (Schema)** : Uma descrição da estrutura dos dados, incluindo seus campos e tipos de dados.
    *   **Esquema do Escritor (Writer's Schema)** : O esquema usado para codificar os dados.
    *   **Esquema do Leitor (Reader's Schema)** : O esquema esperado para os dados durante a decodificação.
    *   **Esquema-on-Read (Schema-on-Read)** : O esquema é implícito na estrutura dos dados e é interpretado no momento da leitura (comum em bancos de dados de documentos "schemaless").
    *   **Esquema-on-Write (Schema-on-Write)** : O esquema é explícito e imposto no momento da escrita (comum em bancos de dados relacionais).
    *   **Resolução de Esquema (Schema Resolution)** : O processo, como no Avro, de reconciliar as diferenças entre o esquema do escritor e o esquema do leitor para traduzir os dados.
*   **Escalabilidade (Scalability)**: A capacidade de um sistema lidar com o aumento do volume de dados, volume de tráfego ou complexidade.
*   **Evoluibilidade (Evolvability)** : A facilidade com que um sistema pode ser modificado e adaptado a requisitos futuros e casos de uso não previstos.
*   **Failover (Failover)** : O processo de transferir a função de líder de um nó para outro em sistemas replicados com um único líder.
*   **Faltas (Faults)**: Um componente do sistema que se desvia de sua especificação (ex: um disco com defeito, um bug de software, erro humano).
*   **Faltas de Hardware (Hardware Faults)**: Falhas em componentes físicos como discos, RAM, ou problemas de energia.
*   **Faltas de Software (Software Faults)**: Bugs, interações ruins entre processos, ou recursos compartilhados.
*   **Falhas (Failures)**: Quando o sistema como um todo para de fornecer o serviço exigido ao usuário, geralmente como resultado de uma falta não tolerada.
*   **Fencing (Fencing Tokens)** : Técnica para garantir que um nó que erroneamente acredita ser o líder não cause danos ao sistema, permitindo escritas apenas na ordem crescente de tokens.
*   **Follower (Seguidor)** : Uma réplica que não aceita escritas diretamente dos clientes, mas apenas processa as alterações de dados que recebe de um líder.
*   **Grafo de Propriedades (Property Graph Model)** : Modelo de dados de grafo onde vértices e arestas podem ter propriedades e rótulos para descrever relacionamentos.
*   **Hot Spots (Hot Spots)** : Partições com uma carga desproporcionalmente alta de requisições ou dados, levando a um desequilíbrio de carga.
*   **Índice (Index)** : Uma estrutura de dados auxiliar que permite localizar rapidamente registros com base no valor de um campo.
    *   **Índice Secundário (Secondary Index)** : Um índice adicional mantido ao lado do armazenamento de dados primário, que permite buscar eficientemente registros que correspondem a uma condição específica.
    *   **Índice Secundário Particionado por Documento (Document-Partitioned Secondary Index / Local Index)** : Onde os índices secundários são armazenados na mesma partição que a chave primária e o valor do documento.
    *   **Índice Secundário Particionado por Termo (Term-Partitioned Secondary Index / Global Index)** : Onde os índices secundários são particionados separadamente, usando os valores indexados.
*   **Joins (Junções)** : Operação para combinar registros que têm algo em comum, tipicamente uma referência de uma tabela para outra.
*   **Last Write Wins (LWW - Última Escrita Vence)** : Um algoritmo de resolução de conflitos que descarta todas as escritas conflitantes, exceto aquela com o timestamp mais recente. Pode levar à perda de dados.
*   **Leader (Líder)** : Em um sistema replicado, a réplica designada que é permitida a fazer alterações.
*   **Leituras Monotônicas (Monotonic Reads)** : Uma garantia de consistência que assegura que um usuário nunca verá "o tempo voltar para trás" — ou seja, nunca lerá dados mais antigos depois de ter lido dados mais novos.
*   **Linguagem de Consulta Declarativa (Declarative Query Language)** : Você especifica *o que* quer obter, não *como* o sistema deve alcançá-lo (ex: SQL, Cypher).
*   **Linguagem de Consulta Imperativa (Imperative Query Language)** : Você especifica *como* o sistema deve executar uma operação, passo a passo.
*   **Localidade de Dados (Data Locality)** : Uma otimização de desempenho que consiste em colocar várias peças de dados no mesmo lugar se elas forem frequentemente necessárias ao mesmo tempo.
*   **Log de Write-Ahead (WAL - Write-Ahead Log)** : Um arquivo "append-only" (apenas escrita no final) usado para tornar um motor de armazenamento resiliente a falhas, garantindo que as modificações sejam registradas antes de serem aplicadas às estruturas de dados principais.

*   **Log Lógico (Logical Log)** : Um formato de log que descreve as alterações de dados em um nível de abstração mais alto (ex: inserção de linha X, atualização de coluna Y), desacoplado dos detalhes do motor de armazenamento.
*   **Manutenibilidade (Maintainability)** : A facilidade com que diversas pessoas (engenharia e operações) podem trabalhar produtivamente no sistema ao longo do tempo.
*   **MapReduce** : Um modelo de programação para processamento em lote (batch processing) de grandes conjuntos de dados em clusters distribuídos, popularizado pelo Hadoop.
*   **Modelo de Documento (Document Model)** : Um modelo de dados onde os dados são armazenados em documentos autocontidos, como JSON ou XML, que podem conter estruturas aninhadas.
*   **Modelo Relacional (Relational Model)** : Um modelo de dados onde os dados são organizados em tabelas (relações), consistindo de linhas (tuplas) e colunas (atributos), com relacionamentos definidos por chaves.
*   **Modelo Triple-Store (Triple-Store Model)** : Um modelo de dados onde as informações são armazenadas como triplas (sujeito, predicado, objeto), frequentemente associado ao Resource Description Framework (RDF) da Web Semântica.
*   **Mismatch Objeto-Relacional (Object-Relational Mismatch)** : A dificuldade em traduzir objetos de uma linguagem de programação orientada a objetos para o modelo relacional de um banco de dados, e vice-versa.
*   **Monitoramento (Monitoring / Telemetria)** : Coleta e análise de métricas de desempenho e taxas de erro para observar a saúde e o comportamento de um sistema.
*   **Normalização (Normalization)** : O processo de organizar as colunas e tabelas de um banco de dados relacional para minimizar a redundância de dados.
*   **Operabilidade (Operability)** : A facilidade para as equipes de operações manterem o sistema funcionando sem problemas (inclui monitoramento, ferramentas, gerenciamento de configuração).
*   **Particionamento (Partitioning / Sharding)** : O processo de dividir um grande conjunto de dados (um banco de dados) em subconjuntos menores e independentes chamados partições, que podem ser armazenadas em diferentes nós.
    *   **Particionamento por Faixa de Chaves (Partitioning by Key Range)** : Atribuir um intervalo contínuo de chaves a cada partição.
    *   **Particionamento por Hash de Chave (Partitioning by Hash of Key)** : Usar uma função hash para determinar qual partição uma chave pertence, visando distribuir os dados uniformemente.
*   **Parâmetros de Carga (Load Parameters)** : Métricas que descrevem a carga de um sistema, como taxa de requisições, proporção de leitura/escrita, e número de usuários.
*   **Qualidade do Serviço (Quality of Service - QoS)** : Garantias sobre o desempenho e disponibilidade de um serviço.
*   **Quórum (Quorum)** : Um número mínimo de réplicas que devem confirmar uma operação (leitura ou escrita) para que ela seja considerada bem-sucedida em um sistema distribuído sem líder.
*   **Rebalanceamento (Rebalancing)** : O processo de mover partições entre nós em um cluster quando nós são adicionados ou removidos, ou quando a distribuição de dados se torna desigual.
*   **Redundância (Redundancy)**: Duplicação de componentes ou dados para aumentar a tolerância a falhas.
*   **Relacionamento "Happened-Before" (Happened-Before Relationship)** : Uma relação causal entre duas operações onde uma operação deve ter ocorrido antes da outra, ou uma depende da outra.
*   **Replicação (Replication)** : Manter cópias dos mesmos dados em vários nós (réplicas) para alta disponibilidade, baixa latência ou escalabilidade de leitura.
    *   **Replicação Assíncrona (Asynchronous Replication)** : O líder processa uma escrita e replica para os seguidores sem esperar a confirmação de todos os seguidores; mais rápida, mas com risco de perda de dados.
    *   **Replicação Baseada em Líder (Leader-Based Replication)** : Uma arquitetura de replicação onde um nó (o líder) aceita todas as escritas e as propaga para outros nós (os seguidores).
    *   **Replicação Multi-Líder (Multi-Leader Replication)** : Uma arquitetura de replicação onde vários nós podem aceitar escritas de clientes, replicando-as de forma assíncrona para outros líderes.
    *   **Replicação Sem Líder (Leaderless Replication)** : Uma arquitetura de replicação onde qualquer réplica pode aceitar escritas diretamente dos clientes, sem um único líder coordenador.
    *   **Replicação Síncrona (Synchronous Replication)** : Uma escrita é considerada bem-sucedida somente após todos os seguidores confirmarem que a receberam. Maior durabilidade, mas menor performance.
*   **Resolução de Conflitos (Conflict Resolution)** : O processo de determinar qual versão de dados prevalece quando ocorrem escritas conflitantes em um sistema distribuído.
*   **Requisitos Funcionais (Functional Requirements)** : O que a aplicação deve fazer (ex: armazenar dados, permitir buscas).
*   **Requisitos Não-Funcionais (Nonfunctional Requirements)** : Propriedades gerais do sistema (ex: segurança, confiabilidade, escalabilidade, manutenibilidade).
*   **Scatter/Gather** : Um padrão de consulta em bancos de dados particionados onde a consulta é enviada para todas as partições, e os resultados são coletados e combinados.
*   **Serialização (Serialization / Encoding)** : A conversão de um objeto ou estrutura de dados na memória em uma sequência de bytes para armazenamento ou transmissão.
*   **Desserialização (Deserialization / Decoding)** : O processo inverso da serialização, convertendo uma sequência de bytes de volta em uma estrutura de dados na memória.
*   **Simplicidade (Simplicity)** : A qualidade de ser fácil de entender para novos engenheiros, frequentemente alcançada removendo a complexidade acidental.
*   **Sistemas Distribuídos (Distributed Systems)** : Sistemas que operam em múltiplas máquinas conectadas por uma rede.
*   **Sistemas Elásticos (Elastic Systems)** : Sistemas que podem automaticamente adicionar ou remover recursos de computação conforme a carga muda.
*   **Sistemas Shared-Nothing (Shared-Nothing Systems)** : Uma arquitetura onde cada máquina tem sua própria CPU, memória e discos, e a comunicação entre elas ocorre exclusivamente através da rede.
*   **Skew (Desequilíbrio)** : Distribuição desigual de dados ou carga de trabalho entre as partições ou nós.
*   **Split Brain** : Uma situação de falha em um sistema distribuído onde dois (ou mais) nós acreditam erroneamente serem o líder, o que pode levar a inconsistências e perda de dados.
*   **SPARQL** : Uma linguagem de consulta declarativa para modelos de dados RDF (triple-stores).
*   **Tags de Campo (Field Tags)** : Números usados em protocolos de serialização binária (como Thrift e Protocol Buffers) como identificadores compactos para campos, em vez de seus nomes.
*   **Telemetria (Telemetry)** : Dados de monitoramento coletados de um sistema para entender seu desempenho e diagnosticar problemas.
*   **Tolerância a Falhas (Fault-tolerance)**: A capacidade de um sistema se recuperar automaticamente se algo der errado (ex: falha de máquina, link de rede).
*   **Transação (Transaction)** : Uma sequência de operações de leitura e escrita que é tratada como uma única unidade lógica, garantindo atomicidade, consistência, isolamento e durabilidade (ACID).
*   **Triple-Store Model (Modelo de Triple-Store)** : Veja "Modelo Triple-Store".
*   **Vértices (Vertices)** : Em modelos de grafo, são os nós ou entidades.
*   **Vetores de Versão (Version Vectors)** : Um mecanismo para detectar escritas concorrentes em sistemas distribuídos sem líder, rastreando as versões dos dados que cada nó "viu".

---
