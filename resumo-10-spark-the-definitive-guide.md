# Resumo do livro Spark the definitive guide
## **Parte I: Visão Geral e Fundamentos do Spark para ETL**

* **Capítulo 1: O que é Apache Spark?** (Spark The Definitive Guide)
    * **Spark como Motor Unificado:** O Apache Spark é um motor poderoso para processamento de dados em larga escala em clusters, oferecendo APIs em Python (PySpark) [Spark The Definitive Guide]. Ele gerencia o carregamento e a computação sobre os dados, mas não é um sistema de armazenamento persistente. Sua versatilidade permite realizar extração, transformação, consultas SQL e streaming com um único motor [Spark The Definitive Guide].
    * **PySpark para Engenheiros de Dados:** A API PySpark permite usar a familiaridade do Python com a capacidade distribuída do Spark, tornando-o rápido e expressivo para manipular grandes volumes de dados [Spark The Definitive Guide].
    * **Quando usar PySpark:** É ideal para grandes conjuntos de dados que não cabem na memória de uma única máquina. Para dados muito pequenos, a sobrecarga da coordenação distribuída pode torná-lo menos eficiente [Spark The Definitive Guide].

* **Capítulo 2: Uma Introdução Suave ao Spark** (Spark The Definitive Guide)
    * **Arquitetura Essencial:** Uma aplicação Spark envolve um **driver** (que coordena as tarefas) e **executors** (que realizam as computações nos nós de trabalho) [Spark The Definitive Guide, 10].
    * **SparkSession: O Ponto de Entrada:** É o objeto principal para interagir com o Spark e sua funcionalidade [Spark The Definitive Guide].
        ```python
        from pyspark.sql import SparkSession
        # Cria ou obtém uma SparkSession
        spark = SparkSession.builder \
            .appName("MeuPrimeiroAppETL") \
            .getOrCreate()
        print("SparkSession inicializada!")
        ```
    * **DataFrames: A Estrutura de Dados Fundamental:** No PySpark, os DataFrames são a principal abstração para dados tabulares (linhas e colunas), otimizados para computação distribuída [Spark The Definitive Guide].
    * **Transformações (Transformations) e Ações (Actions): Avaliação Preguiçosa (Lazy Evaluation):**
        * **Transformações** (ex: `select()`, `filter()`, `withColumn()`) são instruções de como modificar um DataFrame. Elas são **lazy** (preguiçosas), ou seja, não executam imediatamente, apenas constroem um plano lógico [Spark The Definitive Guide, 526, 528]. Isso economiza memória e permite otimizações [Spark The Definitive Guide, 12, 14, 41].
        * **Ações** (ex: `count()`, `show()`, `write()`) são o que realmente aciona a execução das transformações e a computação dos resultados [Spark The Definitive Guide, 11, 15, 526].
    * **Fluxo de Trabalho ETL Básico (Exemplo de Contagem de Palavras):** Este é um padrão comum:
        1.  **Extrair (Extract):** Ler dados de uma fonte [Spark The Definitive Guide, 16].
        2.  **Transformar (Transform):** Limpar, filtrar, reestruturar [Spark The Definitive Guide, 16].
        3.  **Carregar (Load):** Salvar os resultados processados [Spark The Definitive Guide, 16].

        *Exemplo de Código (Contagem de Palavras):*
        ```python
        from pyspark.sql.functions import col, split, explode, lower, regexp_extract

        # 1. Extrair (Extract): Ler um arquivo de texto
        # df = spark.read.text("./data/gutenberg_books/1342-0.txt") # [Spark The Definitive Guide, 23]
        # Para demonstração, criamos um DataFrame pequeno
        data = [("This is a sample sentence.",), ("Another sample, with more words.",)]
        df_livro = spark.createDataFrame(data, ["value"])

        # 2. Transformar (Transform): Processar o texto
        df_palavras = df_livro \
            .select(split(col("value"), " ").alias("line")) \
            .select(explode(col("line")).alias("word")) \
            .select(lower(col("word")).alias("word")) \
            .select(regexp_extract(col("word"), "[a-z']+", 0).alias("word")) \
            .where(col("word") != "")

        # 3. Transformar (Transform) - Agregação: Contar palavras
        df_frequencia = df_palavras.groupby("word").count()

        # Ação para visualizar os resultados
        df_frequencia.show(5)
        # +-----+-----+
        # | word|count|
        # +-----+-----+
        # |sample|    2|
        # |  this|    1|
        # |  more|    1|
        # |  with|    1|
        # |another|    1|
        # +-----+-----+
        # (Saída pode variar)
        ```

