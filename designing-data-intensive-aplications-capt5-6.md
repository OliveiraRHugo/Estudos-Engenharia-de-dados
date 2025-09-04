# Capítulo 5 Replicação (Replication)
Este capítulo aborda a importância da replicação de dados em sistemas distribuídos, as diferentes abordagens e os desafios associados

## 1 Propósitos da Replicação
* Alta Disponibilidade Permite que o sistema continue funcionando mesmo se uma ou várias máquinas falharem

* Baixa Latência Mantém os dados geograficamente próximos aos usuários, reduzindo o tempo de resposta

* Escalabilidade de Leitura Permite que mais máquinas atendam a consultas de leitura, aumentando a taxa de transferência

## 2 Replicação Baseada em Líder (Leader-Based Replication)
* Um nó é designado como o líder, aceitando todas as escritas

* Os outros nós são seguidores, que recebem uma cópia das alterações de dados do líder

* Clientes podem ler do líder ou de qualquer seguidor

* Replicação Síncrona vs Assíncrona
    * Síncrona O líder espera a confirmação do seguidor antes de reportar sucesso, garantindo maior durabilidade, mas com menor performance

    * Assíncrona O líder envia a mensagem e não espera uma resposta do seguidor; é mais rápida, mas com risco de perda de dados se o líder falhar antes que as mudanças cheguem aos seguidores

* Implementação de Logs de Replicação
    * O líder registra todas as escritas em um log "append-only" e o envia aos seguidores

    * Logs lógicos descrevem as alterações em um nível de abstração mais alto, facilitando a compatibilidade entre diferentes versões de software ou motores de armazenamento e úteis para a Captura de Dados de Mudança (Change Data Capture - CDC)

* Problemas com o Atraso de Replicação
    * Atraso de Replicação (Replication Lag) O tempo que leva para uma alteração de dados no líder ser aplicada e visível em um seguidor Esse atraso pode levar a inconsistências para a aplicação

    * Leitura das Próprias Escritas (Reading Your Own Writes) Em replicação assíncrona, um usuário pode não ver imediatamente sua própria escrita se a leitura for direcionada a um seguidor que ainda não recebeu a atualização

    * Leituras Monotônicas (Monotonic Reads) Garante que um usuário nunca verá os dados "voltarem no tempo", ou seja, nunca lerá uma versão mais antiga dos dados depois de ter lido uma versão mais nova

    * Consistência de Prefixo Consistente (Consistent Prefix Reads) Garante que leituras em um sistema distribuído vejam um prefixo consistente do histórico total de operações, preservando a ordem causal

## 3 Replicação Multi-Líder (Multi-Leader Replication)
* Vários nós podem aceitar escritas de clientes

* Frequentemente utilizada em ambientes com múltiplos data centers para reduzir a latência de escrita

* Resolução de Conflitos (Conflict Resolution) Como não há uma ordem global de escritas, conflitos podem ocorrer
Estratégias como "Last Write Wins" (LWW) (última escrita vence) são comuns, mas podem levar à perda de dados Algoritmos mais avançados como transformação operacional e CRDTs (Conflict-Free Replicated Data Types) visam resolver conflitos automaticamente

## 4 Replicação Sem Líder (Leaderless Replication)
* Qualquer réplica pode aceitar escritas diretamente dos clientes, sem um líder coordenador

* Inspirada no sistema Dynamo da Amazon, sendo utilizada em bancos de dados como Riak, Cassandra e Voldemort

* Mecanismos de Consistência
    * Reparo de Leitura (Read Repair) Clientes detectam e corrigem valores obsoletos em réplicas durante as leituras

    * Anti-entropia (Anti-Entropy) Processos em segundo plano sincronizam dados ausentes entre réplicas

* Quóruns (Quorums) Um número mínimo (w) de réplicas deve confirmar uma escrita e um número mínimo (r) de réplicas deve responder a uma leitura para que a operação seja considerada bem-sucedida
Geralmente, w + r > n (onde n é o número total de réplicas) para garantir que uma leitura veja pelo menos uma réplica atualizada

* Vetores de Versão (Version Vectors) Mecanismos para detectar e conciliar escritas concorrentes em sistemas sem líder, rastreando as versões dos dados

* Consistência Eventual (Eventual Consistency) Uma garantia fraca onde as réplicas de dados eventualmente convergirão para o mesmo valor, mas não há garantia de quando isso acontecerá


--------------------------------------------------------------------------------
# Capítulo 6 Particionamento (Partitioning)

Este capítulo detalha como grandes conjuntos de dados são divididos e distribuídos por vários nós para alcançar escalabilidade, os métodos de particionamento e como lidar com a evolução do cluster

