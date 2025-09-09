# Engenharia de dados com Python  

## Ingestão de dados com Pandas  
* Antes de tudo, precisamos instalar e carregar a biblioteca pandas
    ```
    %pip install pandas
    import pandas as pd
    ```
### Extraindo dados de arquivos simples  
* Principal Método: **pd.read_csv()**  
  * Principais [Parâmetros](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html):   
      #### Parâmetros de extração
      * **sep**: Separador utilizado no arquivo; pd.read_csv(sep= ';')   
      * **usecols**: Define as colunas a serem utilizadas a partir de uma lista de nomes ou uma lista numérica (índice das colunas); pd.read_csv(usecols=[0,1,2])
      * **nrows**: Define a quantidade de linhas do arquivo a serem lidas; pd.read_csv(nrows=50)  
        * Seu uso conjunto com o parâmetro skip rows pode ser útil para processar dados em lotes:  
            * **skiprows**: Define um número de linhas a ser puladas na leitura dos dados; pd.read_csv(skiprows=1000)  
                Combinando:  
                * O código abaixo faz com que sejam lidas 500 linhas após as 1000 primeiras, ignorando o cabeçalho da tabela, e renomeia o nome das colunas da tabela.  
                ```
                dataframe = pd.read_csv(
                                       'tabela.csv',
                                       nrows = 500,
                                       skiprows = 1000,
                                       header = None,
                                       names = ['col1','col2','col3','col4']
                                       )
                ```
      * **chunksize** : Define a quantidade de linhas a serem lidas por vez, visando ler um arquivo em lotes; pd.read_csv(chunksize = 1000)
      #### Parâmetros de formato e de transformação          
      * **dtype**: Define o tipo de dado de uma coluna; pd.read_csv(dtype = {'col1' : str})  
          * Você pode verificar os tipos de dados de um dataframe através do atributo **.dtypes**
      * **date_format**: Define o formato dos campos de data; pd.read_csv(date_format = '%d/%m/%y')  
      * **na_values**: Define como valores em branco devem ser tratados; pd.read_csv(na_values={'col1' : 0})