* **Capítulo 3: Um Tour do Conjunto de Ferramentas do Spark** (Spark The Definitive Guide)
    * **`spark-submit` para Produção:** Para executar programas PySpark como scripts independentes, você usa `spark-submit` [Spark The Definitive Guide, 24].
        ```bash
        # Exemplo de como submeter um script PySpark
        $ spark-submit meu_script_etl.py
        ```
    * **Encadeamento de Métodos (Method Chaining):** Torna o código mais conciso e legível ao encadear transformações, eliminando variáveis intermediárias [Spark The Definitive Guide, 22, 23, 122]. O Spark otimiza essas cadeias de operações [Spark The Definitive Guide, 6].
    * **Importações Convencionais:** Use `import pyspark.sql.functions as F` para funções do Spark SQL e `import pyspark.sql.types as T` para tipos de dados [Spark The Definitive Guide].
    * **Escalando a Ingestão de Dados:** Para ler múltiplos arquivos em um único DataFrame, use padrões glob (ex: `*.csv`) [Spark The Definitive Guide, 27].
        ```python
        # Lendo todos os arquivos CSV em um diretório
        df_todos_csv = spark.read.csv("caminho/para/meus_arquivos/*.csv", header=True, inferSchema=True)
        ```

## **Parte II: Structured APIs – DataFrames e SQL para ETL**

* **Capítulo 4: Visão Geral da API Estruturada** (Spark The Definitive Guide)
    * **Schemas e Otimização:** O esquema de um DataFrame (nomes e tipos de colunas) é crucial. O Spark pode inferir o esquema ou você pode defini-lo manualmente para maior robustez do ETL [Spark The Definitive Guide]. O Catalyst Optimizer do Spark usa o esquema e as operações para criar um plano lógico, otimizá-lo e depois gerar um plano físico eficiente para execução [Spark The Definitive Guide, 56, 535].

* **Capítulo 5: Operações Básicas com DataFrames** (Spark The Definitive Guide)
    * **Seleção e Projeção de Colunas:** Use `select()` para escolher, reordenar ou renomear colunas [Spark The Definitive Guide].
        ```python
        # Renomeando colunas e selecionando apenas algumas
        df_selecionado = df.select(col("coluna_original_1").alias("nova_coluna_a"), "coluna_original_2")
        df_renomeado = df.withColumnRenamed("coluna_antiga", "coluna_nova") # [Spark The Definitive Guide, 29]
        ```
    * **Criação de Novas Colunas:** Use `withColumn()` para adicionar colunas baseadas em expressões ou outras colunas [Spark The Definitive Guide].
        ```python
        from pyspark.sql.functions import lit, when
        # Adiciona uma coluna com valor literal e outra com lógica condicional
        df_com_novas_colunas = df.withColumn("constante", lit(10)) \
                                 .withColumn("status_flag", when(col("valor") > 100, "ALTO").otherwise("BAIXO"))
        ```
    * **Remoção de Colunas:** Use `drop()` para remover colunas indesejadas [Spark The Definitive Guide, 76, 124].
        ```python
        df_sem_coluna = df.drop("coluna_sensivel", "coluna_temporaria")
        ```
    * **Filtragem de Linhas:** Use `filter()` ou `where()` para selecionar linhas que atendem a uma condição [Spark The Definitive Guide, 6].
        ```python
        df_registros_validos = df.where((col("idade") >= 18) & (col("pais") == "Brasil"))
        ```
    * **Tratamento de Valores Nulos:**
        * `fillna()`: Preenche valores nulos em colunas específicas com um valor [Spark The Definitive Guide, 29, 107-108].
            ```python
            # Preenche nulos em 'idade' com 0 e em 'cidade' com 'Desconhecida'
            df_nulos_preenchidos = df.fillna({"idade": 0, "cidade": "Desconhecida"})
            ```
        * `dropna()`: Remove linhas que contêm valores nulos [Spark The Definitive Guide, 107-108, 286].
            ```python
            # Remove linhas onde 'id_usuario' é nulo
            df_sem_nulos_criticos = df.dropna(subset=["id_usuario"])
            ```

