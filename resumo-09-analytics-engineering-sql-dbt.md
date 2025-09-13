# Resumo do livro Analytics Engineering with SQL and DBT

## Prefácio

**Foco da Discussão do Capítulo:**
O prefácio introduz o conceito emergente da **Engenharia de Analytics**, posicionando-a como uma evolução natural da inteligência de negócios. Ele enfatiza a importância duradoura da **modelagem de dados** e do **SQL** em conjunto com ferramentas modernas como o **dbt** para construir um **_data value chain_ resiliente** e transformar dados em _insights_ acionáveis. O livro se propõe a ser um guia para integrar tecnologias antigas e novas, fortalecendo a gestão de dados.

**Principais Conceitos do Capítulo:**
* **Engenharia de Analytics:** Conceito que se tornou fundamental no mundo dos negócios, focado em entregar valor por meio de modelos de dados significativos e escaláveis.
* **Papel do Engenheiro de Analytics:** Atua como uma ponte entre a engenharia de dados e a análise, garantindo que os _insights_ sejam confiáveis e acionáveis.
* **Modelagem de Dados:** É o cerne das soluções de engenharia de analytics, envolvendo a estruturação de dados para refletir cenários do mundo real.
* **Ferramentas e Tecnologias:** O livro aborda o uso do **SQL** e do **dbt** como ferramentas principais. O dbt é apresentado como uma ferramenta transformadora que eleva o código de analytics ao nível de _software_ de produção, introduzindo testes e transformações inovadoras.
* **Estrutura do Livro:** Dividido em seis capítulos, cobrindo desde a evolução da gestão de dados até tópicos avançados do dbt e um caso de uso ponta a ponta.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
O prefácio define o **escopo e a importância do campo** para o engenheiro de dados. Ele contextualiza a necessidade de dominar a modelagem de dados, SQL e ferramentas modernas como o dbt para **construir _pipelines_ de dados escaláveis** e entregar valor de negócio, servindo como uma introdução à mentalidade necessária para o sucesso na engenharia de analytics.

---

## Capítulo 1: Analytics Engineering

**Foco da Discussão do Capítulo:**
Este capítulo traça a **evolução histórica da gestão de dados**, desde o _data warehousing_ tradicional até o surgimento das tecnologias _cloud-native_ e da **_modern data stack_**. Ele introduz o **papel do engenheiro de analytics**, suas responsabilidades e como ferramentas como **Apache Airflow** e **dbt** revolucionaram o processamento e a orquestração de dados, focando na flexibilidade e escalabilidade do ELT.

**Principais Conceitos do Capítulo:**
* **Evolução da Gestão de Dados:**
    * Início com **_data warehousing_** nos anos 1980 (Bill Inmon).
    * Avanço para tecnologias **_cloud-native_** como Amazon Redshift, Google BigQuery e Snowflake, simplificando tarefas administrativas e oferecendo capacidades avançadas de processamento de dados.
    * Surgimento da **_Modern Data Stack_** com ferramentas como **Apache Airflow, dbt e Looker**, transformando _workflows_ de dados.
* **O Papel do Engenheiro de Analytics:**
    * Uma evolução do papel do engenheiro de dados, focando em transformar dados brutos em conjuntos de dados limpos e bem definidos.
    * Responsabilidades incluem a criação de modelos de dados confiáveis, documentação de transformações e garantia de testes adequados ao longo dos _pipelines_.
* **Ciclo de Vida da Análise de Dados:** Envolve fases como definição do problema, modelagem de dados, ingestão e transformação de dados, monitoramento da qualidade, testes, documentação, análise de dados e visualização.
* **Estratégias de Ingestão e Transformação:**
    * **_Schema-on-write_:** Mais esforço na transformação dos dados brutos diretamente nos modelos.
    * **_Schema-on-read_:** Ingestão e armazenamento de dados com transformações mínimas, movendo transformações pesadas para camadas _downstream_.
* **ETL vs. ELT:**
    * **ETL (_Extract, Transform, Load_):** Abordagem tradicional onde os dados são extraídos, transformados em um sistema separado e depois carregados no destino.
    * **ELT (_Extract, Load, Transform_):** Abordagem mais recente e flexível, onde os dados são primeiro extraídos e carregados em um sistema de destino antes de serem transformados. Oferece maior flexibilidade e suporte a uma gama mais ampla de aplicações de dados e _insights_ em tempo real.
* **A Revolução dbt:**
    * **dbt (Data Build Tool):** Uma ferramenta _open source_ de linha de comando que simplifica e otimiza a transformação e modelagem de dados usando SQL.
    * **Integração com Airflow:** A integração do dbt com Airflow permite gerenciar e automatizar _pipelines_ de dados de forma mais eficiente. Airflow agenda as execuções do dbt, e o dbt realiza as transformações.
    * **Benefícios do dbt:** Permite escrever código reutilizável, mantenível e testável para transformações, facilitando a colaboração, agendamento, monitoramento e aplicando práticas de CI/CD para _software engineering_ aos dados. Suporta testes, documentação e rastreamento de dados.

**Exemplos de Código:**

1.  **Procedimento SQL para ETL Tradicional (Exemplo 1-1):**
    ```sql
    CREATE PROCEDURE etl_example AS
    BEGIN
        -- Extract data from the source table
        SELECT * INTO #temp_table FROM source_table;
        -- Transform data
        UPDATE #temp_table
        SET column1 = UPPER(column1),
            column2 = column2 * 2;
        -- Load data into the target table
        INSERT INTO target_table
        SELECT * FROM #temp_table;
    END
    stored procedure_ em SQL, onde a transformação ocorre antes do carregamento final.
    * **Importância num Pipeline de Dados:** Este exemplo mostra uma abordagem "legado" de ETL. Embora funcional, contrasta com a filosofia do ELT e do dbt, que promovem carregar os dados brutos primeiro no destino e então transformá-los. Compreender as limitações dessa abordagem tradicional ajuda o engenheiro de dados a apreciar os benefícios de ferramentas modernas de orquestração e transformação de dados.

2.  **DAG Simples do Apache Airflow (Exemplo 41):**
(hours=1),
    )

    task1 = BashOperator(
        task_id='print_date',
        bash_command='date',
        dag=dag,
    )
    task2 = BashOperator(
        task_id='sleep',
        bash_command='sleep 5',
        retries=3,
        dag=dag,
    )

    task1 >> task2
    ```
    * **Lógica do Código:** Este código define um DAG (`simple_dag`) que é agendado para rodar a cada hora. Ele contém duas tarefas: `print_date` que executa o comando `date` (imprime data/hora atual) e `sleep` que dorme por 5 segundos. A `task2` (`sleep`) é configurada para depender da `task1` (`print_date`) e tem 3 tentativas de _retry_ em caso de falha.
    * **O que se Propõe a Fazer:** Demonstrar a criação programática de um _workflow_ simples no Apache Airflow, incluindo agendamento, definição de tarefas, dependências e tratamento de _retries_.
    * **Importância num Pipeline de Dados:** O Airflow é uma ferramenta poderosa para a **orquestração de _workflows_ de dados**. Este exemplo mostra como ele pode ser usado para automatizar a execução de tarefas, gerenciar suas dependências e garantir a resiliência do _pipeline_ através de _retries_. Essa automação é crucial para manter os dados atualizados e precisos em ambientes de produção, sendo um complemento essencial ao dbt para o gerenciamento de todo o fluxo de dados.

3.  **Modelo dbt Simples (Exemplo 1-3):**
    ```sql
    {{ config(materialized='table') }}
    select
        sum(orders.revenue) as total_revenue
    from {{ ref('orders') }} as orders
    ```
    * **Lógica do Código:** Este modelo dbt usa um bloco de configuração Jinja (`{{ config(...) }}`) para especificar que o resultado será materializado como uma `table` (tabela física). A consulta SQL calcula a soma da coluna `revenue` do modelo `orders`, referenciado por `{{ ref('orders') }}`.
    * **O que se Propõe a Fazer:** Ilustrar a simplicidade de criar um modelo de transformação de dados no dbt usando SQL e a sintaxe Jinja para configurações e referências a outros modelos.
    * **Importância num Pipeline de Dados:** Este _snippet_ destaca a capacidade do dbt de **elevar o código de analytics a um nível de _software_ de produção**. Ele permite que engenheiros de analytics escrevam transformações de dados de forma modular, reutilizável e testável, promovendo uma **fonte única de verdade** para métricas de negócio e simplificando o gerenciamento do _pipeline_.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **fundamental para o engenheiro de dados** compreender a paisagem atual da engenharia de analytics. Ele fornece o contexto histórico e a justificativa para o surgimento de novas ferramentas e papéis, permitindo ao engenheiro tomar decisões arquitetônicas informadas e **adotar as melhores práticas de ELT, orquestração (Airflow) e transformação (dbt)** para construir _pipelines_ de dados eficientes, escaláveis e confiáveis.

---

## Capítulo 2: Data Modeling for Analytics

**Foco da Discussão do Capítulo:**
Este capítulo detalha as **técnicas de modelagem de dados** cruciais para a engenharia de analytics. Ele explora a **normalização de dados** (1NF, 2NF, 3NF), os princípios da **modelagem dimensional** (esquemas estrela e floco de neve), o **Data Vault 2.0** e, principalmente, como o **dbt habilita a modelagem de dados modular** através de suas camadas (staging, intermediate, mart), referências a modelos e testes. O capítulo também apresenta o **padrão de arquitetura Medallion**.

**Principais Conceitos do Capítulo:**
* **Modelagem de Dados:** Processo de estruturar dados para refletir cenários do mundo real, essencial para o engenheiro de analytics. Envolve fases conceitual, lógica e física.
* **Normalização de Dados:** Técnica para organizar dados em estruturas lógicas e eficientes, minimizando redundância, melhorando a integridade e otimizando o desempenho de consultas em sistemas OLTP.
    * **Primeira Forma Normal (1NF):** Elimina grupos repetitivos, quebrando dados em unidades atômicas.
    * **Segunda Forma Normal (2NF):** Remove dependências parciais de chaves primárias.
    * **Terceira Forma Normal (3NF):** Remove dependências transitivas, assegurando que atributos não-chave dependam apenas da chave primária.
* **Modelagem Dimensional:** Abordagem para _data warehouses_ e BI.
    * **Esquema Estrela (_Star Schema_):** Amplamente utilizado, classifica tabelas em dimensões (atributos descritivos) e fatos (medidas e eventos). Foca na simplicidade e desempenho de consulta. Recomenda o uso de `LEFT JOIN` para garantir que todos os registros da tabela de fatos sejam incluídos, lidando com não correspondências com `COALESCE`.
    * **Esquema Floco de Neve (_Snowflake Schema_):** Uma extensão do esquema estrela, onde as tabelas de dimensão são normalizadas. Apenas a dimensão de mais alta hierarquia se conecta diretamente à tabela de fatos.
    * **Data Vault 2.0:** Uma abordagem de modelagem que combina elementos de 3NF e modelagem dimensional para um _data warehouse_ empresarial lógico. Projetado para flexibilidade, escalabilidade, rastreabilidade e auditabilidade para diversos tipos de dados. Utiliza _hubs_ (chaves de negócio únicas), _links_ (relações entre _hubs_) e _satellites_ (detalhes históricos e contextuais dos _hubs_ e _links_).
