# Engenharia de dados com Python  

## Transformação e limpeza de dados com Pandas  
* Antes de tudo, precisamos instalar e carregar a biblioteca pandas
    ```
    %pip install pandas
    import pandas as pd
    ```
* A limpeza de dados é necessária pois, os dados devem ser padronizados antes de serem preparados e modelados para a extração de insights e para a montagem de produtos de dados. Por padronizados, quero dizer que os dados devem possuir uma boa qualidade, isto implica em:
1. Variáveis tipo de dados tratados (str, int, float, bool, datetime, category)
2. Variáveis com valore nulos tratados (null, NA, NaN, "-", " ") 
3. Variáveis com valores duplicados tratados
4. Variáveis com valores padronizados no mesmo formato (1, "um", "PE", "Pernambuco", "01 de janeiro de 2025", 01/01/2025)
5. Tabelas na granularidade correta e com o conteúdo desejado

### Tratamentos de tipo de dado
* Verificamos o tipo de dados das variáveis de um DataFrame utilizando o atributo .dtypes ou utilizando o método .info().
* Convertemos o tipo de dado de uma variável para outro tipo através do método [.astype()](https://pandas.pydata.org/docs/reference/api/pandas.Series.astype.html), passando como parâmetro uma string indicando o tipo de dados para oqual desejamos converter a nossa variável. Também podemos utilizar os métodos .to_"tipo do dado"() para tentar realizar a conversão dos tipos.
* Nem sempre conseguimos converter os tipos das nossas variáveis de forma direta, e precisamos realizar tratamentos e transformações nestas, antes de prosseguir com as conversões.

#### Tratamento em variáveis do tipo texto (string)
* Tratamos problemas para este tipo através dos métodos do módulo .str. O módulo [.str](https://pandas.pydata.org/docs/reference/api/pandas.Series.str.capitalize.html) possui diversos métodos que nos permitem padronizar e transformar os valores do tipo texto de um objeto Series (coluna de um DataFrame), e certamente vai sair nossa principal ferramenta para lidar com problemas que envolvam esse tipo de dado.

#### Tratamento em variáveis do tipo data (datetime)
* Tratamos problemas para este tipo através dos métodos do módulo .dt. O módulo [.dt](https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.date.html) possui diversos métodos que nos permitem padronizar e transformar os valores do tipo datetime de um objeto Series (coluna de um DataFrame), e certamente vai sair nossa principal ferramenta para lidar com problemas que envolvam esse tipo de dado.
* Através deste módulo conseguimos formatar datas e extrair os valores do ano, mês, hora, de um valor datetime.

#### Validando as transformações realizadas
* Uma forma de validar o resultado das nossas transformações é utilizando expressões lógicas em conjunto da palavra reservada assert. Através de expressões deste tipo, o python irá testar se o resultado da nossa expressão é True ou False, e irá retornar um erro de assertividade (Assertion Error), caso o resultado avaliado tenha sido False. Isto nos dará um indicativo se a transformação ocorreu da forma adequada ou não.
      
    ```
    assert df['valor'].dtype == 'float'
    ```