## 1 Fundamentos do Particionamento

* Também conhecido como sharding
* Cada registro pertence a exatamente uma partição
* Objetivo principal Escalabilidade, distribuindo o volume de dados e a carga de consultas entre múltiplos processadores e discos em um cluster "shared-nothing" (sem recursos compartilhados)
  
## 2 Particionamento e Replicação

* Frequentemente combinados cópias de cada partição são armazenadas em vários nós para tolerância a falhas

* Um nó pode ser líder para algumas partições e seguidor para outras

## 3 Particionamento de Dados Chave-Valor

* Particionamento por Faixa de Chaves (Partitioning by Key Range)
    * Atribui um intervalo contínuo de chaves (ex* A-M, N-Z) a cada partição

    * As chaves são mantidas em ordem, o que é eficiente para consultas de faixa (range queries)

    * Desvantagem* Pode criar hot spots se todas as escritas se concentrarem em uma única faixa (ex* dados de séries temporais com carimbos de data/hora crescentes)

* Particionamento por Hash da Chave (Partitioning by Hash of Key)
    * Aplica uma função hash na chave para determinar a partição, visando distribuir os dados de forma mais uniforme

    * Evita hot spots causados por chaves sequenciais

    * Desvantagem Perde a capacidade de realizar consultas de faixa eficientes, pois a ordenação é perdida pelo hash

* Cargas de Trabalho Desbalanceadas e Hot Spots (Skewed Workloads and Hot Spots)
    * Ocorre quando algumas partições recebem uma carga desproporcional de requisições ou dados, como no "problema da celebridade" (ex* tweets de Justin Bieber)

## 4 Particionamento e Índices Secundários (Partitioning and Secondary Indexes)

* A introdução de índices secundários complica o particionamento, pois as consultas podem precisar acessar dados de múltiplas partições

* Índice Secundário Particionado por Documento (Document-Partitioned Secondary Index / Índice Local)*
    * Cada partição mantém seu próprio índice secundário, cobrindo apenas os documentos nessa partição

    * Escritas são locais a uma única partição

    * Leituras em índices secundários exigem uma abordagem de "scatter/gather", onde a consulta é enviada a todas as partições, e os resultados são combinados, o que pode ser caro

* Índice Secundário Particionado por Termo (Term-Partitioned Secondary Index / Índice Global)*
    * Um índice global é construído para cobrir dados de todas as partições, mas ele próprio é particionado por termo (ex* cor*vermelho) [208 dados)

* Número Fixo de Partições Criar muito mais partições do que nós e atribuir várias partições a cada nó Novos nós "roubam" partições de nós existentes

* Particionamento Dinâmico* Para bancos de dados particionados por faixa de chaves, as partições são divididas quando excedem um tamanho configurado e mescladas quando encolhem
Partições Proporcionais*