* **Capítulo 6: Trabalhando com Diferentes Tipos de Dados** (Spark The Definitive Guide)
    * **Funções Built-in para Transformação de Dados:** O módulo `pyspark.sql.functions` oferece centenas de funções otimizadas para manipular strings, números, datas, e tipos complexos (arrays, structs, maps) [Spark The Definitive Guide].
        ```python
        from pyspark.sql.functions import lower, upper, trim, concat_ws, to_date, year, month, dayofmonth

        # Manipulação de strings: converter para minúsculas, remover espaços, concatenar
        df_strings = df.withColumn("nome_limpo", lower(trim(col("nome")))) \
                       .withColumn("nome_completo", concat_ws(" ", col("primeiro_nome"), col("sobrenome")))

        # Manipulação de datas: converter string para data, extrair partes da data
        df_datas = df.withColumn("data_compra", to_date(col("data_string"), "yyyy-MM-dd")) \
                     .withColumn("ano_compra", year(col("data_compra"))) \
                     .withColumn("mes_compra", month(col("data_compra")))
        ```
    * **UDFs (User-Defined Functions):** Quando as funções built-in não são suficientes, você pode escrever sua própria lógica Python e executá-la no Spark [Spark The Definitive Guide, 53, 185-191]. Use com cautela, pois são geralmente mais lentas que as funções built-in.
        ```python
        from pyspark.sql.types import DoubleType
        from pyspark.sql.functions import udf

        # UDF para calcular imposto
        @udf(DoubleType())
        def calcula_imposto(valor_bruto):
            if valor_bruto is None:
                return None
            return float(valor_bruto) * 0.15

        df_imposto = df.withColumn("valor_imposto", calcula_imposto(col("valor_total")))
        ```
        As **Pandas UDFs (Vectorized UDFs)** são uma alternativa mais performática para processar dados com funções Pandas, operando em `pd.Series` ou `pd.DataFrame` por partição [Data Analysis with Python and PySpark, 34, 35].

* **Capítulo 7: Agregações** (Spark The Definitive Guide)
    * **Agrupamento (`groupby()`):** Agrupa linhas com base em uma ou mais colunas para realizar cálculos resumidos [Spark The Definitive Guide].
    * **Funções de Agregação (`agg()`):** Aplica funções como `count()`, `sum()`, `avg()`, `min()`, `max()` a grupos de dados [Spark The Definitive Guide].
        ```python
        from pyspark.sql.functions import count, sum, avg
        # Total de vendas e média de preço por produto
        df_sumario_vendas = df.groupby("id_produto") \
                              .agg(count("id_venda").alias("total_vendas"), \
                                   sum("quantidade").alias("total_itens_vendidos"), \
                                   avg("preco_unitario").alias("preco_medio"))
        ```
    * **Funções de Janela (Window Functions):** Permitem realizar cálculos sobre um conjunto de linhas "relacionadas" (uma janela), como médias móveis, rankings ou totais acumulados [Spark The Definitive Guide].
        ```python
        from pyspark.sql.window import Window
        from pyspark.sql.functions import row_number, lag, last_value

        # Define uma janela: particiona por cliente, ordena por data
        window_cliente = Window.partitionBy("id_cliente").orderBy("data_transacao")

        # Calcula o número da transação para cada cliente
        df_com_ranking = df.withColumn("ranking_transacao", row_number().over(window_cliente))

        # Pega o valor da transação anterior para cada cliente
        df_com_lag = df.withColumn("transacao_anterior", lag("valor_transacao", 1).over(window_cliente))

        # Pega o último valor conhecido (útil para preencher gaps)
        window_fill = Window.partitionBy("id_cliente").orderBy("data").rowsBetween(Window.unboundedPreceding, Window.currentRow)
        df_fill_forward = df.withColumn("valor_preenchido", last_value("valor_observado", ignorenulls=True).over(window_fill))
        ```

