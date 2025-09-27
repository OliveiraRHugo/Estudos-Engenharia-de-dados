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

#### Tratamento em variáveis do tipo categoria (category)
* Tratamos problemas para este tipo através dos métodos do módulo .cat. O módulo [.cat](https://pandas.pydata.org/docs/user_guide/categorical.html) possui diversos métodos que nos permitem trabalhar com valores do tipo categórico de um objeto Series (coluna de um DataFrame), e certamente vai sair nossa principal ferramenta para lidar com problemas que envolvam esse tipo de dado.
* Uma forma interessante de trabalhar com categorias é sempre ter um DataFrame ou uma tabela com os possíveis valores da categoria com a qual estamos trabalhando e utilizar ela como referência para tratar o dado. Nossos principais aliados nestes momento são a função set(), o método .difference(), e o método .isin()
* a função set() será utilizada na nossa tabela de categorias de referência. set, ou conjuntos, são uma estrutura de dados do python onde não é possível haver valores duplicados, e todo set, só pode possuir valores do mesmo tipo. Então, ele irá garantir que nenhum valor da nossa tabela de referência está duplicado.
* O método difference() vai ser a estrela do show, ele será utilizando para comparar os valores diferentes que ocorrem na nossa tabela de referência com os valores que ocorrem na nossa series (coluna do dataframe), e retornará os valores discrepantes/inconsistentes.
* Por fim, utilizamos o método isin() para identificar os registros onde os valores discrepantes ocorrem, para que possamos tratá-los da forma adequada.
    ```
    #  Carrega os dados
    df = pd.read_csv('tabela_principal.csv')
    categorias = pd.read_csv('categorias.csv')
    
    #  Converte a variável para o tipo categoria e salva os valores em um conjunto
    categorias['valores'] = categorias['valores'].astype('category')
    referencia_categorias = set(categorias['valores'])
    
    #  Verifica os valores diferentes dos valores das categorias que estão sendo utilizados em produção, e os registros dos mesmos
    valores_inconsistentes = referencia_categorias.difference(df['categoria_producao'])
    print(valores_inconsistentes)
    registros_inconsistentes = df['categoria_producao'].isin(valores_inconsistentes)
    ```
* Outra forma interessante de trabalhar com variáveis categóricas é através de mapeamentos "de-para". Variáveis categóricas normalmente são do tipo texto, e não possuem um armazenamento otimizado. Para sanar esse problema, podemos utilizar o método np.where do numpy que funciona como um case, ou ainda, criar um dicionário de dados onde a chave é o valor da categoria e o valor do dicionário é o valor numérico que queremos atribuir à categoria, e depois utilizar o método nativo .map() para mapear estes valores.

#### Tratamento em variáveis do tipo data (datetime)
* Tratamos problemas para este tipo através dos métodos do módulo .dt. O módulo [.dt](https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.date.html) possui diversos métodos que nos permitem padronizar e transformar os valores do tipo datetime de um objeto Series (coluna de um DataFrame), e certamente vai sair nossa principal ferramenta para lidar com problemas que envolvam esse tipo de dado.
* Através deste módulo conseguimos formatar datas e extrair os valores do ano, mês, hora, de um valor datetime.

#### Validando as transformações realizadas
* Uma forma de validar o resultado das nossas transformações é utilizando expressões lógicas em conjunto da palavra reservada assert. Através de expressões deste tipo, o python irá testar se o resultado da nossa expressão é True ou False, e irá retornar um erro de assertividade (Assertion Error), caso o resultado avaliado tenha sido False. Isto nos dará um indicativo se a transformação ocorreu da forma adequada ou não.
      
    ```
    assert df['valor'].dtype == 'float'
    ```
#### Lindando com valores nulos ou valores duplicados
* Um recurso que pode nos ajudar a identificar valores nulos ou duplicados é ordernar o nosso conjunto de dados, fazemos isso através do método [.sort_valueS()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html)
* Podemos ainda agrupar os dados e realizar operações estatísticas para analisar a qualidade dos dados. Fazemos isto utilizando uma combinação do método .groupby() com o método .agg()
```
colunas = ['a','b','c']
medidas = ['valor':'mean', 'valor':'std']
df_agrupado = df.groupby(by = colunas).agg(medidas).reset_index()
```
* Conseguimos identificar facilmente valores duplicados utilizando os métodos .info() e .duplicated()
* Podemos utilizar .duplicated() como um filtro, para observar os registros dos valores duplicados, ou, podemos ainda realizar a soma do seu resultado para apenas observar o número de registros duplicados
* Podemos ainda simplesmente remover todos os valores duplicados através do .drop_duplicates()
    ```
    duplicados = df[df.duplicated()]
    #ou
    n_duplicados = df.duplicated().sum()
    ```
* Conseguimos identificar facilmente valores nulos utilizando os métodos .info() e .isna()
* Podemos utilizar .isna() como um filtro, para observar os registros dos valores nulos, ou, podemos ainda realizar a soma do seu resultado para apenas observar o número de registros vazios
* Podemos ainda simplesmente remover todos os valores vazios através do .dropna()
    ```
    vazios = df[df.isna()]
    #ou
    n_vazios = df.isna().sum()
    ```
* Para preencher valores vazios, e substituí-los por outros valores, utilizamos o método [.fillna()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.fillna.html)