# Glossário
* Atraso de Replicação (Replication Lag): O tempo que leva para uma alteração de dados no líder ser aplicada e visível em um seguidor
* Captura de Dados de Mudança (Change Data Capture - CDC): Mecanismo para observar todas as alterações de dados feitas em um banco de dados e extraí-las para outros sistemas, geralmente como um fluxo de eventos
* Conflitos de Escrita (Write Conflicts): Ocorre em sistemas multi-líder ou sem líder quando a mesma peça de dados é modificada concorrentemente em diferentes nós, levando a um estado inconsistente
* Consistência Eventual (Eventual Consistency): Uma garantia fraca onde as réplicas de dados eventualmente convergirão para o mesmo valor, mas não há garantia de quando isso acontecerá Leituras podem retornar valores antigos
* Consistência Linearizável (Linearizability): Uma garantia forte de recência para leituras e escritas, fazendo com que o sistema pareça haver apenas uma cópia dos dados e todas as operações são atômicas e executadas em uma ordem bem definida
* Consistência Monotônica (Monotonic Reads): Uma garantia de consistência que assegura que um usuário nunca verá "o tempo voltar para trás" — ou seja, nunca lerá dados mais antigos depois de ter lido dados mais novos
* Consistência de Leitura-do-Próprio-Escrito (Read-Your-Writes Consistency): Uma garantia de consistência que garante que, se um usuário fizer uma escrita e depois tentar lê-la, ele sempre verá o valor mais recente que escreveu
* Consistência de Prefixo Consistente (Consistent Prefix Reads): Garante que leituras em um sistema distribuído vejam um prefixo consistente do histórico total de operações, preservando a ordem causal
* Failover (Failover): O processo de transferir a função de líder de um nó para outro em sistemas replicados com um único líder
* Fencing (Fencing Tokens): Técnica para garantir que um nó que erroneamente acredita ser o líder não cause danos ao sistema, permitindo escritas apenas na ordem crescente de tokens
* Follower (Seguidor): Uma réplica que não aceita escritas diretamente dos clientes, mas apenas processa as alterações de dados que recebe de um líder
* Hot Spots (Hot Spots): Partições com uma carga desproporcionalmente alta de requisições ou dados, levando a um desequilíbrio de carga
* Índice Secundário (Secondary Index): Uma estrutura adicional mantida ao lado do armazenamento de dados primário, que permite buscar eficientemente registros que correspondem a uma condição específica
* Índice Secundário Particionado por Documento (Document-Partitioned Secondary Index / Local Index): Onde os índices secundários são armazenados na mesma partição que a chave primária e o valor do documento
* Índice Secundário Particionado por Termo (Term-Partitioned Secondary Index / Global Index): Onde os índices secundários são particionados separadamente, usando os valores indexados
* Last Write Wins (LWW - Última Escrita Vence): Um algoritmo de resolução de conflitos que descarta todas as escritas conflitantes, exceto aquela com o timestamp mais recente Pode levar à perda de dados
* Leader (Líder): Em um sistema replicado, a réplica designada que é permitida a fazer alterações
* Log Lógico (Logical Log): Um formato de log que descreve as alterações de dados em um nível de abstração mais alto (ex* inserção de linha X, atualização de coluna Y), desacoplado dos detalhes do motor de armazenamento
* Particionamento (Partitioning / Sharding): O processo de dividir um grande conjunto de dados (um banco de dados) em subconjuntos menores e independentes chamados partições, que podem ser armazenadas em diferentes nós
* Particionamento por Faixa de Chaves (Partitioning by Key Range): Atribuir um intervalo contínuo de chaves a cada partição
* Particionamento por Hash de Chave (Partitioning by Hash of Key): Usar uma função hash para determinar qual partição uma chave pertence, visando distribuir os dados uniformemente
* Quórum (Quorum): Um número mínimo de réplicas que devem confirmar uma operação (leitura ou escrita) para que ela seja considerada bem-sucedida em um sistema distribuído sem líder
* Rebalanceamento (Rebalancing): O processo de mover partições entre nós em um cluster quando nós são adicionados ou removidos, ou quando a distribuição de dados se torna desigual
* Relacionamento "Happened-Before" (Happened-Before Relationship): Uma relação causal entre duas operações onde uma operação deve ter ocorrido antes da outra, ou uma depende da outra
* Replicação (Replication): Manter cópias dos mesmos dados em vários nós (réplicas) para alta disponibilidade, baixa latência ou escalabilidade de leitura
* Replicação Assíncrona (Asynchronous Replication): O líder processa uma escrita e replica para os seguidores sem esperar a confirmação de todos os seguidores; mais rápida, mas com risco de perda de dados
* Replicação Baseada em Líder (Leader-Based Replication): Uma arquitetura de replicação onde um nó (o líder) aceita todas as escritas e as propaga para outros nós (os seguidores)
* Replicação Multi-Líder (Multi-Leader Replication): Uma arquitetura de replicação onde vários nós podem aceitar escritas de clientes, replicando-as de forma assíncrona para outros líderes
* Replicação Sem Líder (Leaderless Replication): Uma arquitetura de replicação onde qualquer réplica pode aceitar escritas diretamente dos clientes, sem um único líder coordenador
* Replicação Síncrona (Synchronous Replication): Uma escrita é considerada bem-sucedida somente após todos os seguidores confirmarem que a receberam Maior durabilidade, mas menor performance
* Resolução de Conflitos (Conflict Resolution): O processo de determinar qual versão de dados prevalece quando ocorrem escritas conflitantes em um sistema distribuído
* Scatter/Gather: Um padrão de consulta em bancos de dados particionados onde a consulta é enviada para todas as partições, e os resultados são coletados e combinados
* Sistemas Shared-Nothing (Shared-Nothing Systems): Uma arquitetura onde cada máquina tem sua própria CPU, memória e discos, e a comunicação entre elas ocorre exclusivamente através da rede
* Skew (Desequilíbrio): Distribuição desigual de dados ou carga de trabalho entre as partições ou nós Pode se referir a um desequilíbrio de carga (hot spots) ou a uma anomalia de tempo em transações (read skew, write skew)
* Split Brain: Uma situação de falha em um sistema distribuído onde dois (ou mais) nós acreditam erroneamente serem o líder, o que pode levar a inconsistências e perda de dados
* Vetores de Versão (Version Vectors): Um mecanismo para detectar escritas concorrentes em sistemas distribuídos sem líder, rastreando as versões dos dados que cada nó "viu"