* **Capítulo 8: Joins** (Spark The Definitive Guide)
    * **Combinando DataFrames:** Joins são essenciais para integrar dados de diferentes fontes ou estágios de processamento [Spark The Definitive Guide].
    * **Tipos de Joins Comuns:** `inner`, `outer`, `left_outer`, `right_outer`, `left_semi`, `left_anti` [Spark The Definitive Guide].
        ```python
        # Suponha df_pedidos e df_clientes
        df_pedidos_com_clientes = df_pedidos.join(df_clientes, on="id_cliente", how="inner")

        # Left Anti Join: Encontrar clientes sem pedidos
        df_clientes_sem_pedidos = df_clientes.join(df_pedidos, on="id_cliente", how="left_anti")
        ```

* **Capítulo 9: Fontes de Dados** (Spark The Definitive Guide)
    * **Leitura de Dados (Extract):** O `spark.read` é o objeto central para ler dados de vários formatos e sistemas, como CSV, JSON, Parquet, ORC, JDBC, Texto [Spark The Definitive Guide].
        ```python
        # Leitura de CSV com cabeçalho e inferência de esquema
        df_csv = spark.read.csv("caminho/para/dados.csv", header=True, inferSchema=True)

        # Leitura de JSON
        df_json = spark.read.json("caminho/para/dados.json")

        # Leitura de Parquet (formato otimizado para Spark)
        df_parquet = spark.read.parquet("caminho/para/dados.parquet")

        # Leitura de um banco de dados via JDBC
        df_jdbc = spark.read.format("jdbc") \
            .option("url", "jdbc:postgresql://localhost:5432/meudb") \
            .option("dbtable", "minha_tabela") \
            .option("user", "usuario") \
            .option("password", "senha") \
            .load()
        ```
    * **Escrita de Dados (Load):** O `df.write` é usado para persistir DataFrames processados. Você pode controlar o formato e o modo de escrita [Spark The Definitive Guide].
        ```python
        # Escreve como Parquet (recomendado para performance)
        df_processado.write.mode("overwrite").parquet("caminho/de/saida/dados_processados.parquet")

        # Escreve como CSV com cabeçalho
        df_processado.write.mode("append").option("header", "true").csv("caminho/de/saida/dados_adicionados.csv")

        # Escreve para um banco de dados via JDBC
        df_processado.write.format("jdbc") \
            .option("url", "jdbc:postgresql://localhost:5432/meudb") \
            .option("dbtable", "tabela_final") \
            .option("user", "usuario") \
            .option("password", "senha") \
            .mode("overwrite") \
            .save()
        ```

* **Capítulo 10: Spark SQL** (Spark The Definitive Guide)
    * **Consultas SQL Diretas:** O Spark SQL permite executar consultas SQL sobre DataFrames, aproveitando o otimizador do Spark. Você pode "promover" um DataFrame a uma tabela/view temporária [Spark The Definitive Guide].
        ```python
        # Criar uma view temporária a partir de um DataFrame
        df_csv.createOrReplaceTempView("vendas_temporarias")

        # Executar uma consulta SQL
        df_resultado_sql = spark.sql("SELECT produto, SUM(valor) AS total_vendas FROM vendas_temporarias GROUP BY produto ORDER BY total_vendas DESC")
        df_resultado_sql.show()
        ```
    * A combinação de PySpark e SQL é poderosa para engenheiros de dados, permitindo a flexibilidade de ambos [Spark The Definitive Guide].

## **Parte III: APIs de Baixo Nível (RDDs) (Breve Menção)**

* **Capítulo 12: Resilient Distributed Datasets (RDDs)** (Spark The Definitive Guide)
    * **RDDs:** Embora DataFrames sejam preferidos para a maioria das tarefas de ETL devido às otimizações, RDDs oferecem controle de baixo nível sobre os dados distribuídos [Spark The Definitive Guide, 43, 209, 210, 212, 317].
    * **Quando usar:** Para cenários muito específicos que exigem controle granular ou quando as APIs estruturadas não são adequadas [Spark The Definitive Guide, 212, 317].

