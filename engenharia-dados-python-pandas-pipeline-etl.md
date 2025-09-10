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
