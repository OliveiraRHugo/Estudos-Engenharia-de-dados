# Engenharia de dados com Python  

## Pipeline de dados e ETL  

* Um pipeline de dados é um **conjunto de tarefas**, e portanto um processo, **que envolve a extração, a transformação, e a disponibilização de dados** para consumo.
* Existem 2 modelos principais de pipelines de dados
  1. ETL (Extract, Transform, Load): Formato mais tradicional de pipeline de dados, e é caracterizado por realizar as tarefas de um pipeline na seguinte ordem: extração dos dados, transformação dos dados, carga dos dados.
  2. **ELT (Extract, Load, Transform): Formato mais recente de pipeline de dados, priorizando a disponibilidade dos dados no Data Warehouse**. É caracterizado por realizar as tarefas de um pipeline na seguinte ordem: extração dos dados, carga dos dados, transformação dos dados.
* Num pipeline de dados, **tipicamente representamos cada parte do processo do ETL como funções, as quais irão conter todas as operações dos seus respectivos processos**, seja de extração, de transformação, ou de carga.
  ```
  # Extract data from the raw_data.csv file
  extracted_data = extract(file_name="raw_data.csv")
  
  # Transform the extracted_data
  transformed_data = transform(data_frame=extracted_data)
  
  # Load the transformed_data to cleaned_data.csv
  load(data_frame=transformed_data, target_table="cleaned_data")
  
  ```

### Principais métodos por etapa do processo de ETL

#### E (Extração)
* Aqui estão **todos os métodos e recursos que envolvem a leitura de arquivos ou de dados: .read_csv(), read_sql(), read_...**

#### T (Transformação)
* Aqui estão **todos os métodos que permitem a modificação dos dados**, dentre estes tipos de operação estão: **operações de filtro, substituição, conversão de tipo, criação de novos campos, tratamento de valores nulos e não nulos, etc...**

#### L (Carga)
* Aqui estão **todos os métodos que permitem a escrita, ou a carga, de um DataFrame em um arquivo: .to_csv(), .to_sql(), etc...**
* Durante o processo de carga é necessário validar se os dados foram de fato escritos na nova fonte. Uma forma de validar isso é utilizando o pacote [os](https://docs.python.org/3/library/os.html), e verificar se no caminho apontado para a escrita dos novos dados, está contido os arquivos com os novos dados:
  ```
  import os
  arquivo_existe = os.path.exists("novo_arquivo_novos_dados.csv")
  print(arquivo_existe)
  ```
  
### Monitorando pipelines
* O nosso pipeline pode ter sua execução interrompida por diversos motivos, desde alterações na fonte dos dados até por remoções de bibliotecas de desenvolvimento. Tendo isso em mente, se faz necessário o monitoramento da execução do mesmo, visando sanar os problemas que impedem a disponibilidade dos dados antes dos usuários sentirem a dor advinda destes.
* O principal recurso para monitoramento de pipelines são os logs (registros). Eles documentam a performance do pipeline, e nos dão uma ideia inicial do problema que ocorreu e como tratá-lo.
* Para criar logs, utilizamos a biblioteca [logging](https://docs.python.org/3/library/logging.html)
* O uso de logs com expressões try e catch do python formam a solução mais básica de monitoramento de pipelines. Use sempre os logs na etapa do catch.