### Extraindo dados de softwares de planilhas
* É boa prática, antes de ler os arquivos de planilhas, **remover toda a formatação da aba que será consumida**.  
* Principal Método: **pd.read_excel()**  
  * Principais [Parâmetros](https://pandas.pydata.org/docs/reference/api/pandas.read_excel.html):   
      #### Parâmetros de extração
      * **usecols**: Define as colunas a serem utilizadas a partir de uma lista de nomes ou uma lista numérica (índice das colunas); pd.read_excel(usecols=[0,1,2])
      * **nrows**: Define a quantidade de linhas do arquivo a serem lidas; pd.read_excel(nrows=50)  
        * Seu uso conjunto com o parâmetro skip rows pode ser útil para processar dados em lotes:  
            * **skiprows**: Define um número de linhas a ser puladas na leitura dos dados; pd.read_excel(skiprows=1000)  
                Combinando:  
                * O código abaixo faz com que sejam lidas 500 linhas após as 1000 primeiras, ignorando o cabeçalho da tabela, e renomeia o nome das colunas da tabela.  
                ```
                dataframe = pd.read_excel(
                                       'tabela.xlsx',
                                       nrows = 500,
                                       skiprows = 1000,
                                       header = None,
                                       names = ['col1','col2','col3','col4']
                                       )
                ```
      ##### Extraindo dados de múltiplas abas de uma planilha
      * Por padrão, pd.read_excel() lê a apenas a primeira aba de uma planilha, mas podemos modificar isto com o uso do parâmetro **sheet_name**, visando carregar múltiplas abas. Tenha em mente que qualquer parâmetro passado como argumento para o método pd._read_excel, será aplicado para todas as abas:
          ```
          pd.read_excel(sheet_name=['aba1','aba2'])
          ```
      * Uma forma de carregar todas as abas sem listá-las nome por nome é utilizando a expressão **SheeT_name=None**. O uso desta expressão nos retornará um dicionário, onde a chave de cada dicionário é o nome da aba correspondente, e o valor do dicionário é o dataframe que representa o conteúdo desta aba.
      Para combinar os dataframes das abas em uma única aba, fazemos:
          ```
          # Dicionário de dataframes inicial
          excel = pd.read_excel(sheet_name=None)

          # Cria um dataframe vazio para carregar todas as planilhas
          df = pd.DataFrame()

          # Itera entre os dataframes do dicionário
          for nome_aba, dados in excel.items():
              # Registra de qual aba vieram os dados
              dados['fonte'] = nome_aba

              # Unifica os dataframes em um só
              df = pd.concat([df, dados])
          ```
      #### Parâmetros de formato e de transformação   
      ##### Trabalhando com dados Booleanos (True or False)
      * Dados booleanos são carregados tipicamente como valores numéricos, float, pois são convertidos para 0 e 1. Para manter os booleanos como tal, precisamos explicitamente indicar isto:
          ```
          dtype={'coluna_booleano' : bool}
          ```
      ###### Atenção!
      * Valores NA, ou valores faltantes, ao serem convertidos para valores booleanos, por padrão são transformados em valores True. No caso, é de extrama importância tratar os valores nulos, antes de converter os valores em valores booleanos.
      * Outra forma de tratar é explicitamente indicar valores associados aos valores True, e aos valores False, através dos parâmetros **true_values** e **false_values**:
          ```
          dtype={'coluna_booleano' : bool},
          true_values=['Sim'],
          false_values=['Não']
          ```
      * Verifique se um dataframe possui valores em branco através da sequência de métodos **.isna().sum()**. 
      ##### Trabalhando com datas, tipo datetime    
      * Datas por padrão são interpretadas como dados de texto, e portanto, são carregados como dados do tipo object no pandas.  
      * Para que haja o carregamento da forma correta, **devemos utilizar** o parâmetro **parse_dates**. Em situações onde estamos lidando com formatos de data não convencionais, usamos o método **.to_datetime()**, especificando o formato de data utilizando uma máscara no formato de string:
          ```
          parse_dates=['col_data_1','col_data_2', ['col_data_3','col_hora_data_3']]
          #ou                             
          df.to_datetime(df['col_data_1'], format='%m/%d/%Y') #  formato atual do dado, não é necessariamente o formato desejado!
          ```
### Extraindo dados de bancos de dados     
* O processo de extrair dados de um banco de dados envolve 2 etapas:
      1. Criar uma conexão com o banco de dados
      2. Executar uma consulta ao banco de dados
* Para isto, utilizamos a biblioteca python SQLALCHEMY
* A principal função da biblioteca é a create_engine() . Esta função é a responsável por fazer conectar o script ao sistema de banco de dados, através de um string de conexão (basicamente o endereço do banco de dados, tipicamente composto por ip, porta, driver, e sgbd), e das informações de autenticação (usuario e senha do banco de dados). O farmato da string de conexão varia de acordo com o sgbd utilizado.
* Instanciada a nossa engine de conexão, utilizamos o método pd.read_sql() passando o código da consulta SQL e o nossa engine como parâmetros para conseguir acessar os dados.
    ```
    import pandas as pd
    from sqlalchemy import create_engine
    
    connection = create_engine("string de conexão")
    query = """
            select *
            from atendimentos;"""
    df = pd.read_sql(query, connection) #(query, engine)
    ```
    
### Extraindo dados de arquivos JSON e APIs  
#### JSON
* Java Script Object Notation (JSON) é uma forma padrão de organizar dados de APIS, Bancos de dados NoSQL, e documentos da WEB. Neste modelo de dados, os dados são organizados em coleções de objetos, organizados no modelo atributo-valor, acessíveis num formato similar ao de um dicionário python (chave-valor). Estes objetos podem possuir outros objetos aninhados, tornando, apesar de performática a sua consulta, um pouco trabalhoso o acesso aos dados.
* Tendo em mente a organização no formato de documentos, desse tipo de dado, categorizamos estes como dados não tabulares, e portanto, semi-estruturados (possuem uma estrutura, apesar de não ser uma no formato de tabela), pois podem variar e muito de forma. O pandas, no caso, através de um processo de inferência, tenta converter a estrutura do documento JSON num formato tabular.
* Um arquivo JSON pode ser organizado de diversas formas, mas as mais comuns são:
      1. Orientada a registros (linhas): Cada dicionário representa uma linha da tabela, e o arquivo JSON como um todo, é representado como uma lista destes dicionários.
      2. Orientada a colunas: Todo o JSON é um dicionário composto por outros dicionários, onde cada dicionário é uma coluna, e esta coluna possui como chave o índice da linha da tabela, e o valor de fato daquele registro para aquela coluna.
* Conseguimos indicar com que tipo de orientação/forma estamos trabalhando através do parâmetro [orient](https://pandas.pydata.org/docs/reference/api/pandas.read_json.html#:~:text=strings%20is%20deprecated.-,orient,-str%2C%20optional) 
* Para ler o conteúdo de uma fonte que utiliza JSON, utilizamos o método .read_json()
#### APIs
* Uma API é um programa que busca padronizar a forma como a qual outros sistemas podem se comunicar com um sistema de uma empresa
* APIs são a principal origem do consumo de dados no formato JSON
* Cada API possui regras prórpias de como consumir seu conteúdo, mas elas tendem a seguir uma estrutura padrão.
* A biblioteca mais popular para consumir APIs com python é a [Requests](https://requests.readthedocs.io/en/latest/)
* As API trabalham com requisições feitas através de uma URL passada como string, muito similar às strings de conexão de banco de dados tradicionais.
* O principal método pra obter dados utilizando a bilioteca citada é o requests.get(), o qual recebe a string URL da API
* Tal método possui 2 parâmetros chaves:
  1. params : recebe um dicionário de parâmetros e valores que personalizam a requisição feita à API
  2. hearders: recebe um dicionário que tipicamente é utilizado para que o usuário consumidor dos dados seja autenticado para consumir a API (normalmente é pedido uma chave de conexão)
* O resultado do uso de tal método, combinado com os parâmetros citados, é um objeto response (resposta), contendo os dados e metadados do objeto no formato JSON.
* utilizar response.json() irá retornar apenas os dados no formato de dicionário. Para converter estes dados num formato tabular, utilizamos pd.DataFrame para converter esse dicionário em um Pandas DataFrame.
  ```
      import requests
      import pandas as pd
      api_url = "https://api_zoologico.com/v1/search"
      params = {"dieta":"carnivora","meio_locomocao":"terrestre"}
      headers = {f"Autorização:{chave_acesso}"}
      response = requests.get(
                      api_url,
                      params = params,
                      headers = headers
                  )
      dados_api = response.json()
      df = pd.DataFrame(dados_api)
  ```
  