* **Modelagem de Dados Modular com dbt:** Estrutura projetos dbt para construir modelos de forma organizada, reutilizável e fácil de manter.
    * **Referência a Modelos (`{{ ref() }}`):** Sintaxe Jinja que permite estabelecer dependências entre modelos dbt e gerar gráficos de linhagem. Essencial para reutilização de código e garantia de consistência.
    * **Referência a Fontes (`{{ source() }}`):** Sintaxe Jinja para referenciar dados brutos na plataforma de dados. Deve ser usado principalmente na camada de _staging_ para evitar impactar a flexibilidade e modularidade do _workflow_.
    * **Modelos de Staging:** Geralmente materializam como _views_, limpam e preparam dados brutos, evitando _joins_ e agregações desnecessárias. Promovem o princípio DRY (_Don't Repeat Yourself_) e servem como blocos de construção para modelos posteriores.
    * **Modelos Intermediários:** Combinam e transformam dados de modelos de _staging_ para operações mais complexas antes de chegarem à camada de _marts_.
    * **Modelos de Mart:** A camada superior do _pipeline_, responsável por integrar e apresentar entidades de negócio aos usuários finais (dashboards/aplicações).
* **Testes de Modelos dbt:** Garantem a acurácia e confiabilidade dos modelos de dados.
    * **Testes Singulares:** Escritos em arquivos SQL, verificam aspectos específicos dos dados (ex: ausência de valores NULL).
    * **Testes Genéricos:** Mais versáteis, aplicados a múltiplos modelos ou fontes de dados, definidos em arquivos YAML.
* **Documentação de Dados:** Geração automatizada de documentação para modelos dbt usando a CLI (`dbt docs generate`) e comentários no SQL.
* **Otimização de Modelos de Dados:** Uso de materializações apropriadas para melhor desempenho e depuração com comandos dbt (`dbt run --full-refresh`).
* **Padrão de Arquitetura Medallion:** Um paradigma de modelagem para ambientes _lakehouse_ com três camadas de refinamento de dados:
    * **Bronze Layer:** Dados brutos dos sistemas fonte, com metadados adicionais. Foca em CDC e arquivo histórico.
    * **Silver Layer:** Dados limpos e transformados, prontos para análise.
    * **Gold Layer:** Dados agregados para _insights_ de negócio, BI, relatórios e aplicações de _machine learning_. Garante confiabilidade, desempenho e transações ACID.

**Exemplos de Código:**

1.  **Modelo Físico de Banco de Dados (`CREATE TABLE` - Exemplo 2-1):**
    ```sql
    CREATE TABLE category (
      category_id INT PRIMARY KEY,
      category_name VARCHAR(255)
    );
    CREATE TABLE books (
      book_id INT PRIMARY KEY,
      ISBN VARCHAR(13),
      title VARCHAR(50),
      summary VARCHAR(255)
      FOREIGN KEY (category_id) REFERENCES category(category_id),
    );
    CREATE TABLE authors (
      author_id INT PRIMARY KEY,
      author_name VARCHAR(255),
      date_birth DATETIME
    );
    CREATE TABLE publishes (
      book_id INT,
      author_id INT,
      publish_date DATE,
      planned_publish_date DATE
      FOREIGN KEY (book_id) REFERENCES books(book_id),
      FOREIGN KEY (author_id) REFERENCES author(author_id)
    );
    ```
    * **Lógica do Código:** Este código DDL (`Data Definition Language`) define a estrutura de quatro tabelas (`category`, `books`, `authors`, `publishes`) para um banco de dados relacional. Ele especifica o nome de cada coluna, seu tipo de dado (ex: `INT`, `VARCHAR(255)`, `DATETIME`, `DATE`) e restrições de integridade, como `PRIMARY KEY` para identificadores únicos e `FOREIGN KEY` para estabelecer relações entre as tabelas.
    * **O que se Propõe a Fazer:** Demonstrar a fase física da modelagem de dados, que traduz o modelo lógico em um esquema de banco de dados real com tipos de dados e restrições específicas para um sistema de gerenciamento de banco de dados (DBMS) como MySQL.
    * **Importância num Pipeline de Dados:** A criação de um modelo físico é essencial para o engenheiro de dados, pois define a **estrutura real e a integridade** do banco de dados onde os dados serão armazenados. Ele garante que os dados sejam compatíveis com o sistema subjacente e otimizados para armazenamento, recuperação e desempenho de consultas.

2.  **Tabelas em 3ª Forma Normal (3NF) (`CREATE TABLE` - Exemplo 2-5):**
    ```sql
    CREATE TABLE authors (
      author_id INT PRIMARY KEY,
      author_name VARCHAR(100)
    );
    CREATE TABLE books (
      book_id INT PRIMARY KEY,
      title VARCHAR(100),
    );
    CREATE TABLE genres (
      genre_id INT PRIMARY KEY,
      genre_name VARCHAR(50)
    );
    CREATE TABLE bookDetails (
      book_id INT PRIMARY KEY,
      author_id INT,
      genre_id INT,
      publication_year INT,
      FOREIGN KEY (author_id) REFERENCES authors(author_id),
      FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
    );
    ```
    * **Lógica do Código:** Este exemplo mostra as tabelas `authors`, `books`, `genres` e `bookDetails` após terem sido divididas e reestruturadas para satisfazer a Terceira Forma Normal. Isso envolve a eliminação de dependências transitivas, onde atributos não-chave dependem de outros atributos não-chave, garantindo que cada coluna esteja diretamente relacionada à chave primária da tabela.
    * **O que se Propõe a Fazer:** Ilustrar o resultado da aplicação dos princípios de normalização de dados até a 3NF.
    * **Importância num Pipeline de Dados:** A normalização é fundamental para **reduzir a redundância de dados e melhorar a integridade dos dados** em sistemas OLTP (_Online Transaction Processing_). Para o engenheiro de dados, isso significa projetar bancos de dados mais eficientes, consistentes e fáceis de manter, o que é vital para sistemas transacionais onde a integridade é prioritária.

3.  **Criação de Tabelas de Dimensão no Esquema Estrela (`CREATE TABLE` - Exemplo 2-7):**
    ```sql
    -- Create the dimension tables
    CREATE TABLE dimBooks (
      book_id INT PRIMARY KEY,
      title VARCHAR(100)
    );
    CREATE TABLE dimAuthors (
      author_id INT PRIMARY KEY,
      author VARCHAR(100)
    );
    CREATE TABLE dimGenres (
      genre_id INT PRIMARY KEY,
      genre VARCHAR(50)
    );
    ```
    * **Lógica do Código:** Este código DDL cria três tabelas de dimensão: `dimBooks`, `dimAuthors` e `dimGenres`. Cada tabela possui uma chave primária (`PRIMARY KEY`) e colunas que armazenam atributos descritivos relacionados à respectiva entidade (livros, autores, gêneros).
    * **O que se Propõe a Fazer:** Demonstrar a criação de tabelas de dimensão, que são um componente central do esquema estrela em modelagem dimensional.
    * **Importância num Pipeline de Dados:** As tabelas de dimensão fornecem o **contexto descritivo** para análises. Elas permitem que os usuários de BI e ferramentas analíticas filtrem, agrupem e explorem dados por atributos de negócio (ex: nome do autor, título do livro). Isso é crucial para a usabilidade e a flexibilidade de _data warehouses_.

4.  **Criação de Tabela de Fatos no Esquema Estrela (`CREATE TABLE` - Exemplo 2-8):**
    ```sql
    -- Create the fact table
    CREATE TABLE factBookPublish (
      book_id INT,
      author_id INT,
      genre_id INT,
      publication_year INT,
      FOREIGN KEY (book_id) REFERENCES dimBooks (book_id),
      FOREIGN KEY (author_id) REFERENCES dimAuthors (author_id),
      FOREIGN KEY (genre_id) REFERENCES dimGenres (genre_id)
    );
    ```
    * **Lógica do Código:** Este código DDL cria uma tabela de fatos chamada `factBookPublish`. Ela contém chaves estrangeiras (`FOREIGN KEY`) que referenciam as chaves primárias das tabelas de dimensão correspondentes (`dimBooks`, `dimAuthors`, `dimGenres`) e colunas que armazenam medidas ou eventos (neste caso, `publication_year` como um atributo do evento de publicação).
    * **O que se Propõe a Fazer:** Ilustrar a criação de uma tabela de fatos, que é outro componente central do esquema estrela.
    * **Importância num Pipeline de Dados:** As tabelas de fatos armazenam as **métricas e eventos de negócio** mensuráveis, como vendas, publicações ou visitas. A conexão com as dimensões (através de chaves estrangeiras) permite análises contextuais e flexíveis, respondendo a perguntas como "quantos livros de qual gênero foram publicados por qual autor em um determinado ano".

5.  **Consulta SQL com `LEFT JOIN` e `COALESCE` (Exemplo de texto):**
    ```sql
    SELECT COALESCE(dg.genre, 'Not Available'),
           COUNT(*) AS total_publications
    FROM factBookPublish bp
    LEFT JOIN dimGenres dg ON dg.genre_id = bp.genre_id
    GROUP BY dg.genre;
    ```
    * **Lógica do Código:** Esta consulta utiliza um `LEFT JOIN` para combinar a tabela de fatos `factBookPublish` com a tabela de dimensão `dimGenres`. O `LEFT JOIN` garante que todos os registros da tabela de fatos sejam incluídos no resultado. A função `COALESCE` substitui valores NULL na coluna `genre` (que podem ocorrer se uma publicação não tiver um gênero correspondente na dimensão) por 'Not Available' ou '-1'. A consulta então agrupa por gênero e conta o total de publicações.
    * **O que se Propõe a Fazer:** Demonstrar como usar `LEFT JOIN` em conjunto com `COALESCE` para recuperar dados analíticos de um esquema estrela, lidando com a possível ausência de correspondências em tabelas de dimensão de forma robusta.
    * **Importância num Pipeline de Dados:** Esta é uma **melhor prática** para consultas em _data warehouses_. O `LEFT JOIN` assegura que nenhuma observação da tabela de fatos seja perdida, enquanto `COALESCE` melhora a **robustez da consulta e a qualidade do relatório**, evitando NULLs e tornando os dados mais compreensíveis e utilizáveis para análise.

6.  **Modelo de Staging dbt (`stg_books.sql` - Exemplo 2-16):**
    ```sql
    /* This should be file stg_books.sql, and it queries the raw table to create the new model */
    SELECT
      book_id,
      title,
      author,
      publication_year,
      genre
    FROM
      raw_books
    ```
    * **Lógica do Código:** Este modelo dbt simples, contido no arquivo `stg_books.sql`, simplesmente seleciona todas as colunas relevantes de uma tabela de dados brutos (`raw_books`). Ele pode incluir transformações básicas como renomeação de colunas ou conversão de tipos de dados.
    * **O que se Propõe a Fazer:** Ilustrar a criação de um modelo de _staging_ no dbt. Esta camada inicial do _pipeline_ dbt foca na transformação e preparação dos dados brutos antes de um processamento mais complexo.
    * **Importância num Pipeline de Dados:** Os modelos de _staging_ são a **primeira camada de transformação** no dbt, cruciais para a **qualidade e consistência dos dados**. Eles garantem que os dados brutos sejam limpos e padronizados, atuando como blocos de construção para modelos posteriores e promovendo o princípio DRY (_Don't Repeat Yourself_).

7.  **Modelo Intermediário dbt (`int_book_authors.sql` - Exemplo 2-17):**
    ```sql
    -- This should be file int_book_authors.sql
    -- Reference the staging models
    WITH
      books AS (
        SELECT *
        FROM {{ ref('stg_books') }}
      ),
      authors AS (
        SELECT *
        FROM {{ ref('stg_authors') }}
      )
    -- Combine the relevant information
    SELECT
      b.book_id,
      b.title,
      a.author_id,
      a.author_name
    FROM
      books b
    JOIN
      authors a ON b.author_id = a.author_id
    ```
    * **Lógica do Código:** O modelo `int_book_authors` utiliza Common Table Expressions (CTEs) nomeadas `books` e `authors`, que, por sua vez, referenciam os modelos de _staging_ `stg_books` e `stg_authors` usando a sintaxe Jinja `{{ ref() }}`. Em seguida, ele combina essas CTEs através de um `JOIN` para relacionar livros e autores.
    * **O que se Propõe a Fazer:** Demonstrar a criação de um modelo intermediário no dbt, que combina e transforma dados de múltiplos modelos de _staging_ para operações mais complexas.
    * **Importância num Pipeline de Dados:** Modelos intermediários são cruciais para **quebrar transformações complexas em etapas menores e gerenciáveis**, aumentando a clareza, a testabilidade e a reutilização do código. Eles constroem a ponte entre os dados brutos (camada de _staging_) e os modelos de negócio (camada de _marts_).

8.  **Modelo de Mart dbt (`mart_book_authors.sql` - Exemplo 2-18):**
    ```sql
    {{
      config(
        materialized='table',
        unique_key='author_id',
        sort='author_id'
      )
    }}
    WITH book_counts AS (
      SELECT
        author_id,
        COUNT(*) AS total_books
      FROM {{ ref('int_book_authors') }}
      GROUP BY author_id
    )
    SELECT
      author_id,
      total_books
    FROM book_counts
    ```
    * **Lógica do Código:** Este modelo `mart_book_authors` usa um bloco `config` para definir sua materialização como uma `table` e especificar `unique_key` e `sort`. Ele utiliza uma CTE (`book_counts`) que referencia o modelo intermediário `int_book_authors`, agrupa por `author_id` e conta o total de livros, apresentando um resultado final agregado.
    * **O que se Propõe a Fazer:** Ilustrar a criação de um modelo de _mart_ no dbt. Esta camada superior do _pipeline_ dbt foca na agregação e apresentação de dados de negócio aos usuários finais para dashboards ou aplicações.
    * **Importância num Pipeline de Dados:** Modelos de _mart_ são a **camada de consumo** do _data warehouse_, projetados para serem usados diretamente por ferramentas de BI e aplicações. Eles fornecem uma **visão coerente e agregada dos dados**, crucial para a tomada de decisões de negócio e para a "fonte única de verdade".

9.  **Teste Genérico dbt em YAML (Exemplo 2-20):**
    ```yaml
    version: 2
    tests:
      - name: non_negative_values
        severity: warn
        description: Check for non-negative values in specific columns
        columns:
          - column_name: amount
            assert_non_negative: {}
          - column_name: quantity
            assert_non_negative: {}
    ```
    * **Lógica do Código:** Este arquivo YAML define um teste genérico nomeado `non_negative_values`. Ele é configurado para verificar se os valores nas colunas `amount` e `quantity` são não-negativos. Se a condição for violada, ele emitirá um aviso (`severity: warn`).
    * **O que se Propõe a Fazer:** Demonstrar como definir um teste genérico reutilizável em dbt, que pode ser aplicado a múltiplas colunas em diferentes modelos.
    * **Importância num Pipeline de Dados:** Testes genéricos são altamente **escaláveis e essenciais para a qualidade dos dados**. Eles automatizam a verificação de padrões comuns (como valores não-negativos, unicidade, não-nulidade) em todo o _data warehouse_, garantindo a confiabilidade dos dados e capturando erros cedo no ciclo de desenvolvimento.

10. **Reutilização de Teste Genérico dbt em YAML (Exemplo 2-21):**
    ```yaml
    version: 2
    models:
      - name: my_model
        columns:
          - column_name: amount
            tests: ["my_project.non_negative_values"]
          - column_name: quantity
            tests: ["my_project.non_negative_values"]
    ```
    * **Lógica do Código:** Neste arquivo YAML, o modelo `my_model` especifica que as colunas `amount` e `quantity` devem ser testadas usando o teste genérico `non_negative_values` definido no projeto `my_project`.
    * **O que se Propõe a Fazer:** Ilustrar como um teste genérico definido centralmente pode ser facilmente reutilizado em múltiplas colunas de diferentes modelos.
    * **Importância num Pipeline de Dados:** A capacidade de reutilizar testes genéricos é uma **melhor prática de eficiência e consistência**. Isso garante que as regras de validação de dados sejam aplicadas uniformemente em todo o _data warehouse_ sem duplicação de código, reduzindo o esforço de manutenção e aumentando a confiança nos dados.

11. **Documentação de Modelo dbt em SQL com Comentários (Exemplo 2-22):**
    ```sql
    /* nps_metrics.sql
    *-- This model calculates the Net Promoter Score (NPS) for our product based on customer feedback.*
    *Dependencies: - This model relies on the "customer_feedback" table in the "feedback" schema, which stores customer feedback data.  - It also depends on the "customer" table in the "users" schema, containing customer information.*
    *Calculation: -- The NPS is calculated by categorizing customer feedback from Promoters, Passives, and Detractors based on their ratings. -- Promoters: Customers with ratings of 9 or 10. -- Passives: Customers with ratings of 7 or 8. -- Detractors: Customers with ratings of 0 to 6. -- The NPS is then derived by subtracting the percentage of Detractors from the percentage of Promoters. */
    ```
    * **Lógica do Código:** Este bloco de comentários SQL, que pode ser aprimorado com sintaxe Markdown, é incorporado diretamente ao arquivo `nps_metrics.sql`. Ele fornece detalhes essenciais sobre o modelo, suas dependências e a lógica de cálculo da métrica.
    * **O que se Propõe a Fazer:** Demonstrar como documentar um modelo dbt e suas métricas diretamente no arquivo SQL usando comentários, que são então usados para gerar a documentação automatizada do dbt.
    * **Importância num Pipeline de Dados:** A **documentação como código** é uma **melhor prática crucial** para engenheiros de analytics. Ela garante que a lógica de negócio e as definições de dados sejam claras, acessíveis e mantidas junto ao código, facilitando o _onboarding_, a colaboração e a auditoria dos modelos de dados.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **fundamental para o engenheiro de dados** que projeta e implementa _data warehouses_ e soluções analíticas. Ele fornece uma base sólida em **técnicas de modelagem de dados (normalização, dimensional, Data Vault)** e demonstra como o **dbt viabiliza uma arquitetura modular e testável**. O engenheiro aprende a aplicar as **melhores práticas para garantir a qualidade, integridade e usabilidade dos dados**, desde a camada bruta até os modelos de consumo, o que é vital para construir uma "fonte única de verdade" e permitir análises de negócio confiáveis.

---

## Capítulo 3: SQL for Analytics

**Foco da Discussão do Capítulo:**
Este capítulo aprofunda a **resiliência e versatilidade do SQL** como linguagem de análise, cobrindo os fundamentos de bancos de dados relacionais e não-relacionais, tipos de linguagens SQL (DDL, DML, DCL, TCL), views, CTEs, funções de janela, e como o SQL é usado no processamento de dados distribuídos. Também apresenta a integração do SQL com ferramentas de _big data_ como **DuckDB, Polars e FugueSQL**, e até mesmo para treinamento de modelos de _machine learning_ com **Dask-SQL**.

**Principais Conceitos do Capítulo:**
* **Resiliência do SQL:** Uma linguagem padronizada, poderosa e versátil para gerenciar bancos de dados relacionais. É a primeira escolha para tarefas analíticas devido à sua sintaxe intuitiva e ampla aceitação.
    * **Legibilidade:** Sua natureza declarativa permite que usuários de diferentes perfis entendam e interpretem o código, focando no "o quê" dos dados, não no "como".
    * **CTEs (Common Table Expressions):** Auxiliam na quebra de grandes consultas em partes menores, gerenciáveis e testáveis, promovendo modularidade.
    * **Funções Lambda:** Permitem escrever funções arbitrárias diretamente em instruções SQL, aumentando a flexibilidade (discutido com Dask-SQL).
* **A Pirâmide DIKW (Dados, Informação, Conhecimento, Sabedoria):** Modelo conceitual que descreve a transformação de dados brutos em sabedoria acionável, com os bancos de dados servindo como base.
* **Fundamentos de Banco de Dados:**
    * **Categorias:** **Relacionais** (dados em tabelas, chaves para relações, SQL, propriedades ACID) e **Não-Relacionais (NoSQL)**.
    * **DBMS (_Database Management System_):** Software para interagir com bancos de dados, garantindo segurança, integridade, concorrência, recuperação e _backup_.
    * **Linguagens de Interação com Banco de Dados:**
        * **DDL (_Data Definition Language_):** Para gerenciar esquemas (CREATE, DROP, ALTER, RENAME, TRUNCATE, CONSTRAINT).
        * **DML (_Data Manipulation Language_):** Para manipular dados (SELECT, INSERT, UPDATE, DELETE).
        * **DCL (_Data Control Language_):** Para gerenciar permissões.
        * **TCL (_Transaction Control Language_):** Para gerenciar transações.
    * **Tipos de Dados Comuns:** `INT`, `DECIMAL`, `BOOLEAN`, `DATE`, `TIME`, `TIMESTAMP`, `TEXT`.
    * **Constraints:** Regras para garantir a integridade dos dados (PRIMARY KEY, FOREIGN KEY, NOT NULL, UNIQUE, CHECK).
* **Manipulação de Dados com DML:**
    * **`INSERT`:** Adiciona novos registros a uma tabela.
    * **`SELECT`:** Extrai dados específicos de um banco de dados, retornando um _result set_. Pode usar `WHERE` (filtragem condicional), `GROUP BY` (agregação), `HAVING` (filtragem em grupos), `ORDER BY` (ordenação).
    * **`JOIN`:** Combina dados de múltiplas tabelas (INNER, LEFT, RIGHT, FULL, CROSS JOIN).
    * **`UPDATE`:** Modifica registros existentes em uma tabela.
    * **`DELETE`:** Remove registros de uma tabela. Pode-se usar também _soft delete_.
* **Views:** Tabelas virtuais definidas por uma consulta. Não armazenam dados fisicamente, mas recuperam dinamicamente. Atuam como filtros, consolidam dados e simplificam consultas complexas, além de prover segurança.
* **CTEs (_Common Table Expressions_):** Conjuntos de resultados temporários que melhoram a legibilidade e a mantenebilidade de consultas complexas. Podem ser encadeadas, reutilizadas e são uma alternativa a _views_ permanentes.
* **Funções de Janela (_Window Functions_):** Realizam cálculos em um conjunto de linhas relacionadas à linha atual sem agrupar. Incluem funções de agregação (MAX, MIN, AVG, SUM, COUNT), ranqueamento (ROW_NUMBER, RANK, DENSE_RANK, NTILE) e analíticas (LEAD, LAG, FIRST_VALUE, LAST_VALUE).
* **SQL para Processamento de Dados Distribuídos:** Ferramentas que estendem as capacidades SQL para lidar com _big data_ de forma eficiente.
    * **DuckDB:** Banco de dados OLAP in-process, rápido para cargas de trabalho intensivas em análise. Pode ser integrado com Pandas e executar SQL em DataFrames.
    * **Polars:** Biblioteca Python de alta performance para manipulação de dados, escrita em Rust. Suporta operações paralelas (multithreading), avaliação _lazy_ e pode executar SQL diretamente em DataFrames.
    * **FugueSQL:** Estrutura para executar código Python ou SQL em várias plataformas de computação distribuída (Spark, Dask, Polars). Fornece uma interface unificada e simplifica a manutenção de código em projetos de _big data_.
* **Bônus: Treinamento de Modelos de ML com SQL:** Uso de ferramentas como **Dask-SQL** para criar, treinar, fazer previsões e exportar modelos de _machine learning_ usando sintaxe SQL (CREATE MODEL, PREDICT, EXPORT MODEL).

**Exemplos de Código:**

1.  **Sintaxe `ALTER TABLE` (Exemplo 3-3):**
    ```sql
    -- Add a new column
    ALTER TABLE table_name ADD column_name datatype [column_constraint];

    -- Modify a datatype of an existing column
    ALTER TABLE table_name ALTER COLUMN column_name [new_datatype];

    -- Rename a column
    ALTER TABLE table_name RENAME COLUMN old_column_name TO new_column_name;

    -- Add a new constraint to a column
    ALTER TABLE table_name ADD CONSTRAINT constraint_name constraint_type (column_name);

    -- Modify an existing constraint
    ALTER TABLE table_name ALTER CONSTRAINT constraint_name [new_constraint];

    -- Remove an existing column
    ALTER TABLE table_name DROP COLUMN column_name;
    ```
    * **Lógica do Código:** Este _snippet_ DDL apresenta a sintaxe para diversas operações de modificação na estrutura de uma tabela, como adicionar uma nova coluna (`ADD`), alterar o tipo de dado de uma coluna existente (`ALTER COLUMN`), renomear uma coluna (`RENAME COLUMN`), adicionar/modificar/remover restrições (`ADD CONSTRAINT`, `ALTER CONSTRAINT`) e remover uma coluna (`DROP COLUMN`).
    * **O que se Propõe a Fazer:** Ilustrar a flexibilidade do DDL para adaptar o esquema do banco de dados a requisitos de negócio em constante mudança.
    * **Importância num Pipeline de Dados:** O comando `ALTER TABLE` é crucial para engenheiros de dados na **manutenção e evolução de esquemas de banco de dados**. Ele permite ajustes na estrutura das tabelas ao longo do tempo sem perder os dados existentes, o que é vital para a **adaptação e longevidade dos modelos de dados**.

2.  **Instrução `INSERT` (Exemplo 3-5, 3-6):**
    ```sql
    -- Sintaxe genérica
    INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);

    -- Inserindo dados na tabela authors
    INSERT INTO authors (author_id, author_name)
    VALUES (1, 'Stephanie Mitchell'), (2, 'Paul Turner'), (3, 'Julia Martinez'), (4, 'Rui Machado'), (5, 'Thomas Brown');
    ```
    * **Lógica do Código:** O primeiro _snippet_ mostra a sintaxe padrão para inserir uma ou mais linhas de dados em uma tabela específica, opcionalmente listando as colunas. O segundo _snippet_ aplica essa sintaxe para inserir múltiplos registros simulados nas colunas `author_id` e `author_name` da tabela `authors`.
    * **O que se Propõe a Fazer:** Adicionar novos registros a uma tabela em um banco de dados.
    * **Importância num Pipeline de Dados:** O comando `INSERT` é fundamental para a **ingestão e atualização de dados** em bancos de dados. No contexto de ETL (ou ELT), ele é usado na fase de "Load" para persistir dados transformados ou para alimentar tabelas de dimensão/fato em um _data warehouse_.

3.  **`SELECT` com Operadores Condicionais (Exemplo 3-10):**
    ```sql
    -- Livros publicados antes de 2015
    SELECT
      book_title,
      publication_year
    FROM books
    WHERE publication_year < 2015;

    -- Livros publicados em 2017
    SELECT
      book_title,
      publication_year
    FROM books
    WHERE publication_year = 2017;

    -- Livros com "Python" no título
    SELECT
      book_title,
      publication_year
    FROM books
    WHERE book_title LIKE '%Python%';
    ```
    * **Lógica do Código:** Estes _snippets essencial** em qualquer análise ou pipeline de dados. Permite que analistas e engenheiros de dados selecionem subconjuntos de dados relevantes para suas consultas, otimizando o desempenho e focando nos _insights_ necessários.

4.  **`SELECT` com `GROUP BY` e `HAVING` (Exemplo 3-15):**
    ```sql
    SELECT
      category_id,
      COUNT(book_id) AS book_count
    FROM bookCategory
    GROUP BY category_id
    HAVING COUNT(book_id) >= 2;
    ```
    * **Lógica do Código:** Esta consulta agrupa os registros da tabela `bookCategory` por `category_id`. Em seguida, usa a função agregada `COUNT(book_id)` para contar o número de livros em cada categoria. Finalmente, a cláusula `HAVING` filtra esses grupos, retornando apenas as categorias onde a contagem de livros (`book_count`) é maior ou igual a 2.
    * **O que se Propõe a Fazer:** Agrupar dados com base em uma ou mais colunas e então filtrar esses grupos agregados com base em uma condição.
    * **Importância num Pipeline de Dados:** Os comandos `GROUP BY` e `HAVING` são essenciais para **análises sumárias e geração de relatórios**. Eles permitem ao engenheiro de dados agregar métricas e filtrar os resultados agregados, fornecendo _insights_ de alto nível sobre os dados, como "quais categorias têm pelo menos X livros".

5.  **`SELECT` com `ORDER BY` (Exemplo 3-17):**
    ```sql
    SELECT
      book_title,
      publication_year
    FROM books
    ORDER BY publication_year DESC;
    ```
    * **Lógica do Código:** Esta consulta seleciona o título e o ano de publicação dos livros da tabela `books`. A cláusula `ORDER BY publication_year DESC` organiza o conjunto de resultados em ordem decrescente com base no ano de publicação, listando os livros mais recentes primeiro.
    * **O que se Propõe a Fazer:** Organizar o conjunto de resultados de uma consulta em uma sequência desejada.
    * **Importância num Pipeline de Dados:** A ordenação é crucial para a **apresentação e exploração de dados**, facilitando a identificação de tendências (ex: livros mais novos), os itens mais recentes/antigos, ou os valores mais altos/baixos em um conjunto de dados.

6.  **`INNER JOIN` (Exemplo 3-20):**
    ```sql
    SELECT
      authors.author_id,
      authors.author_name,
      books.book_title
    FROM authors
    INNER JOIN books ON Authors.author_id = Books.author_id
    ```
    * **Lógica do Código:** Esta consulta combina linhas das tabelas `authors` e `books` usando um `INNER JOIN`. A condição `ON Authors.author_id = Books.author_id` especifica que apenas as linhas onde os valores de `author_id` são correspondentes em ambas as tabelas serão incluídas no resultado. Isso efetivamente retorna apenas os autores que possuem livros.
    * **O que se Propõe a Fazer:** Combinar dados de múltiplas tabelas com base em uma condição de correspondência comum.
    * **Importância num Pipeline de Dados:** Os comandos `JOIN` são o **alicerce da integração de dados** em bancos de dados relacionais. Eles permitem que engenheiros de dados construam visões unificadas dos dados a partir de fontes separadas, o que é fundamental para análises complexas e para a criação de tabelas de fatos e dimensões.

7.  **Instrução `UPDATE` (Exemplo 3-29):**
    ```sql
    UPDATE books
    SET book_title = 'Learning React Fundamentals'
    WHERE book_id = 2;
    ```
    * **Lógica do Código:** Este comando `UPDATE` modifica a coluna `book_title` na tabela `books`, definindo seu novo valor como 'Learning React Fundamentals'. A cláusula `WHERE book_id = 2` garante que apenas o registro com `book_id` igual a 2 seja atualizado.
    * **O que se Propõe a Fazer:** Modificar registros existentes em uma tabela do banco de dados.
    * **Importância num Pipeline de Dados:** O comando `UPDATE` é essencial para **manter a acurácia e a atualização dos dados** no banco de dados. Ele é usado para corrigir erros, refletir novas informações ou aplicar transformações de dados que modificam o estado existente dos registros.

8.  **Instrução `DELETE` (Exemplo 3-31):**
    ```sql
    DELETE FROM Category
    WHERE category_id = 6
    ```
    * **Lógica do Código:** Este comando `DELETE` remove uma ou mais linhas da tabela `Category`. A cláusula `WHERE category_id = 6` especifica que apenas a linha onde `category_id` é igual a 6 deve ser removida. Se a cláusula `WHERE` fosse omitida, todas as linhas da tabela seriam excluídas.
    * **O que se Propõe a Fazer:** Remover registros de uma tabela com base em uma condição específica.
    * **Importância num Pipeline de Dados:** O comando `DELETE` é fundamental para a **manutenção de dados**, permitindo a remoção de informações desatualizadas, irrelevantes ou incorretas. Alternativamente, técnicas de "soft delete" (onde um _flag_ indica que o registro está deletado) podem ser usadas para preservar o histórico e a auditabilidade.

9.  **Criação de View (`CREATE VIEW` - Exemplo 3-32):**
    ```sql
    CREATE VIEW author_book_count AS
    SELECT a.author_id,
           a.author_name,
           COUNT(b.book_id) AS total_books
    FROM authors a
    LEFT JOIN books b ON a.author_id = b.author_id
    GROUP BY a.author_id, a.author_name;
    ```
    * **Lógica do Código:** Esta instrução cria uma _view_ virtual chamada `author_book_count`. A _view_ é definida por uma consulta que une as tabelas `authors` e `books`, agrupa por autor e conta o número total de livros por autor. Quando a _view_ é consultada, essa consulta subjacente é executada dinamicamente.
    * **O que se Propõe a Fazer:** Criar uma tabela virtual (view) que simplifica consultas complexas e reutiliza a lógica de consulta.
    * **Importância num Pipeline de Dados:** Views melhoram a **abstração e a segurança** dos dados. Elas permitem que analistas e usuários de negócio consultem dados complexos sem precisar entender a estrutura subjacente das tabelas, além de poderem ser usadas para ocultar colunas ou linhas sensíveis, facilitando a governança de dados.

10. **Sintaxe de CTE (`WITH` - Exemplo 3-37):**
    ```sql
    WITH cte_name (column1, column2, ..., columnN) AS (
        -- Query definition goes here
    )
    SELECT column1, column2, ..., columnN
    FROM cte_name
    -- Additional query operations go here
    ```
    * **Lógica do Código:** Este _snippet_ define a estrutura de uma Common Table Expression (CTE). Ele usa a palavra-chave `WITH` para dar um nome à CTE, opcionalmente lista as colunas e define sua consulta subjacente (`Query definition goes here`). A CTE pode então ser referenciada em uma consulta `SELECT` subsequente como se fosse uma tabela real.
    * **O que se Propõe a Fazer:** Simplificar consultas SQL complexas, dividindo-as em blocos menores e mais gerenciáveis.
    * **Importância num Pipeline de Dados:** CTEs melhoram drasticamente a **legibilidade, modularidade e depurabilidade** de consultas SQL complexas. Elas promovem a reutilização de lógica de consulta e ajudam a evitar código redundante, tornando-as uma ferramenta valiosa para engenheiros de dados e analistas.

11. **Função de Janela `RANK()` (Exemplo 3-46):**
    ```sql
    SELECT book_id,
           book_title,
           publication_year,
           RANK() OVER (ORDER BY publication_year) AS rank
    FROM books;
    ```
    * **Lógica do Código:** Esta consulta seleciona informações sobre livros e usa a função de janela `RANK() OVER (ORDER BY publication_year)` para atribuir uma classificação (rank) a cada livro com base no seu `publication_year`. A cláusula `ORDER BY` dentro de `OVER` define a ordem para o ranqueamento. Se houver livros com o mesmo ano de publicação, eles receberão o mesmo _rank_, e o _rank_ subsequente será pulado.
    * **O que se Propõe a Fazer:** Realizar cálculos de ranqueamento em um conjunto de linhas sem agrupar o _result set_ inteiro.
    * **Importância num Pipeline de Dados:** Funções de janela são extremamente poderosas para **análises avançadas**. Elas permitem cálculos complexos (como ranqueamento, somas cumulativas, médias móveis) sem a necessidade de _self-joins_ ou subconsultas complexas, otimizando o desempenho e a clareza da consulta analítica.

12. **Comando `PREDICT` (Dask-SQL - Exemplo 3-81):**
    ```sql
    ''' Predict: Test the recently created model by applying the predictions to the rows of the df— in this case assign each observation to a cluster'''
    c.sql("""
      SELECT * FROM PREDICT (
          MODEL clustering,
          SELECT sepallength, sepalwidth, petallength, petalwidth FROM iris
          LIMIT 100
      )
    """)
    ```
    * **Lógica do Código:** Este comando Dask-SQL usa a instrução `PREDICT` para aplicar um modelo de _machine learning_ previamente treinado (`MODEL clustering`) a um subconjunto de dados (as primeiras 100 linhas da tabela `iris`, selecionando features específicas). O resultado são as previsões do cluster para cada observação.
    * **O que se Propõe a Fazer:** Fazer previsões usando um modelo de ML treinado diretamente dentro do contexto SQL.
    * **Importância num Pipeline de Dados:** Demonstra a crescente integração de ML com SQL. Isso permite que engenheiros e cientistas de dados apliquem modelos preditivos em grandes volumes de dados usando uma linguagem familiar, facilitando a **democratização do ML em pipelines de dados** e a automatização da inferência.

13. **Instalação de Polars (Exemplo 3-60):**
    ```bash
    pip install polars
    ```
    * **Lógica do Código:** Comando de terminal para instalar a biblioteca Polars usando o gerenciador de pacotes `pip`.
    * **O que se Propõe a Fazer:** Instalar a biblioteca Polars para manipulação de dados em Python.
    * **Importância num Pipeline de Dados:** Polars é uma alternativa de **alta performance** ao Pandas para manipulação de dados em Python, escrita em Rust. É crucial para **escalar o processamento de dados** em Python, especialmente em cenários de _big data_ onde a velocidade e o uso eficiente da memória são críticos.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **fundamental para o engenheiro de dados** que precisa dominar o SQL em profundidade e explorar seu potencial além dos bancos de dados relacionais tradicionais. Ele capacita o engenheiro a:
* Escrever **consultas SQL eficientes e complexas** usando DDL, DML, CTEs e funções de janela.
* Compreender os **fundamentos dos bancos de dados** e suas interações.
* Alavancar ferramentas como **DuckDB, Polars e FugueSQL** para o **processamento de _big data_** e **otimizar o desempenho** dos _pipelines_.
* Explorar a integração do **SQL com _machine learning_**, abrindo novas portas para a orquestração de _workflows_ de MLOps.
Isso é vital para construir soluções de dados escaláveis, robustas e com alto valor analítico.

---

## Capítulo 4: Data Transformation with dbt

**Foco da Discussão do Capítulo:**
Este capítulo oferece uma **exploração detalhada do dbt**, aprofundando sua **filosofia de design, fluxo de dados e a estrutura de um projeto dbt**. Ele demonstra como o dbt transforma dados brutos em modelos estruturados e acessíveis, cobrindo a construção de modelos (staging, intermediate, mart), a parametrização via arquivos YAML, a documentação, os testes (genéricos e singulares) e a implantação de _jobs_ em produção, com ênfase nas **melhores práticas para o desenvolvimento e operações de dados**.

**Principais Conceitos do Capítulo:**
* **Propósito do dbt:** Ajuda a transformar dados em plataformas de dados de forma fácil e integrada usando SQL. Atua na fase de transformação do ELT, fornecendo controle de versão, documentação, testes e implantação automatizada. É uma das ferramentas que define o que os engenheiros de analytics fazem.
* **Filosofia de Design do dbt:**
    * **Abordagem _Code-centric_:** Define transformações de dados usando código SQL (e Jinja) em vez de GUIs ou scripts manuais. Promove colaboração, controle de versão e automação.
    * **Modularidade para Reutilização (_DRY_):** Permite criar componentes de código reutilizáveis (modelos, macros, testes) para reduzir a complexidade e a sobrecarga computacional.
    * **_Incremental Builds_:** Atualiza apenas as partes afetadas do _pipeline_, acelerando o desenvolvimento e reduzindo o tempo de processamento.
    * **Documentação como Código:** Descrições, explicações e metadados armazenados junto ao código do projeto, facilitando a compreensão e colaboração.
    * **Qualidade, Teste e Validação de Dados:** Framework de teste que permite definir verificações de qualidade e regras de validação para garantir a confiabilidade dos dados.
    * **Integração com Controle de Versão (Git):** Integração para desenvolvimento colaborativo, rastreamento de mudanças e capacidade de reverter alterações.
    * **Integração Nativa com Plataformas de Dados:** Projetado para funcionar perfeitamente com plataformas como Snowflake, BigQuery e Redshift.
    * **_Open Source_ e Extensível:** Comunidade ativa, permite criar macros e pacotes personalizados.
    * **Separação de Transformação e Carregamento:** O dbt transforma os dados e o carrega na plataforma de dados.
* **Fluxo de Dados dbt:** Ilustra onde o dbt se encaixa no panorama geral do fluxo de dados, transformando dados de diversas fontes (BigQuery, Snowflake, Databricks, Redshift). Pode ser usado via **dbt Cloud** ou **dbt Core (CLI)**.
* **dbt Cloud:**
    * Versão gerenciada do dbt, oferece IDE, agendamento de _jobs_, monitoramento, logs, métricas em tempo real e recursos avançados de colaboração (controle de versão, testes, documentação).
    * **Configuração com BigQuery e GitHub:** Guia passo a passo para conectar o dbt Cloud a uma conta BigQuery (via arquivo JSON de chave de serviço) e a um repositório GitHub para controle de versão.
    * **Melhores Práticas Git:** Commits frequentes e significativos, estratégia de _branching_, `pull` antes de `push`, histórico limpo, uso de tags, colaboração, conhecimento para desfazer mudanças, documentação e _backups_.
    * **UI do dbt Cloud:** Abas para Desenvolvimento (IDE), Implantação (histórico de _runs_, _jobs_, ambientes, _source freshness_ do _snapshot_), Documentação, Configurações de Perfil e Notificações.
    * **dbt Cloud IDE:** Editor de texto, _file explorer_, controles Git, janela de informações (Preview, Compile, Build, Lineage para rastrear dependências) e linha de comando para executar comandos dbt.
* **Estrutura de um Projeto dbt:** Um diretório organizado com pastas e arquivos (`analyses`, `dbt_packages`, `logs`, `macros`, `models`, `seeds`, `snapshots`, `target`, `tests`, `.gitignore`, `dbt_project.yml`, `README.md`) seguindo padrões e convenções de nomenclatura.
    * **`dbt_project.yml`:** Arquivo de configuração central do projeto. Define nome, versão, `config-version`, _profile_ (conexão com BD), caminhos de pastas, `clean-targets` e configurações padrão dos modelos (ex: materialização padrão).
    * **`packages.yml`:** Declara pacotes dbt de terceiros a serem instalados.
    * **`profiles.yml`:** (Relevante para dbt Core CLI) Configurações de conexão com plataformas de dados, definindo `target`, `type` e detalhes de conexão específicos do banco de dados.
    * **Arquivos YAML:** Usados para definir propriedades e algumas configurações de componentes do dbt (modelos, _snapshots_, _seeds_, testes, _sources_, projeto). **Melhores práticas:** equilibrar centralização e tamanho do arquivo, usar "config por pasta", separar configurações de _source_ e modelo, usar arquivos Markdown para documentação.
* **Modelos dbt:** Arquivos SQL com `SELECT` statements que transformam dados brutos. O nome do modelo indica o nome da futura tabela ou _view_. A modelagem de dados é crucial para construir modelos adequados.
    * **Camadas de Modelagem:** _Staging_, _Intermediate_ e _Marts_ (ou _Core_). O capítulo detalha a criação de um caso de uso para analisar pedidos por cliente, incluindo o total pago por pedido bem-sucedido e por tipo de pagamento.
    * **`{{ ref() }}`:** Função Jinja para referenciar modelos _upstream_, construindo dependências e gráficos de linhagem. Essencial para reutilização de código e garantia de consistência.
    * **`{{ source() }}`:** Função Jinja para referenciar fontes de dados brutas declaradas em arquivos YAML. Funciona como `ref()`, mas para dados de origem.
    * **Materializações:** Estratégias para persistir modelos dbt na plataforma de dados. O padrão é `view`, mas pode ser configurado como `table`.
* **Sources:** Representam os dados brutos disponíveis na plataforma de dados, capturados por uma ferramenta EL genérica. São definidos em arquivos YAML e podem ser visualizados em gráficos de linhagem.
    * **_Source Freshness_:** Teste para verificar quão atualizados estão os dados de origem. Configura um campo de _timestamp_ de carregamento (`loaded_at_field`) e limites (`warn_after`, `error_after`) para disparar avisos ou erros.
* **Testes dbt:** Assertions sobre seus dados para garantir acurácia e confiabilidade. Foco em verificação de padrões, não em testes de lógica fina (como testes unitários em _software development_).
    * **Testes Genéricos:** Simples e altamente escaláveis, definidos em arquivos YAML. Incluem `unique` (valores únicos), `not_null` (ausência de nulos), `accepted_values` (valores em uma lista predefinida) e `relationships` (integridade referencial entre modelos/colunas). Podem ser aplicados a modelos e _sources_.
    * **Testes Singulares:** Definidos em arquivos `.sql` na pasta `tests`. Usados para testar atributos específicos ou condições que testes genéricos não abrangem (ex: `total_amount` não-negativo). Podem ser aplicados a modelos e _sources_.
    * **Comandos de Teste:** `dbt test` (executa todos os testes), `dbt test --select test_type:singular` (apenas testes singulares), `dbt test --select <model_name>` (testes de um modelo específico), `dbt test --select <test_name>` (um teste específico), `dbt test --select source:<source_name>` (testes de uma fonte específica).
* **Analyses Folder:** Para consultas _ad hoc_, auditoria, treinamento ou refatoração. Não são executadas por `dbt run`, mas usam Jinja e controle de versão, podendo ser compiladas com `dbt compile`.
* **Seeds:** Arquivos CSV com pequenas quantidades de dados não voláteis, materializados como tabelas ao executar `dbt seed`. Usados para tabelas de _lookup_ ou dados de teste.
* **Documentação:** Essencial para colaboração, _onboarding_ e rastreabilidade. Pode ser feita diretamente em arquivos YAML (propriedade `description`) ou usando blocos de documentação (`doc blocks`) em arquivos Markdown (`.md`) referenciados via Jinja. Gerada com `dbt docs generate`.
* **_Jobs_ e Implantação:** Configurar e agendar _jobs_ no dbt Cloud para automatizar a execução de comandos dbt (`dbt build`, `dbt test`, `dbt docs generate`, `dbt source freshness`) em ambientes de produção. É parte do processo CI/CD.
    * **Ambiente de Implantação:** Configurar um _schema_ ou _dataset_ dedicado para produção.
    * **Triggers:** Agendamento, Webhooks, API.
* **Comandos dbt e Sintaxe de Seleção:**
    * `dbt run`: Executa transformações definidas nos modelos.
    * `dbt test`: Executa testes nos modelos.
    * `dbt docs generate`: Gera a documentação do projeto.
    * `dbt build`: Compila e executa todos os modelos, testes, _seeds_ e _snapshots_.
    * Outros comandos: `dbt seed`, `dbt clean`, `dbt snapshot`, `dbt deps`, `dbt source snapshot-freshness`, `dbt debug`, `dbt parse`, `dbt clone`, `dbt init`, `dbt retry`, `dbt ls`.
    * **Sintaxe de Seleção:** Permite especificar quais recursos incluir ou excluir ao executar comandos dbt, usando curinga `*`, nomes de modelos, tags, dependências (`+`, `-` para _upstream_ e _downstream_) e pacotes.

**Exemplos de Código:**

1.  **Testando a Conexão BigQuery no dbt Cloud (Exemplo 4-1):**
    ```sql
    select * from `dbt-tutorial.jaffle_shop.customers`;
    select * from `dbt-tutorial.jaffle_shop.orders`;
    select * from `dbt-tutorial.stripe.payment`;
    ```
    * **Lógica do Código:** Este _snippet_ consiste em três consultas `SELECT` básicas que acessam tabelas de _datasets_ públicos do dbt ( `jaffle_shop.customers`, `jaffle_shop.orders`, `stripe.payment`) no BigQuery.
    * **O que se Propõe a Fazer:** Validar a conexão bem-sucedida entre o dbt Cloud e o BigQuery após a configuração inicial.
    * **Importância num Pipeline de Dados:** É um passo inicial essencial para **confirmar a conectividade** entre o dbt e a plataforma de dados. Garante que o dbt possa acessar os dados brutos, o que é o ponto de partida para qualquer pipeline de engenharia de analytics.

2.  **`git commit` e `git add` (Tabela 4-1):**
    ```bash
    git add . # ou git add <caminho/para/diretorio/>
    git commit -m "Commit message here"
    ```
    * **Lógica do Código:** O comando `git add .` adiciona todas as mudanças no diretório de trabalho atual para a área de _staging_ do Git. O comando `git commit -m "Commit message here"` então cria um novo _snapshot_ dessas mudanças no repositório local, junto com uma mensagem descritiva.
    * **O que se Propõe a Fazer:** Salvar e versionar as alterações feitas no código-fonte do _pipeline_ ETL de forma incremental e controlada.
    * **Importância num Pipeline de Dados:** Esses comandos são fundamentais para o **controle de versão (Git)**, que é a base do desenvolvimento colaborativo e dos pipelines CI/CD. Permitem rastrear o histórico de alterações, reverter para versões anteriores, auditar o código e organizar o trabalho em equipe.

3.  **`git push` (Tabela 4-1):**
    ```bash
    git push <origin branch_name>
    ```
    * **Lógica do Código:** Este comando envia as mudanças commitadas do repositório Git local para o repositório remoto (ex: GitHub, AWS CodeCommit) na _branch_ especificada.
    * **O que se Propõe a Fazer:** Publicar as mudanças de código para o sistema de controle de versão remoto.
    * **Importância num Pipeline de Dados:** O `git push` é o gatilho para a **integração contínua (CI)** e a **implantação contínua (CD)**. Ele garante que o código mais recente seja compartilhado com a equipe e que os pipelines de CI/CD (como aqueles configurados na AWS) sejam acionados automaticamente para testar e implantar as novas funcionalidades.

4.  **Configuração Padrão de Materialização em `dbt_project.yml` (Exemplo 4-5):**
    ```yaml
    models:
      dbt_analytics_engineer_book:
        staging:
          materialized: view
    ```
    * **Lógica do Código:** Este bloco de configuração dentro do arquivo `dbt_project.yml` define que todos os modelos localizados na pasta `staging` do projeto `dbt_analytics_engineer_book` serão materializados como `views` por padrão.
    * **O que se Propõe a Fazer:** Configurar o tipo de materialização padrão para um grupo específico de modelos dentro do projeto dbt.
    * **Importância num Pipeline de Dados:** A definição de materializações padrão é uma **melhor prática para otimização de custo e desempenho**. _Views_ são ideais para a camada de _staging_ por sua flexibilidade e baixo custo de armazenamento (não armazenam dados fisicamente), enquanto tabelas são usadas para _marts_ finais. Centraliza a configuração e promove a consistência em todo o projeto.

5.  **Modelo de Staging com `source()` no dbt (`stg_stripe_order_payments.sql` - Exemplo 4-22):**
    ```sql
    -- REPLACE IT IN stg_stripe_order_payments.sql
    select
        id as payment_id,
        orderid as order_id,
        paymentmethod as payment_method,
        case
            when paymentmethod in ('stripe'
                                , 'paypal'
                                , 'credit_card'
                                , 'gift_card')
            then 'credit'
            else 'cash'
        end as payment_type,
        status,
        amount,
        case
            when status = 'success'
            then true
            else false
        end as is_completed_payment,
        created as created_date
    from {{ source('stripe', 'payment') }}
    ```
    * **Lógica do Código:** Este modelo SQL dbt seleciona e transforma colunas da fonte de dados `stripe.payment` (referenciada por `{{ source('stripe', 'payment') }}`) para criar um modelo de _staging_ para pagamentos. Ele renomeia colunas, categoriza o `payment_type` e cria um indicador booleano `is_completed_payment`.
    * **O que se Propõe a Fazer:** Limpar e padronizar dados brutos de pagamentos para uso _downstream_ nos modelos de transformação.
    * **Importância num Pipeline de Dados:** Modelos de _staging_ são a **primeira camada de transformação**, onde a qualidade dos dados é aprimorada e as colunas são padronizadas. O uso da função `{{ source() }}` torna o código mais modular, desacopla o modelo da referência direta à tabela bruta e facilita o rastreamento da linhagem.

6.  **Configuração de _Source Freshness_ em `_jaffle_shop_sources.yml` (Exemplo 4-23):**
    ```yaml
    version: 2
    sources:
      - name: jaffle_shop
        database: dbt-tutorial
        schema: jaffle_shop
        tables:
          - name: customers
          - name: orders
            loaded_at_field: _etl_loaded_at
            freshness:
              warn_after: {count: 12, period: hour}
              error_after: {count: 24, period: hour}
    ```
    * **Lógica do Código:** Este arquivo YAML define a fonte `jaffle_shop.orders` e configura um teste de _source freshness_ para ela. Ele especifica que o campo `_etl_loaded_at` deve ser monitorado. Um aviso (`warn_after`) será disparado se os dados tiverem mais de 12 horas, e um erro (`error_after`) se tiverem mais de 24 horas.
    * **O que se Propõe a Fazer:** Garantir que os dados brutos de origem estejam atualizados, com alertas configuráveis para atrasos.
    * **Importância num Pipeline de Dados:** O teste de _source freshness_ é crucial para a **qualidade e confiabilidade dos dados**. Dados desatualizados podem levar a _insights_ incorretos e decisões de negócio falhas. Este teste automatizado fornece **observabilidade e alertas proativos**, essenciais para manter a saúde do pipeline.

7.  **Testes Genéricos em `_jaffle_shop_models.yml` (Exemplo 4-24):**
    ```yaml
    version: 2
    models:
      - name: stg_jaffle_shop_customers
        config:
          materialized: view
        columns:
          - name: customer_id
            tests:
              - unique
              - not_null
      - name: stg_jaffle_shop_orders
        config:
          materialized: view
        columns:
          - name: order_id
            tests:
              - unique
              - not_null
          - name: status
            tests:
              - accepted_values:
                  values:
                    - completed
                    - shipped
                    - returned
                    - placed
                    - return_pending # Correção de um valor ausente no exemplo do livro
          - name: customer_id
            tests:
              - relationships:
                  to: ref('stg_jaffle_shop_customers')
                  field: customer_id
    ```
    * **Lógica do Código:** Este arquivo YAML define vários testes genéricos para as colunas dos modelos de _staging_ `stg_jaffle_shop_customers` e `stg_jaffle_shop_orders`. Inclui `unique` e `not_null` para `customer_id` e `order_id`, `accepted_values` para o `status` do pedido (verificando se os valores estão em uma lista predefinida) e `relationships` para garantir a integridade referencial entre `customer_id` nos pedidos e clientes.
    * **O que se Propõe a Fazer:** Validar a qualidade e integridade dos dados nos modelos dbt usando testes automatizados.
    * **Importância num Pipeline de Dados:** Testes genéricos são altamente **escaláveis e essenciais para a qualidade dos dados**. Eles verificam padrões comuns e regras de negócio, construindo confiança nos modelos analíticos e capturando erros cedo no ciclo de desenvolvimento, antes que afetem _insights_ de negócio.

8.  **Teste Singular em `.sql` (`assert_total_payment_amount_is_positive.sql` - Exemplo 4-25):**
    ```sql
    select
        order_id,
        sum(total_amount) as total_amount
    from {{ ref('int_payment_type_amount_per_order') }}
    group by 1
    having total_amount < 0
    ```
    * **Lógica do Código:** Este arquivo SQL define um teste singular que consulta o modelo `int_payment_type_amount_per_order`. Ele agrupa os dados por `order_id` e soma o `total_amount` para cada pedido. O teste falhará (`having total_amount < 0`) se for encontrado algum pedido com um `total_amount` negativo, o que seria uma anomalia.
    * **O que se Propõe a Fazer:** Escrever um teste específico para uma regra de negócio que os testes genéricos podem não cobrir, garantindo que o `total_amount` de pagamentos seja sempre positivo.
    * **Importância num Pipeline de Dados:** Testes singulares permitem **validar regras de negócio específicas e complexas**, garantindo a **acurácia de métricas críticas** e prevenindo dados inconsistentes em camadas mais avançadas do _data warehouse_. Eles são um complemento valioso aos testes genéricos.

9.  **Análise SQL na pasta `analyses` (`most_valuable_customers.sql` - Exemplo 4-28):**
    ```sql
    with fct_orders as (
        select * from {{ ref('fct_orders')}}
    ),
    dim_customers as  (
        select * from {{ ref('dim_customers' )}}
    )
    select
        cust.customer_id,
        cust.first_name,
        SUM(total_amount) as global_paid_amount
    from fct_orders as ord
    left join dim_customers as cust ON ord.customer_id = cust.customer_id
    where ord.is_order_completed = 1
    group by cust.customer_id, first_name
    order by 3 desc
    limit 10
    ```
    * **Lógica do Código:** Este arquivo SQL, localizado na pasta `analyses`, combina dados dos modelos `fct_orders` e `dim_customers` (referenciados por `{{ ref() }}`). Ele filtra por pedidos concluídos (`is_order_completed = 1`), agrupa por cliente, calcula a soma total paga (`global_paid_amount`) e retorna os 10 clientes com maior valor gasto, ordenados de forma decrescente.
    * **O que se Propõe a Fazer:** Realizar uma consulta _ad hoc_, de auditoria ou de treinamento para identificar os clientes mais valiosos, sem necessariamente criar um modelo persistente no banco de dados.
    * **Importância num Pipeline de Dados:** A pasta `analyses` permite que analistas e engenheiros de dados **experimentem e validem consultas complexas** usando a infraestrutura dbt (Jinja, controle de versão) sem impactar o _data warehouse_ de produção com modelos intermediários desnecessários. É uma ferramenta para gerar _insights_ rápidos ou validar lógicas.

10. **Seed CSV (`customer_range_per_paid_amount.csv` - Exemplo 4-29):**
    ```csv
    min_range,max_range,classification
    0,9.999,Regular
    10,29.999,Bronze
    30,49.999,Silver
    50,9999999,Gold
    ```
    * **Lógica do Código:** Este é um arquivo CSV simples que define faixas de valores (`min_range`, `max_range`) e suas classificações correspondentes (`classification`). Ao executar `dbt seed`, este arquivo é materializado como uma tabela no banco de dados.
    * **O que se Propõe a Fazer:** Fornecer uma tabela de _lookup_ estática ou de dados de referência que raramente muda e não provém de sistemas de origem.
    * **Importância num Pipeline de Dados:** Seeds são ideais para **dados de configuração ou mapeamento** que precisam ser incluídos no _data warehouse_ sem passar por um processo EL extenso. Eles simplificam a inclusão de dados auxiliares no _data warehouse_ e podem ser referenciados em modelos dbt usando a função `{{ ref() }}`.

11. **Doc Block em Markdown (`_core_doc.md` - Exemplo 4-32):**
    ```markdown
    {% docs is_order_completed_docblock %}

    Binary data which states if the order is completed or not, considering the order status. It can contain one of the following values:

    | is_order_completed | definition                                                |
    |--------------------|-----------------------------------------------------------|
    | 0                  | An order that is not completed yet, based on its status   |
    | 1                  | An order which was completed already, based on its status |

    {% enddocs %}
    ```
    * **Lógica do Código:** Este _snippet_ define um bloco de documentação nomeado `is_order_completed_docblock` dentro de um arquivo Markdown. Ele descreve o significado e os valores possíveis de um campo binário, incluindo uma tabela para clareza. Este bloco pode ser referenciado em arquivos YAML de modelos usando `{{ doc('is_order_completed_docblock') }}`.
    * **O que se Propõe a Fazer:** Criar documentação rica, detalhada e reutilizável para colunas de modelos.
    * **Importância num Pipeline de Dados:** A **documentação como código** é uma **melhor prática crucial** para engenheiros de analytics. Doc blocks permitem que eles forneçam **descrições detalhadas e contextuais** para ativos de dados, melhorando a compreensão para todos os usuários, facilitando o _onboarding_ e a governança de dados.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **indispensável para o engenheiro de dados** que constrói e opera soluções de dados com dbt. Ele fornece uma compreensão profunda da **filosofia e estrutura do dbt**, capacitando o engenheiro a:
* **Projetar e implementar modelos de dados modulares e escaláveis** usando as camadas de _staging_, _intermediate_ e _marts_.
* **Garantir a qualidade e a acurácia dos dados** através de testes genéricos e singulares.
* **Gerenciar o _data flow_ e a linhagem** com `ref()` e `source()`.
* **Automatizar a implantação e o monitoramento** de _pipelines_ em produção via dbt Cloud _jobs_, aplicando as melhores práticas de CI/CD.
Dominar esses conceitos é fundamental para entregar dados confiáveis e acionáveis.

---

## Capítulo 5: dbt Advanced Topics

**Foco da Discussão do Capítulo:**
Este capítulo aprofunda em **tópicos avançados do dbt**, indo além dos fundamentos para otimizar a eficiência e a escalabilidade dos _pipelines_. Ele explora a gama de **materializações de modelos** (tabelas, views, efêmeros, incrementais, views materializadas, _snapshots_), o poder do **SQL dinâmico com Jinja**, a reutilização de código através de **macros SQL** e **dbt packages**, e o papel estratégico do **_dbt Semantic Layer_** para democratizar o acesso a métricas de negócio.

**Principais Conceitos do Capítulo:**
* **Materializações de Modelos:** Estratégias para persistir modelos dbt na plataforma de dados, otimizando desempenho e escalabilidade ao reduzir a necessidade de recalcular queries.
    * **`table`:** Modelo materializado como uma tabela física no banco de dados. Lento para construir, mas rápido para consultar. Recomendado para grandes volumes de dados e modelos na camada de _marts_.
    * **`view`:** Modelo materializado como uma _view_ virtual. Rápido para construir (armazena apenas a query), mas pode ser lento para consultar (executa a query em tempo de execução). Usado para simplificar consultas ou prover segurança.
    * **`ephemeral`:** Modelos não materializados que são inseridos como Common Table Expressions (CTEs) em modelos _downstream_. Não persistem na plataforma de dados, mas ajudam na modularidade e refatoração do código SQL.
    * **`incremental`:** Processa apenas dados novos ou alterados, em vez de todo o conjunto de dados. Reduz consumo de recursos e tempo de processamento. Requer um campo de _timestamp_ (`_etl_loaded_at`) e lógica Jinja (`{% if is_incremental() %}`). Suporta estratégias como `merge`, `append`, `insert_overwrite`.
    * **_Materialized Views_:** Objetos de banco de dados que armazenam o resultado de uma query como uma tabela física, periodicamente atualizada. Podem ser uma alternativa aos modelos incrementais do dbt, delegando a lógica incremental para a plataforma de dados.
    * **_Snapshots_:** Copiam um _dataset_ em um ponto específico no tempo, usados para rastrear o histórico de mudanças em tabelas que são continuamente atualizadas (Slowly Changing Dimensions - SCDs). Configurados com `unique_key`, `strategy` (`timestamp` ou `check`), `updated_at`, `target_schema`. Adicionam colunas `dbt_scd_id`, `dbt_updated_at`, `dbt_valid_from`, `dbt_valid_to`.
* **SQL Dinâmico com Jinja:** Linguagem de _templating_ para Python, amplamente utilizada em dbt para criar SQL dinâmico. Permite o uso de variáveis, expressões, loops e condicionais.
    * **Delimitadores:** `{% ... %}` para statements (variáveis, loops, condicionais), `{{ ... }}` para expressões (imprimir valores), `{# ... #}` para comentários.
    * **Controle de Whitespaces:** Uso de `-%}` ou `{%-` para remover espaços em branco.
    * **Casos de Uso:** Limitar a quantidade de dados em ambientes de desenvolvimento, gerar métricas dinamicamente (ex: calcular montantes para diferentes tipos de pagamento de forma escalável).
* **Macros SQL:** Peças de código reutilizáveis (análogas a funções em outras linguagens) para automatizar tarefas repetitivas. Definidas em arquivos `.sql` na pasta `macros` usando Jinja.
    * **Criação:** Recebem argumentos, usam funções Jinja como `return()` e `run_query()` para executar SQL e obter resultados dinamicamente.
    * **Modularidade e Reutilização:** Podem ser genéricas (ex: `get_column_values`) e chamadas por macros especializadas (ex: `get_payment_types`) para máxima reutilização.
    * **Casos de Uso:** Centralizar a configuração de tipos de pagamento, gerar listas de valores distintos do banco de dados, limitar _datasets_ em ambientes de desenvolvimento ou teste.
    * **Customização de Macros Core:** O dbt permite personalizar macros internas (ex: `generate_schema_name`) para alinhar com as convenções do projeto.
* **dbt Packages:** Projetos dbt autônomos com modelos e macros reutilizáveis que podem ser instalados em outros projetos via `packages.yml` e `dbt deps`.
    * **`dbt_utils`:** Um pacote popular da dbt Labs com funções e macros de utilidade (ex: `date_spine()`, `safe_divide()`) para tarefas comuns de modelagem de dados.
* **_dbt Semantic Layer_:** Uma camada de abstração lógica que atua como uma ponte entre dados brutos e _insights_ significativos. Simplifica estruturas de dados complexas, facilita a compreensão comum dos dados e garante integridade e confiabilidade.
    * **Componentes:** _Semantic Models_ (definem entidades, dimensões e medidas) e _Metrics_ (definem as métricas a serem calculadas).
    * **_Semantic Engine_ (MetricFlow):** Interpreta o modelo semântico e gera consultas analíticas padronizadas e reutilizáveis. Análogo ao _dbt Documentation Engine_.
    * **Uso:** Definir métricas como `order_total` com filtros (ex: `is_order_completed = true`), que são traduzidas em SQL por MetricFlow.

**Exemplos de Código:**

1.  **Configuração de Materialização `table` (`config` - Exemplo 4-12):**
    ```sql
    {{
       config(
          materialized='table'
       )
    }}
    ```
    * **Lógica do Código:** Este bloco de configuração Jinja é colocado no início de um arquivo SQL de modelo dbt e instrui o dbt a materializar o resultado da consulta subjacente como uma tabela física (`table`) no banco de dados.
    * **O que se Propõe a Fazer:** Persistir o modelo dbt como uma tabela física no banco de dados, em vez do padrão que é uma _view_.
    * **Importância num Pipeline de Dados:** A escolha da materialização impacta diretamente o **desempenho e o custo** do _data warehouse_. A materialização `table` é adequada para modelos finais na camada de _marts_ que são consultados frequentemente e precisam de alta performance, pois armazenam os dados em disco para acesso rápido.

2.  **Materialização Ephemeral (Figura 5-4):**
    * **Lógica do Código:** A Figura 5-4 ilustra o código SQL compilado de um modelo _downstream_ (`dim_customer`). A diferença entre a materialização `view` (topo) e `ephemeral` (inferior) do modelo de _staging_ `stg_jaffle_shop_customers` é que, no caso efêmero, o código SQL do modelo de _staging_ é diretamente incluído como uma CTE na consulta do `dim_customer`, em vez de ser referenciado como uma _view_ ou tabela separada.
    * **O que se Propõe a Fazer:** Demonstrar como modelos efêmeros funcionam: seu código é inserido diretamente em modelos _downstream_ como CTEs temporárias e não são persistidos como objetos separados no banco de dados.
    * **Importância num Pipeline de Dados:** Modelos efêmeros são uma **melhor prática de refatoração** para organizar a lógica de transformação sem criar objetos permanentes desnecessários no banco de dados. Eles reduzem a complexidade do esquema, podem otimizar algumas execuções ao evitar a leitura de objetos intermediários e são ideais para transformações internas de um fluxo maior.

3.  **Configuração de Modelo Incremental em `_jaffle_shop_orders.yml` (Exemplo 5-1):**
    ```yaml
    version: 2
    models:
      - name: stg_jaffle_shop_orders
        config:
          materialized: incremental
          incremental_strategy: merge
          unique_key: order_id
    ```
    * **Lógica do Código:** Este arquivo YAML configura o modelo `stg_jaffle_shop_orders` para ser `incremental`. Ele especifica a estratégia `merge` (que mescla novos dados com os existentes, atualizando correspondências e inserindo novos registros) e define `order_id` como a `unique_key` para identificar registros.
    * **O que se Propõe a Fazer:** Configurar um modelo dbt para cargas de dados incrementais, onde apenas novos ou alterados dados são processados.
    * **Importância num Pipeline de Dados:** Modelos incrementais são **cruciais para eficiência e custo** em pipelines que lidam com grandes volumes de dados que mudam frequentemente. Eles reduzem o tempo de execução e o consumo de recursos ao processar apenas as alterações, mantendo os dados atualizados com menor impacto computacional.

4.  **Lógica Jinja para Modelo Incremental (Exemplo 5-2):**
    ```sql
    select
        id as order_id,
        user_id as customer_id,
        order_date,
        status,
        _etl_loaded_at
    from {{ source('jaffle_shop', 'orders') }}

    {% if is_incremental() %}

    where _etl_loaded_at >= (select max(_etl_loaded_at) from {{ this }} )

    {% endif %}
    ```
    * **Lógica do Código:** Este modelo SQL usa um bloco condicional Jinja `{% if is_incremental() %}`. A macro `is_incremental()` retorna `true` se o modelo está sendo executado em modo incremental e já existe no banco de dados. Se `true`, uma cláusula `WHERE` é adicionada para filtrar registros onde `_etl_loaded_at` é maior ou igual ao `max(_etl_loaded_at)` da tabela atual (`{{ this }}`), garantindo que apenas os dados mais recentes sejam processados.
    * **O que se Propõe a Fazer:** Implementar a lógica de filtragem de dados novos para um modelo incremental.
    * **Importância num Pipeline de Dados:** Esta lógica Jinja é o cerne dos modelos incrementais no dbt. Ela garante que apenas os dados realmente novos sejam processados, **otimizando drasticamente o desempenho do pipeline, economizando recursos** de computação e mantendo a consistência dos dados carregados.

5.  **Criação de Snapshot (`snap_order_status_transition.sql` - Exemplo 5-5):**
    ```sql
    {% snapshot orders_status_snapshot %}

    {{
        config(
          target_schema='snapshots',
          unique_key='id',
          strategy='timestamp',
          updated_at='_etl_loaded_at',
        )
    }}
    select * from {{ source('jaffle_shop', 'orders') }}

    {% endsnapshot %}
    ```
    * **Lógica do Código:** Este arquivo SQL define um _snapshot_ dbt para a fonte `jaffle_shop.orders`. O bloco `config` especifica o `target_schema` como `snapshots`, `unique_key` como `id`, `strategy` como `timestamp` (para rastrear mudanças com base em um _timestamp_ de atualização) e `updated_at` como `_etl_loaded_at`.
    * **O que se Propõe a Fazer:** Capturar e versionar as mudanças de dados em uma tabela de forma contínua, criando um histórico de todas as alterações ao longo do tempo.
    * **Importância num Pipeline de Dados:** Snapshots são vitais para **rastrear Slowly Changing Dimensions (SCDs)**, permitindo análises históricas e auditoria. Eles fornecem uma visão "time-travel" dos dados, essencial para **análises de tendência, conformidade** e reprocessamento de dados.

6.  **Jinja para Limitar Dados em Ambiente de Desenvolvimento (Exemplo 5-6):**
    ```sql
    select * from {{ ref('fct_orders' )}}
    -- limit the amount of data queried in dev
    {% if target.name != 'prod' %}
    where order_date > DATE_SUB(CURRENT_DATE(), INTERVAL 3 MONTH)
    {% endif %}
    ```
    * **Lógica do Código:** Este _snippet_ SQL usa um bloco condicional Jinja (`{% if target.name != 'prod' %}`) para adicionar uma cláusula `WHERE` que filtra os dados por `order_date` para os últimos 3 meses. Esta filtragem é aplicada **apenas** se o ambiente de destino (`target.name`) não for 'prod'.
    * **O que se Propõe a Fazer:** Reduzir automaticamente o volume de dados processados em ambientes de desenvolvimento e teste para otimizar o desempenho.
    * **Importância num Pipeline de Dados:** Esta macro é uma **melhor prática crucial para otimização de custo e desempenho** em desenvolvimento. Ao limitar o volume de dados, acelera os ciclos de desenvolvimento e teste, evitando sobrecarga desnecessária na plataforma de dados, o que é fundamental para a agilidade do engenheiro de dados.

7.  **Jinja Dinâmico para Geração de Métricas (Exemplo 5-7):**
    ```sql
    {# declaration of payment_type variable. Add here if a new one appears #}
    {%- set payment_types= ['cash','credit'] -%}
    with
    payments as (
    select * from {{ ref('stg_stripe_order_payments') }}
    ),
    pivot_and_aggregate_payments_to_order_grain as (
    select
          order_id,
          {% for payment_type in payment_types -%}
    sum(
              case
                 when payment_type = '{{ payment_type }}' and
                      status = 'success'
                 then amount
                 else 0
              end
           ) as {{ payment_type }}_amount,
          {%- endfor %}
    sum(case when status = 'success' then amount end) as total_amount
    from payments
    group by 1
    )
    select * from pivot_and_aggregate_payments_to_order_grain
    ```
    * **Lógica do Código:** Este modelo dbt declara uma variável Jinja `payment_types`. Em seguida, usa um loop `{% for %}` para gerar dinamicamente múltiplas colunas de soma condicionais (`cash_amount`, `credit_amount`), uma para cada tipo de pagamento na lista `payment_types`. Isso evita a repetição de código SQL para cada tipo de pagamento.
    * **O que se Propõe a Fazer:** Criar código SQL mais dinâmico, escalável e DRY (_Don't Repeat Yourself_) para transformações que dependem de listas de valores.
    * **Importância num Pipeline de Dados:** Macros Jinja para SQL dinâmico são **poderosas para modularidade e escalabilidade**. Elas permitem que o código do pipeline se adapte a novas categorias ou requisitos de negócio de forma automática, reduzindo a manutenção manual e o risco de erros na lógica de transformação.

8.  **Macro para Obter Tipos de Pagamento Dinamicamente (Exemplo 5-14):**
    ```sql
    {% macro get_payment_types() %}
        {% set payment_type_query %}
        select
            distinct payment_type
        from {{ ref('stg_stripe_order_payments') }}
        order by 1
        {% endset %}

    {% set results = run_query(payment_type_query) %}

    {% if execute %}
        {# Return the first column #}
        {% set results_list = results.columns.values() %}
        {% else %}
        {% set results_list = [] %}
        {% endif %}

    {{ return(results_list) }}
    {% endmacro %}
    ```
    * **Lógica do Código:** Esta macro `get_payment_types()` executa uma subconsulta SQL (`select distinct payment_type...`) para obter dinamicamente todos os tipos de pagamento únicos do modelo de _staging_ `stg_stripe_order_payments`. Ela utiliza a função `run_query()` para executar o SQL dentro do contexto Jinja e `results.columns.values()` para extrair os valores distintos da primeira coluna do resultado.
    * **O que se Propõe a Fazer:** Centralizar e automatizar a obtenção de listas de valores dinamicamente do banco de dados para uso em outros modelos, eliminando a necessidade de listas codificadas manualmente.
    * **Importância num Pipeline de Dados:** Macros dinâmicas são uma **melhor prática de reusabilidade e escalabilidade**. Elas garantem que o pipeline sempre reflita os dados mais recentes, adaptando-se a mudanças sem intervenção manual, o que é vital para a consistência e a baixa manutenção do código.

9.  **Macro de Uso Geral (`get_column_values`) e Especializadas (Exemplo 5-15):**
    ```sql
    {# Generic macro to give a column name and table, outputs the distinct fields of the given column name #}
    {% macro get_column_values(column_name, table_name) %}
    {# ... dynamic query and return logic ... #}
    {{ return(results_list) }}
    {% endmacro %}

    {# Macro to get the distinct payment_types #}
    {% macro get_payment_types() %}
    {{ return(get_column_values('payment_type', ref('stg_stripe_order_payments'))) }}
    {% endmacro %}

    {# Macro to get the distinct payment_methods #}
    {% macro get_payment_methods() %}
    {{ return(get_column_values('payment_method', ref('stg_stripe_order_payments'))) }}
    {% endmacro %}
    ```
    * **Lógica do Código:** Este _snippet_ demonstra uma hierarquia de macros. Primeiro, uma macro genérica `get_column_values()` é definida para obter valores distintos de qualquer coluna em qualquer tabela. Em seguida, duas macros especializadas (`get_payment_types()`, `get_payment_methods()`) chamam a macro genérica `get_column_values()` com parâmetros específicos, tornando-as mais fáceis de usar em contextos de negócio.
    * **O que se Propõe a Fazer:** Promover a reutilização máxima de código, encapsulando a lógica comum em macros genéricas e criando macros especializadas mais fáceis de usar para cenários específicos.
    * **Importância num Pipeline de Dados:** A hierarquia de macros é uma **prática de engenharia de _software_** que aumenta significativamente a modularidade e a manutenibilidade do código dbt. Permite que mudanças na lógica base sejam feitas em um único lugar e se propaguem por todo o projeto, reduzindo a dívida técnica.

10. **Macro para Limitar _Dataset_ em Ambiente de Desenvolvimento (Exemplo 5-16, 5-17):**
    ```sql
    {# Macro that considering the target name, limits the amount of data queried for the nbr_months_of_data defined #}
    {% macro limit_dataset_if_not_deploy_env(column_name, nbr_months_of_data) %}
    -- limit the amount of data queried if not in the deploy environment.
    {% if target.name != 'deploy' %}
    where {{ column_name }} > DATE_SUB(CURRENT_DATE(), INTERVAL {{ nbr_months_of_data }}  MONTH)
    {% endif %}
    {% endmacro %}

    -- Chamada no modelo fct_orders.sql
    from orders as ord
    left join payment_type_orders as pto ON ord.order_id = pto.order_id
    -- Add macro here
    {{- limit_dataset_if_not_deploy_env('order_date', 3) }}
    ```
    * **Lógica do Código:** A macro `limit_dataset_if_not_deploy_env` verifica se o ambiente de execução (`target.name`) não é 'deploy'. Se não for, ela insere uma cláusula `WHERE` na consulta SQL para filtrar os dados pela `column_name` (ex: `order_date`) para os últimos `nbr_months_of_data` (ex: 3 meses). Essa macro é chamada no modelo `fct_orders.sql`.
    * **O que se Propõe a Fazer:** Reduzir automaticamente o volume de dados processados em ambientes de desenvolvimento ou teste, apenas quando o ambiente não for de implantação.
    * **Importância num Pipeline de Dados:** Essa macro é uma **melhor prática crucial para otimização de recursos e custo** em ambientes de não-produção. Ao limitar o volume de dados, acelera o desenvolvimento e teste, evita sobrecarga desnecessária na plataforma de dados e economiza custos de computação.

11. **Configuração de Pacotes em `packages.yml` (Exemplo 5-18):**
    ```yaml
    packages:
      - package: dbt-labs/dbt_utils
        version: 1.1.1
    ```
    * **Lógica do Código:** Este arquivo YAML declara a dependência do pacote `dbt_utils` e sua versão específica (`1.1.1`). Ao executar `dbt deps`, o dbt baixa e instala este pacote.
    * **O que se Propõe a Fazer:** Declarar pacotes dbt de terceiros para serem instalados no projeto.
    * **Importância num Pipeline de Dados:** Pacotes dbt promovem a **reutilização de código, a padronização** de tarefas comuns e a extensibilidade. O `packages.yml` é fundamental para gerenciar essas dependências e estender as capacidades do dbt, reduzindo a necessidade de "reinventar a roda".

12. **Uso da Macro `safe_divide` do Pacote `dbt_utils` (Exemplo 5-21):**
    ```sql
    select
        order_id,
        customer_id,
        cash_amount,
        total_amount,
        {{ dbt_utils.safe_divide('cash_amount', 'total_amount') }}
    from {{ ref('fct_orders') }}
    ```
    * **Lógica do Código:** A macro `dbt_utils.safe_divide` é usada para dividir `cash_amount` por `total_amount`. Se o denominador (`total_amount`) for zero ou nulo, a macro retorna `null` em vez de gerar um erro de divisão por zero.
    * **O que se Propõe a Fazer:** Realizar uma operação de divisão segura que lida graciosamente com denominadores inválidos.
    * **Importância num Pipeline de Dados:** Macros de utilidade como `safe_divide` são **essenciais para a robustez do pipeline**, evitando falhas devido a dados problemáticos (ex: zero ou valores nulos em denominadores). Elas melhoram a confiabilidade das transformações e reduzem a necessidade de lógica de tratamento de erros manual.

13. **Configuração de _Semantic Model_ em YAML (Exemplo 5-22):**
    ```yaml
    semantic_models:
      - name: orders
        description: |
          Order fact table. This table is at the order grain with one row per order.
        model: ref('fct_orders')
        entities:
          - name: order_id
            type: primary
          - name: customer
            type: foreign
            expr: customer_id
        dimensions:
          - name: order_date
            type: time
            type_params:
              time_granularity: day
          - name: is_order_completed
            type: categorical
        measures:
          - name: total_amount
            description: Total amount paid by the customer with successful payment
              status.
            agg: sum
          - name: order_count
            expr: 1
            agg: sum
          # ... outras medidas ...
    ```
    * **Lógica do Código:** Este arquivo YAML define um `semantic_model` chamado `orders` que referencia o modelo dbt `fct_orders`. Ele descreve as **entidades** (chaves primárias e estrangeiras), **dimensões** (atributos para fatiar/detalhar dados) e **medidas** (métricas a serem agregadas) que podem ser usadas para definir métricas de negócio.
    * **O que se Propõe a Fazer:** Criar uma camada de abstração para os dados, fornecendo definições de negócio padronizadas e um modelo para a criação de métricas.
    * **Importância num Pipeline de Dados:** O _dbt Semantic Layer_ é uma **melhor prática para governança e democratização de dados**. Ele garante a **consistência de métricas** em toda a organização, simplifica o acesso aos dados para usuários de negócio e centraliza a lógica de definição de métricas, minimizando erros e discrepâncias em relatórios.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é **crucial para o engenheiro de dados** que busca otimizar, escalar e profissionalizar seus _pipelines_ dbt. Ele capacita o engenheiro a:
* **Selecionar a materialização mais apropriada** para cada modelo, balanceando custo e performance.
* **Escrever código SQL dinâmico e reutilizável** usando Jinja e macros para reduzir a duplicação e aumentar a adaptabilidade.
* **Alavancar pacotes dbt** para estender a funcionalidade e usar as melhores práticas da comunidade.
* **Implementar uma camada semântica** para garantir a consistência das métricas e facilitar o consumo de dados pelos usuários de negócio.
Essas habilidades são essenciais para construir e manter **_pipelines_ de dados de alta qualidade, eficientes e escaláveis** em ambientes de produção.

---

## Capítulo 6: Building an End-to-End Analytics Engineering Use Case

**Foco da Discussão do Capítulo:**
Este capítulo final **consolida todo o aprendizado** do livro, guiando o leitor através da construção de um **caso de uso ponta a ponta de engenharia de analytics**. Ele aborda desde a modelagem de dados operacional e analítica, passando pela arquitetura de dados e a criação de um _data warehouse_ com dbt (incluindo camadas de _staging_, dimensões e fatos), até a implementação de testes, documentação e implantação em produção. O objetivo é ilustrar um _workflow_ analítico holístico para um cenário omnichannel.

**Principais Conceitos do Capítulo:**
* **Definição do Problema:** Um caso de uso de analytics omnichannel para aprimorar a experiência do cliente, exigindo um _dataset_ abrangente de clientes, produtos e interações em vários canais.
* **Modelagem de Dados Operacionais:** Foco em criar uma base sólida para sistemas OLTP.
    * **Modelo Conceitual:** Representação de alto nível das entidades e seus relacionamentos.
    * **Modelo Lógico:** Normaliza os dados para eliminar redundâncias e melhorar a integridade.
    * **Modelo Físico:** Traduz o modelo lógico para configurações específicas de armazenamento de banco de dados (ex: MySQL), incluindo tipos de dados e restrições. Inclui colunas de auditoria (`CREATED_AT`, `UPDATED_AT`) como **melhor prática** para extrações incrementais e CDC.
* **Arquitetura de Dados de Alto Nível:** Uma arquitetura enxuta para o caso de uso omnichannel, envolvendo MySQL (fonte), BigQuery (destino para dados brutos e _data warehouse_), e dbt (transformação).
    * **Simulação de ETL:** Um _script_ Python para extrair dados do MySQL, limpar alguns tipos de dados e carregá-los no BigQuery. Utiliza `mysql.connector`, `pandas` e `pandas_gbq`.
    * **Granularidade dos Fatos de Negócio:** Define o nível de detalhe em que os fatos são capturados e analisados, essencial para equilibrar detalhes e complexidade. A escolha depende dos objetivos analíticos.
* **Criação do _Data Warehouse_ com dbt:**
    * **Setup Inicial:** Conexão do dbt com BigQuery e GitHub (recapitulação do Capítulo 4).
    * **Camada de _Staging_:** Criação de arquivos YAML para _sources_ (`_omnichannel_raw_sources.yml`) e modelos (`_omnichannel_raw_models.yml`). Modelos de _staging_ (`stg_channels`, `stg_customers`, `stg_products`, `stg_purchase_history`, `stg_visit_history`) extraem dados das fontes e os preparam.
    * **Modelos de Dimensão:** `dim_channels`, `dim_customers`, `dim_products`, `dim_date`. Capturam informações descritivas de entidades de negócio e usam chaves substitutas geradas pela macro `dbt_utils.generate_surrogate_key()` para estabilidade. Utilizam pacotes `dbt_utils` e `dbt_date`.
    * **Modelos de Fato:** `fct_purchase_history`, `fct_visit_history`. Representam dados numéricos mensuráveis de eventos de negócio. Integram-se com as dimensões, calculam métricas (ex: `mtr_total_amount_gross`, `mtr_total_amount_net`) e usam `COALESCE()` para tratar chaves estrangeiras não correspondentes.
* **Testes, Documentação e Implantação com dbt:**
    * **Testes Genéricos:** Focam em garantir a unicidade e não-nulidade das chaves substitutas em dimensões, e a integridade referencial (`relationships` test) entre tabelas de fatos e dimensões.
    * **Testes Singulares:** Focam em métricas específicas de tabelas de fatos (ex: `mtr_total_amount_gross` positivo, `mtr_unit_price <= mtr_total_amount_gross`, `mtr_length_of_stay_minutes` positivo), garantindo que os dados sigam regras de negócio.
    * **Documentação:** Atualizar arquivos YAML dos modelos com descrições abrangentes para tabelas e colunas, gerando documentação HTML com `dbt docs generate`.
    * **Implantação:** Criação de um ambiente de produção no dbt Cloud, configuração de _jobs_ para `dbt build` e `dbt test` automatizados e agendamento de execuções para o ambiente de produção.
* **Análise de Dados com SQL:** Utilizar o _data warehouse_ (modelo estrela) no BigQuery para responder a perguntas de negócio complexas usando SQL avançado (ex: total vendido por trimestre, tempo médio por canal, top produtos por canal, top clientes). Usa CTEs e funções de janela (`RANK() OVER`) para análises.

**Exemplos de Código:**

1.  **Criação de Tabelas Operacionais MySQL (`CREATE TABLE` - Exemplo 6-2):**
    ```sql
    CREATE DATABASE IF NOT EXISTS OMNI_MANAGEMENT;
    USE OMNI_MANAGEMENT;

    CREATE TABLE IF NOT EXISTS customers (
        customer_id INT PRIMARY KEY AUTO_INCREMENT,
        name VARCHAR(150),
        date_birth DATE,
        email_address VARCHAR(150),
        phone_number VARCHAR(30),
        country VARCHAR(100),
        CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        UPDATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
    );
    -- Tabelas similares para 'products' e 'channels'
    ```
    * **Lógica do Código:** Este _snippet_ DDL cria um banco de dados (`OMNI_MANAGEMENT`) e a tabela `customers` com colunas, tipos de dados (`INT`, `VARCHAR`, `DATE`, `TIMESTAMP`), chave primária auto-incrementável (`PRIMARY KEY AUTO_INCREMENT`) e colunas de auditoria (`CREATED_AT`, `UPDATED_AT`).
    * **O que se Propõe a Fazer:** Construir a camada de dados operacionais para um sistema transacional que servirá como fonte de dados para o _data warehouse_.
    * **Importância num Pipeline de Dados:** A modelagem operacional é a base para a **captura de dados transacionais**. Colunas de auditoria são cruciais para **CDC (_Change Data Capture_)** e extrações incrementais, essenciais para alimentar o _data warehouse_ de forma eficiente.

2.  **Criação de Tabelas de Relacionamento MySQL (`CREATE TABLE` - Exemplo 6-3):**
    ```sql
    CREATE TABLE IF NOT EXISTS purchaseHistory (
        customer_id INTEGER,
        product_sku INTEGER,
        channel_id INTEGER,
        quantity INT,
        discount DOUBLE DEFAULT 0,
        order_date DATETIME NOT NULL,
        CREATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        UPDATED_AT TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        FOREIGN KEY (channel_id) REFERENCES channels(channel_id),
        FOREIGN KEY (product_sku) REFERENCES products(product_sku),
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
    );
    -- Tabela similar para 'visitHistory'
    ```
    * **Lógica do Código:** Este _snippet_ DDL cria a tabela `purchaseHistory` para registrar eventos de compra. Ela inclui chaves estrangeiras (`FOREIGN KEY`) que referenciam as tabelas `customers`, `products` e `channels`, além de colunas para quantidade, desconto e data do pedido. Também adiciona colunas de auditoria (`CREATED_AT`, `UPDATED_AT`).
    * **O que se Propõe a Fazer:** Capturar as relações e os fatos de negócio em um modelo de dados operacional, que será a fonte para o _data warehouse_.
    * **Importância num Pipeline de Dados:** Tabelas de relacionamento são cruciais para **manter a integridade referencial** e capturar eventos transacionais. Elas são a fonte de dados brutos que serão transformados em fatos e dimensões para o _data warehouse_, representando o registro original das operações de negócio.

3.  **Função de Extração Python para MySQL (Exemplo 6-5):**
    ```python
    '''
        Simulate the extraction step in an ETL job
    '''
    def extract_table_from_mysql(table_name, my_sql_connection):
        # Extract data from mysql table
        extraction_query = 'select * from ' + table_name
        df_table_data = pd.read_sql(extraction_query,my_sql_connection)
        return df_table_data
    ```
    * **Lógica do Código:** A função `extract_table_from_mysql` conecta a um banco de dados MySQL, constrói uma consulta `SELECT * FROM <table_name>` para a tabela especificada e usa `pd.read_sql()` do Pandas para extrair os dados diretamente para um DataFrame Pandas.
    * **O que se Propõe a Fazer:** Simular a etapa de extração de um _job_ ETL do MySQL para obter os dados operacionais.
    * **Importância num Pipeline de Dados:** Esta função demonstra como um engenheiro de dados extrai dados de sistemas de origem operacionais, que é o **primeiro passo em qualquer pipeline ETL/ELT**. É crucial para mover dados do sistema transacional para uma área onde possam ser limpos e transformados.

4.  **Função de Carregamento Python para BigQuery (Exemplo 6-7):**
    ```python
    '''
        Simulate the load step in an ETL job
    '''
    def load_data_into_bigquery(bq_project_id, dataset,table_name,df_table_data):
        import pandas_gbq as pdbq
        full_table_name_bg = "{}.{}".format(dataset,table_name)
        pdbq.to_gbq(df_table_data,full_table_name_bg,project_id=bq_project_id,
          if_exists='replace')
    ```
    * **Lógica do Código:** A função `load_data_into_bigquery` utiliza a biblioteca `pandas_gbq` para carregar um DataFrame Pandas (`df_table_data`) para uma tabela específica no BigQuery. O nome completo da tabela é formatado usando o `dataset` e `table_name`. O argumento `if_exists='replace'` garante que a tabela seja substituída se já existir.
    * **O que se Propõe a Fazer:** Simular a etapa de carregamento de um _job_ ETL, movendo os dados extraídos e minimamente transformados para o BigQuery, que atuará como _data lake_ para dados brutos.
    * **Importância num Pipeline de Dados:** Esta função é um exemplo prático de como um engenheiro de dados carrega dados para um _data lake_ ou _data warehouse_ na nuvem. É crucial para **persistir os dados extraídos e transformados**, tornando-os disponíveis para processamento _downstream_ pelo dbt.

5.  **Configuração de _Source_ dbt (`_omnichannel_raw_sources.yml` - Exemplo 6-10):**
    ```yaml
    version: 2
    sources:
      - name: omnichannel
        database: analytics-engineering-book
        schema: omnichannel_raw
        tables:
          - name: Channels
          - name: Customers
          - name: Products
          - name: VisitHistory
          - name: PurchaseHistory
    ```
    * **Lógica do Código:** Este arquivo YAML define uma fonte de dados chamada `omnichannel` com seu `database` e `schema` específicos no BigQuery. Ele lista as tabelas brutas disponíveis nessa fonte (`Channels`, `Customers`, `Products`, `VisitHistory`, `PurchaseHistory`) para que o dbt possa referenciá-las.
    * **O que se Propõe a Fazer:** Declarar formalmente as fontes de dados brutas para o dbt.
    * **Importância num Pipeline de Dados:** As declarações de _source_ são uma **melhor prática** no dbt, pois formalizam as dependências de dados brutos, permitem testes de _freshness_ e constroem a linhagem completa do _pipeline_. Isso é crucial para a **governança de dados e a compreensão do _data flow_** do início ao fim.

6.  **Modelo de Dimensão dbt com Chave Substituta (`dim_channels` - Exemplo 6-17):**
    ```sql
    with stg_dim_channels AS (
        SELECT
            channel_id AS nk_channel_id,
            channel_name AS dsc_channel_name,
            created_at AS dt_created_at,
            updated_at AS dt_updated_at
        FROM {{ ref("stg_channels")}}
    )
    SELECT
        {{ dbt_utils.generate_surrogate_key( ["nk_channel_id"] )}} AS sk_channel,
        * FROM stg_dim_channels
    ```
    * **Lógica do Código:** Este modelo dbt seleciona dados do modelo de _staging_ `stg_channels`, renomeando as colunas para seguir uma convenção (`nk_`, `dsc_`, `dt_`). Em seguida, utiliza a macro `dbt_utils.generate_surrogate_key()` para criar uma chave substituta (`sk_channel`) a partir da chave natural (`nk_channel_id`), adicionando essa nova coluna ao resultado.
    * **O que se Propõe a Fazer:** Construir uma tabela de dimensão para o _data warehouse_, incluindo a geração de chaves substitutas para garantir a estabilidade e a integração.
    * **Importância num Pipeline de Dados:** Chaves substitutas são uma **melhor prática em modelagem dimensional**, pois isolam o _data warehouse_ das mudanças nas chaves naturais dos sistemas de origem, garantindo **consistência e estabilidade** nos dados analíticos. A macro `dbt_utils` automatiza essa geração.

7.  **Modelo de Fato dbt com Junções e Métricas Calculadas (`fct_purchase_history` - Exemplo 6-22):**
    ```sql
    with stg_fct_purchase_history AS (
        SELECT
            customer_id AS nk_customer_id,
            product_sku AS nk_product_sku,
            channel_id AS nk_channel_id,
            quantity AS mtr_quantity,
            discount AS mtr_discount,
            CAST(order_date AS DATE) AS dt_order_date
        FROM {{ ref("stg_purchase_history")}}
    )
    SELECT
        COALESCE(dcust.sk_customer, '-1') AS sk_customer,
        COALESCE(dchan.sk_channel, '-1') AS sk_channel,
        COALESCE(dprod.sk_product, '-1') AS sk_product,
        fct.dt_order_date AS sk_order_date,
        fct.mtr_quantity,
        fct.mtr_discount,
        dprod.mtr_unit_price,
        ROUND(fct.mtr_quantity * dprod.mtr_unit_price,2) AS mtr_total_amount_gross,
        ROUND(fct.mtr_quantity *
                  dprod.mtr_unit_price *
                  (1 - fct.mtr_discount),2) AS mtr_total_amount_net
    FROM stg_fct_purchase_history AS fct
    LEFT JOIN {{ ref("dim_customers")}} AS dcust ON fct.nk_customer_id = dcust.nk_customer_id
    LEFT JOIN {{ ref("dim_channels")}} AS dchan ON fct.nk_channel_id = dchan.nk_channel_id
    LEFT JOIN {{ ref("dim_products")}} AS dprod ON fct.nk_product_sku = dprod.nk_product_sku
    ```
    * **Lógica do Código:** Este modelo dbt seleciona dados do modelo de _staging_ `stg_fct_purchase_history`, unindo-os com as dimensões correspondentes (`dim_customers`, `dim_channels`, `dim_products`) através de `LEFT JOIN` para obter as chaves substitutas (`sk_`). Ele calcula métricas como `mtr_total_amount_gross` e `mtr_total_amount_net` com base em quantidade, preço unitário e desconto, e usa `COALESCE` para lidar com chaves ausentes.
    * **O que se Propõe a Fazer:** Construir uma tabela de fatos para o _data warehouse_, integrando dimensões e calculando métricas de negócio para análises de vendas.
    * **Importância num Pipeline de Dados:** Tabelas de fatos são o **núcleo das análises de negócio**, armazenando métricas e seus contextos dimensionais. Esse modelo demonstra a criação de métricas complexas e a integração de dados para fornecer _insights_ valiosos sobre o desempenho de vendas, como receita bruta e líquida.

8.  **Configuração de Testes Genéricos dbt para o _Data Warehouse_ (Exemplo 6-24):**
    ```yaml
    version: 2
    models:
      - name: dim_customers
        columns:
          - name: sk_customer
            tests:
              - unique
              - not_null
      # ... outras dimensões com testes unique/not_null ...
      - name: fct_purchase_history
        columns:
          - name: sk_customer
            tests:
              - relationships:
                  to: ref('dim_customers')
                  field: sk_customer
          # ... outros fatos com testes relationships ...
    ```
    * **Lógica do Código:** Este arquivo YAML define testes genéricos para as dimensões (verificando que as chaves substitutas `sk_customer`, `sk_channel`, `sk_product` e `date_day` são `unique` e `not_null`) e para as tabelas de fatos (usando o teste `relationships` para validar a integridade referencial das chaves estrangeiras com as dimensões correspondentes).
    * **O que se Propõe a Fazer:** Implementar uma bateria de testes genéricos abrangente para o recém-construído _data warehouse_.
    * **Importância num Pipeline de Dados:** Testes genéricos são **críticos para a qualidade e confiabilidade do _data warehouse_**. Eles garantem que as chaves primárias e estrangeiras funcionem corretamente, que não haja valores nulos inesperados e que a integridade referencial seja mantida, fundamental para a **confiança nos dados analíticos**.

9.  **Teste Singular dbt para Métrica Positiva (`assert_mtr_total_amount_gross_is_positive.sql` - Exemplo 6-25):**
    ```sql
    select
        sk_customer,
        sk_channel,
        sk_product,
        sum(mtr_total_amount_gross) as mtr_total_amount_gross
    from {{ ref('fct_purchase_history') }}
    group by 1, 2, 3
    having mtr_total_amount_gross < 0
    ```
    * **Lógica do Código:** Este teste singular consulta o modelo `fct_purchase_history`, agrupa por chaves substitutas de cliente, canal e produto, e soma a métrica `mtr_total_amount_gross`. O teste falha se a soma de `mtr_total_amount_gross` for menor que zero para qualquer grupo, indicando um erro nos dados (ex: valores de vendas negativos).
    * **O que se Propõe a Fazer:** Validar que uma métrica calculada na tabela de fatos seja sempre positiva, aplicando uma regra de negócio específica.
    * **Importância num Pipeline de Dados:** Testes singulares garantem a **acurácia das métricas de negócio** e capturam **anomalias nos dados** que podem não ser detectadas por testes genéricos. Eles são vitais para a qualidade final dos dados servidos para análise e para a tomada de decisões.

10. **Documentação de Modelos em YAML (`_omnichannel_marts.yml` - Exemplo 6-28):**
    ```yaml
    version: 2
    models:
      - name: dim_customers
        description: All customers' details. Includes anonymous users who used guest
          checkout.
        columns:
          - name: sk_customer
            description: Surrogate key of the customer dimension.
            tests:
              - unique
              - not_null
      # ... outras dimensões e fatos com descrições ...
    ```
    * **Lógica do Código:** Este arquivo YAML adiciona propriedades `description` aos modelos e colunas na camada de _marts_ (ex: `dim_customers`). Ele fornece explicações sobre o propósito de cada ativo de dados e suas colunas, complementando os testes já definidos [440, 441, 442, 44álise SQL: Top 3 Produtos por Canal (Exemplo 6-31):**
    ```sql
    WITH base_cte AS (
        SELECT dp.dsc_product_name,
               dc.dsc_channel_name,
               ROUND(SUM(fct.mtr_total_amount_net),2) as sum_total_amount
        FROM `omnichannel_analytics`.`fct_purchase_history` fct
        LEFT JOIN `omnichannel_analytics`.`dim_products` dp ON dp.sk_product = fct.sk_product
        LEFT JOIN `omnichannel_analytics`.`dim_channels` dc ON dc.sk_channel = fct.sk_channel
        GROUP BY dc.dsc_channel_name, dp.dsc_product_name
    ), ranked_cte AS(
        SELECT base_cte.dsc_product_name,
               base_cte.dsc_channel_name,
               base_cte.sum_total_amount,
               RANK() OVER(PARTITION BY dsc_channel_name ORDER BY sum_total_amount DESC) AS rank_total_amount
        FROM base_cte
    )
    SELECT * FROM ranked_cte WHERE rank_total_amount <= 3
    ```
    * **Lógica do Código:** Esta consulta utiliza Common Table Expressions (CTEs) e uma função de janela `RANK() OVER (PARTITION BY ... ORDER BY ...)` para calcular o ranque dos produtos dentro de cada canal, com base nas vendas líquidas (`sum_total_amount`). A `base_cte` agrega as vendas por produto e canal, e a `ranked_cte` atribui o ranque. Finalmente, a consulta principal seleciona os top 3 produtos para cada canal.
    * **O que se Propõe a Fazer:** Responder a uma pergunta de negócio complexa ("Quais são os 3 produtos mais vendidos por canal?") usando o _data warehouse_ e SQL avançado.
    * **Importância num Pipeline de Dados:** Demonstra o poder de um _data warehouse_ bem modelado (esquema estrela) e SQL avançado (CTEs, funções de janela) para **derivar _insights_ de negócio complexos e acionáveis**, permitindo análises multifacetadas dos dados e suportando a tomada de decisões estratégicas.

**Onde se Encaixa o Tema Abordado no Capítulo no Fluxo de Trabalho de um Engenheiro de Dados:**
Este capítulo é a **culminação do aprendizado prático** para o engenheiro de dados. Ele oferece a experiência de:
* **Construir um _workflow_ de engenharia de analytics ponta a ponta**, desde a modelagem inicial até a implantação em produção.
* **Aplicar todas as melhores práticas** de modelagem (operacional e dimensional), desenvolvimento de dbt (staging, dimensões, fatos, testes, documentação) e automação.
* **Resolver problemas de negócio complexos** usando SQL sobre um _data warehouse_ bem estruturado.
Isso é essencial para **projetar, implementar e manter soluções de dados robustas, escaláveis e valiosas** em cenários do mundo real.