## **Parte IV: Aplicações de Produção e Otimização ETL**

* **Capítulo 11: Pensando em Performance: Operações e Memória** (Data Analysis with Python and PySpark)
    * **Operações Narrow vs. Wide:** Entender a diferença é crucial para otimizar [Data Analysis with Python and PySpark, 1, 40, 41, 42].
        * **Narrow Transformations:** Operam em dados que já estão na mesma partição (ex: `filter()`, `withColumn()`). Não exigem troca de dados (shuffle) entre os nós [Data Analysis with Python and PySpark, 41; Spark The Definitive Guide, 19].
        * **Wide Transformations:** Exigem que o Spark reorganize (shuffle) os dados entre as partições (ex: `groupby()`, `join()`, `orderBy()`). O shuffle é uma operação custosa, pois envolve I/O de rede e disco [Data Analysis with Python and PySpark, 42; Spark The Definitive Guide, 527].
    * **Caching de DataFrames:** Salvar DataFrames intermediários na memória (ou disco) para reutilização, evitando recomputações custosas, especialmente em pipelines complexos com múltiplas ações [Data Analysis with Python and PySpark, 1, 45, 270; Spark The Definitive Guide, 531, 546].
        ```python
        # Cachear um DataFrame intermediário para reutilização
        df_interm_limpo = df_raw.filter(col("status") == "OK") \
                                .withColumn("data_parsed", to_date(col("data_str")))
        df_interm_limpo.cache() # [Spark The Definitive Guide, 546]
        # Uma ação para forçar o cache
        df_interm_limpo.count()

        # Agora, operações subsequentes neste DataFrame serão mais rápidas
        df_analise_a = df_interm_limpo.groupby("produto").agg(sum("vendas"))
        df_analise_b = df_interm_limpo.groupby("regiao").agg(avg("preco"))
        ```
    * **Spark UI:** A interface web do Spark (http://localhost:4040 por padrão) é uma ferramenta essencial para monitorar o desempenho, identificar shuffles caros e depurar gargalos de desempenho em suas tarefas de ETL [Spark The Definitive Guide, 296-303].

* **Capítulo 15: Como o Spark Roda em um Cluster** (Spark The Definitive Guide)
    * **Arquitetura e Ciclo de Vida:** Reforça a compreensão de como o Driver, Executors e Cluster Manager interagem para executar uma aplicação Spark, traduzindo o plano lógico em um plano físico de execução [Spark The Definitive Guide, 10, 535].

* **Capítulo 16: Desenvolvendo Aplicações Spark** (Spark The Definitive Guide)
    * **Estrutura de Código para Produção:** Dicas sobre como organizar seu código PySpark para maior manutenibilidade e resiliência, focando em testes da **lógica de negócios**, não da funcionalidade básica do Spark [Spark The Definitive Guide, 269, 541].
    * **Escolha da Linguagem:** Use a linguagem (Python, Scala, R, Java) na qual você se sente mais confortável e que melhor se adapta ao caso de uso, pois as Structured APIs do Spark oferecem desempenho e estabilidade consistentes [Spark The Definitive Guide, 544].

## **Parte V: Streaming para ETL em Tempo Real**

* **Capítulo 20: Fundamentos do Processamento de Streams** (Spark The Definitive Guide)
    * **Processamento de Streams (Real-time ETL):** Lida com fluxos contínuos de dados (ex: logs, transações, dados de sensores) para computar resultados em tempo quase real [Spark The Definitive Guide, 335, 547].
    * **Structured Streaming:** A API de streaming do Spark, construída sobre DataFrames, permite usar as mesmas operações de transformação que você já conhece do processamento batch, de forma contínua [Spark The Definitive Guide, 340, 552, 557].
    * **Batch vs. Streaming Unificado:** A lógica de negócios deve funcionar consistentemente entre batch e streaming. O Structured Streaming foi projetado para isso, permitindo "aplicações contínuas" que combinam ambos [Spark The Definitive Guide, 547].
    * **Event Time vs. Processing Time:** É crucial entender se você deve processar dados com base no timestamp *do evento* (inserido na fonte) ou no timestamp *de processamento* (quando o registro chega ao sistema), especialmente para dados de fontes remotas onde a ordem de chegada pode variar [Spark The Definitive Guide, 346, 553, 559].

* **Capítulo 21: Básico do Structured Streaming** (Spark The Definitive Guide)
    * **Fontes de Streaming (`spark.readStream`):** Para ler dados de fontes de streaming como arquivos em um diretório, Kafka, etc. [Spark The Definitive Guide, 345, 556].
    * **Sinks de Saída (`df.writeStream`):** Para enviar os resultados do seu stream para diferentes destinos (console, memória, Parquet, Kafka) [Spark The Definitive Guide, 345, 556].
    * **Modos de Saída (Output Modes):** `append` (apenas novos registros), `complete` (tabela completa atualizada), `update` (apenas linhas atualizadas) [Spark The Definitive Guide, 346].
    * **Triggers:** Controlam a frequência com que o Spark processa novos dados (ex: a cada 5 segundos, ou `once()` para processar um micro-batch de uma vez) [Spark The Definitive Guide, 346].

    *Exemplo de Código (ETL de Streaming Simples):*
    ```python
    # 1. Extrair (Extract): Ler dados JSON de um diretório em modo de streaming
    # Assumimos que novos arquivos JSON estão sendo adicionados continuamente a 'caminho/para/entrada_stream'
    df_stream_raw = spark.readStream.format("json") \
                                    .option("path", "caminho/para/entrada_stream") \
                                    .schema("id INT, valor DOUBLE, status STRING") \
                                    .load()

    # 2. Transformar (Transform): Filtrar e adicionar uma coluna
    df_stream_processado = df_stream_raw.filter(col("status") == "SUCESSO") \
                                         .withColumn("valor_dobrado", col("valor") * 2)

    # 3. Carregar (Load): Escrever para o console (para demonstração) e para Parquet
    # Escreve para o console a cada 5 segundos no modo 'append'
    query_console = df_stream_processado.writeStream \
                                        .outputMode("append") \
                                        .format("console") \
                                        .trigger(processingTime="5 seconds") \
                                        .start()

    # Escreve para arquivos Parquet no modo 'append'
    query_parquet = df_stream_processado.writeStream \
                                        .outputMode("append") \
                                        .format("parquet") \
                                        .option("path", "caminho/de/saida/stream_parquet") \
                                        .option("checkpointLocation", "caminho/de/saida/checkpoint_stream") \
                                        .trigger(processingTime="1 minute") \
                                        .start()

    # Aguarda o término das consultas (útil em scripts de produção)
    # query_console.awaitTermination()
    # query_parquet.awaitTermination()
    ```

## **Parte VI: Análise Avançada e Machine Learning (Breve Visão Geral para Contexto)**

* **Capítulo 24: Visão Geral da Análise Avançada e Machine Learning** (Spark The Definitive Guide)
    * O Spark inclui o **MLlib**, uma biblioteca robusta para Machine Learning que opera sobre DataFrames [Spark The Definitive Guide, 38, 409]. Embora o foco deste resumo seja ETL, é importante saber que você pode estender seus pipelines para incluir etapas de engenharia de features, treinamento e avaliação de modelos de ML em escala [Spark The Definitive Guide, 406, 567].
    * **Transformers e Estimators:** São os blocos de construção para ML, permitindo a **engenharia de features** (pré-processamento de dados para ML) e o treinamento de modelos [Spark The Definitive Guide, 296-302, 410, 427, 573]. A engenharia de features é, em sua essência, uma forma de transformação de dados [Data Analysis with Python and PySpark, 46, 567].

---

Este resumo foca nos aspectos práticos e no fluxo de trabalho de um engenheiro de dados usando PySpark, com exemplos de código para as tarefas mais comuns de ETL, desde a ingestão de dados até o carregamento, passando por limpeza, transformação e otimização.
