```python
import pandas as pd
```


```python
df = pd.read_csv(
    "../dados/datatran2025.csv",
    sep=";",
    encoding="latin1",
    low_memory=False
)
```


```python
df.shape
```




    (72529, 30)



Então confirmamos que a nossa base tem 72.529 registros e 30 colunas. ✅


```python
df.columns
```




    Index(['id', 'data_inversa', 'dia_semana', 'horario', 'uf', 'br', 'km',
           'municipio', 'causa_acidente', 'tipo_acidente',
           'classificacao_acidente', 'fase_dia', 'sentido_via',
           'condicao_metereologica', 'tipo_pista', 'tracado_via', 'uso_solo',
           'pessoas', 'mortos', 'feridos_leves', 'feridos_graves', 'ilesos',
           'ignorados', 'feridos', 'veiculos', 'latitude', 'longitude', 'regional',
           'delegacia', 'uop'],
          dtype='object')



Tem uma observação importante: o resultado aparece como dtype='object' no final, mas isso não significa que todas as colunas sejam do tipo object. Esse dtype é o tipo do objeto que contém os nomes das colunas. Vamos verificar os tipos reais agora.


```python
df.dtypes
```




    id                         int64
    data_inversa              object
    dia_semana                object
    horario                   object
    uf                        object
    br                         int64
    km                        object
    municipio                 object
    causa_acidente            object
    tipo_acidente             object
    classificacao_acidente    object
    fase_dia                  object
    sentido_via               object
    condicao_metereologica    object
    tipo_pista                object
    tracado_via               object
    uso_solo                  object
    pessoas                    int64
    mortos                     int64
    feridos_leves              int64
    feridos_graves             int64
    ilesos                     int64
    ignorados                  int64
    feridos                    int64
    veiculos                   int64
    latitude                  object
    longitude                 object
    regional                  object
    delegacia                 object
    uop                       object
    dtype: object



O object não significa necessariamente que está errado.

Significa que o Pandas carregou aquela coluna como texto. Algumas provavelmente são realmente texto, como:

uf
municipio
causa_acidente
tipo_acidente

Mas outras merecem nossa atenção:

data_inversa → provavelmente vamos transformar em data
horario → podemos tratar como horário
km → é uma informação numérica, mas veio como object

👉 Isso já é um problema de qualidade/tipo de dado que podemos documentar no projeto.


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 72529 entries, 0 to 72528
    Data columns (total 30 columns):
     #   Column                  Non-Null Count  Dtype 
    ---  ------                  --------------  ----- 
     0   id                      72529 non-null  int64 
     1   data_inversa            72529 non-null  object
     2   dia_semana              72529 non-null  object
     3   horario                 72529 non-null  object
     4   uf                      72529 non-null  object
     5   br                      72529 non-null  int64 
     6   km                      72529 non-null  object
     7   municipio               72529 non-null  object
     8   causa_acidente          72529 non-null  object
     9   tipo_acidente           72529 non-null  object
     10  classificacao_acidente  72528 non-null  object
     11  fase_dia                72529 non-null  object
     12  sentido_via             72529 non-null  object
     13  condicao_metereologica  72529 non-null  object
     14  tipo_pista              72529 non-null  object
     15  tracado_via             72529 non-null  object
     16  uso_solo                72529 non-null  object
     17  pessoas                 72529 non-null  int64 
     18  mortos                  72529 non-null  int64 
     19  feridos_leves           72529 non-null  int64 
     20  feridos_graves          72529 non-null  int64 
     21  ilesos                  72529 non-null  int64 
     22  ignorados               72529 non-null  int64 
     23  feridos                 72529 non-null  int64 
     24  veiculos                72529 non-null  int64 
     25  latitude                72529 non-null  object
     26  longitude               72529 non-null  object
     27  regional                72527 non-null  object
     28  delegacia               72507 non-null  object
     29  uop                     72491 non-null  object
    dtypes: int64(10), object(20)
    memory usage: 16.6+ MB
    

🔎 O que estamos vendo

A base tem:

72.529 registros
30 colunas
O índice vai de 0 até 72528
Quase todas as colunas mostradas estão com 72.529 valores não nulos

Mas olha esta linha:

classificacao_acidente    72528 non-null

A base tem 72.529 linhas, mas essa coluna tem 72.528 valores preenchidos.

👉 Portanto, já encontramos pelo menos 1 valor ausente nessa coluna.

Isso é exatamente o tipo de coisa que precisamos descobrir na etapa de diagnóstico.

⚠️ E tem outro detalhe interessante

Também confirmamos algo que já tínhamos percebido:

km       object

O km provavelmente deveria ser tratado como número, mas foi carregado como texto.


```python
df.isnull().sum()
```




    id                         0
    data_inversa               0
    dia_semana                 0
    horario                    0
    uf                         0
    br                         0
    km                         0
    municipio                  0
    causa_acidente             0
    tipo_acidente              0
    classificacao_acidente     1
    fase_dia                   0
    sentido_via                0
    condicao_metereologica     0
    tipo_pista                 0
    tracado_via                0
    uso_solo                   0
    pessoas                    0
    mortos                     0
    feridos_leves              0
    feridos_graves             0
    ilesos                     0
    ignorados                  0
    feridos                    0
    veiculos                   0
    latitude                   0
    longitude                  0
    regional                   2
    delegacia                 22
    uop                       38
    dtype: int64



🔎 Valores ausentes

O resultado mostra que praticamente todas as colunas estão com 0 valores ausentes.

A exceção que já tínhamos encontrado aparece aqui:

classificacao_acidente    1

Ou seja:

classificacao_acidente possui exatamente 1 valor ausente em 72.529 registros.


```python
df[df["classificacao_acidente"].isnull()]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>data_inversa</th>
      <th>dia_semana</th>
      <th>horario</th>
      <th>uf</th>
      <th>br</th>
      <th>km</th>
      <th>municipio</th>
      <th>causa_acidente</th>
      <th>tipo_acidente</th>
      <th>...</th>
      <th>feridos_graves</th>
      <th>ilesos</th>
      <th>ignorados</th>
      <th>feridos</th>
      <th>veiculos</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>regional</th>
      <th>delegacia</th>
      <th>uop</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>652519</td>
      <td>2025-01-01</td>
      <td>quarta-feira</td>
      <td>07:50:00</td>
      <td>CE</td>
      <td>116</td>
      <td>546,2</td>
      <td>PENAFORTE</td>
      <td>Pista esburacada</td>
      <td>Colisão frontal</td>
      <td>...</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>1</td>
      <td>6</td>
      <td>-7,812288</td>
      <td>-39,08333306</td>
      <td>SPRF-CE</td>
      <td>DEL05-CE</td>
      <td>UOP03-DEL05-CE</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 30 columns</p>
</div>



Essa é exatamente a linha que possui o único valor ausente em classificacao_acidente.
Como só existe 1 valor ausente em 72.529 registros, isso representa uma quantidade praticamente irrelevante da base. Mas, para o projeto, precisamos registrar e justificar a decisão.

Antes de decidir, vamos verificar se existe alguma relação entre classificacao_acidente e outras informações desse registro que possa nos ajudar.


```python
df[df["id"] == 652519].T
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>id</th>
      <td>652519</td>
    </tr>
    <tr>
      <th>data_inversa</th>
      <td>2025-01-01</td>
    </tr>
    <tr>
      <th>dia_semana</th>
      <td>quarta-feira</td>
    </tr>
    <tr>
      <th>horario</th>
      <td>07:50:00</td>
    </tr>
    <tr>
      <th>uf</th>
      <td>CE</td>
    </tr>
    <tr>
      <th>br</th>
      <td>116</td>
    </tr>
    <tr>
      <th>km</th>
      <td>546,2</td>
    </tr>
    <tr>
      <th>municipio</th>
      <td>PENAFORTE</td>
    </tr>
    <tr>
      <th>causa_acidente</th>
      <td>Pista esburacada</td>
    </tr>
    <tr>
      <th>tipo_acidente</th>
      <td>Colisão frontal</td>
    </tr>
    <tr>
      <th>classificacao_acidente</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>fase_dia</th>
      <td>Pleno dia</td>
    </tr>
    <tr>
      <th>sentido_via</th>
      <td>Crescente</td>
    </tr>
    <tr>
      <th>condicao_metereologica</th>
      <td>Céu Claro</td>
    </tr>
    <tr>
      <th>tipo_pista</th>
      <td>Simples</td>
    </tr>
    <tr>
      <th>tracado_via</th>
      <td>Reta</td>
    </tr>
    <tr>
      <th>uso_solo</th>
      <td>Não</td>
    </tr>
    <tr>
      <th>pessoas</th>
      <td>6</td>
    </tr>
    <tr>
      <th>mortos</th>
      <td>1</td>
    </tr>
    <tr>
      <th>feridos_leves</th>
      <td>1</td>
    </tr>
    <tr>
      <th>feridos_graves</th>
      <td>0</td>
    </tr>
    <tr>
      <th>ilesos</th>
      <td>1</td>
    </tr>
    <tr>
      <th>ignorados</th>
      <td>4</td>
    </tr>
    <tr>
      <th>feridos</th>
      <td>1</td>
    </tr>
    <tr>
      <th>veiculos</th>
      <td>6</td>
    </tr>
    <tr>
      <th>latitude</th>
      <td>-7,812288</td>
    </tr>
    <tr>
      <th>longitude</th>
      <td>-39,08333306</td>
    </tr>
    <tr>
      <th>regional</th>
      <td>SPRF-CE</td>
    </tr>
    <tr>
      <th>delegacia</th>
      <td>DEL05-CE</td>
    </tr>
    <tr>
      <th>uop</th>
      <td>UOP03-DEL05-CE</td>
    </tr>
  </tbody>
</table>
</div>



Agora temos uma informação importante: esse acidente teve 1 morto, então a classificação, pela lógica da própria variável, seria “Com Vítimas Fatais”. A documentação da PRF define essa coluna como uma classificação de gravidade, incluindo “Sem Vítimas”, “Com Vítimas Feridas”, “Com Vítimas Fatais” e “Ignorado”.
O dado original está ausente → isso precisa ser registrado.
Podemos inferir o valor a partir de mortos = 1 → isso pode ser uma decisão de tratamento, mas precisamos justificá-la.


```python
pd.crosstab(df["classificacao_acidente"], df["mortos"])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>mortos</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>16</th>
    </tr>
    <tr>
      <th>classificacao_acidente</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Com Vítimas Fatais</th>
      <td>0</td>
      <td>4631</td>
      <td>434</td>
      <td>93</td>
      <td>28</td>
      <td>13</td>
      <td>3</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Com Vítimas Feridas</th>
      <td>56181</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Sem Vítimas</th>
      <td>11138</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



A tabela mostra uma relação perfeitamente consistente:
mortos = 0 → acidentes classificados como Com Vítimas Feridas ou Sem Vítimas
mortos > 0 → acidentes classificados como Com Vítimas Fatais
Não encontramos, nessa tabela, nenhum caso de acidente com mortos sendo classificado como outra coisa.

Então, para o nosso registro ID 652519, que possui mortos = 1, podemos justificar o preenchimento como "Com Vítimas Fatais".

### Tratamento do valor ausente em `classificacao_acidente`

Foi identificado apenas um registro com valor ausente na coluna `classificacao_acidente`, correspondente ao acidente de ID 652519.

Ao analisar o registro, verificou-se que o acidente possui 1 vítima fatal (`mortos = 1`). Além disso, a análise da relação entre `classificacao_acidente` e `mortos` mostrou que os registros classificados como "Com Vítimas Fatais" possuem pelo menos uma vítima fatal.

Dessa forma, o valor ausente foi preenchido como "Com Vítimas Fatais", utilizando como critério a informação disponível na própria base.


```python
df.duplicated().sum()

```




    np.int64(0)



72.529 registros analisados e nenhuma duplicidade exata encontrada.


```python
df["id"].duplicated().sum()
```




    np.int64(0)



Significa que não há nenhum ID repetido.

Então, até agora nosso diagnóstico de duplicidades ficou:

Linhas totalmente duplicadas: 0
IDs duplicados: 0


```python
(df.isnull().sum() / len(df) * 100).sort_values(ascending=False)
```




    uop                       0.052393
    delegacia                 0.030333
    regional                  0.002758
    classificacao_acidente    0.001379
    dia_semana                0.000000
    id                        0.000000
    br                        0.000000
    km                        0.000000
    causa_acidente            0.000000
    municipio                 0.000000
    tipo_acidente             0.000000
    horario                   0.000000
    uf                        0.000000
    data_inversa              0.000000
    condicao_metereologica    0.000000
    sentido_via               0.000000
    fase_dia                  0.000000
    tipo_pista                0.000000
    mortos                    0.000000
    tracado_via               0.000000
    uso_solo                  0.000000
    pessoas                   0.000000
    ilesos                    0.000000
    feridos_graves            0.000000
    feridos_leves             0.000000
    ignorados                 0.000000
    latitude                  0.000000
    veiculos                  0.000000
    feridos                   0.000000
    longitude                 0.000000
    dtype: float64




O que esse resultado mostra

As quatro colunas que apresentam ausência são:
| Coluna                   | Percentual ausente |
| ------------------------ | -----------------: |
| `uop`                    |            0,0524% |
| `delegacia`              |            0,0303% |
| `regional`               |            0,0028% |
| `classificacao_acidente` |            0,0014% |
As demais colunas apresentam 0% de valores ausentes.

Isso é interessante porque mostra que os dados estão muito completos. Mesmo a coluna com maior percentual de ausência (uop) tem apenas cerca de 0,05% dos registros sem informação.


```python
df["uf"].value_counts()
```




    uf
    MG    9570
    SC    8186
    PR    7630
    RJ    6428
    RS    4899
    SP    4683
    BA    4108
    GO    3196
    PE    3013
    ES    2642
    MT    2636
    PB    1978
    MS    1654
    RN    1648
    PI    1490
    RO    1452
    CE    1302
    MA    1262
    PA    1117
    DF    1011
    TO     677
    AL     629
    SE     589
    AC     280
    AP     169
    RR     142
    AM     138
    Name: count, dtype: int64



Esse resultado nos dá duas informações importantes. 👀

1. Não aparecem categorias estranhas em uf

Temos as 27 UFs brasileiras, todas representadas pelas siglas padrão:

MG: 9.570
SC: 8.186
PR: 7.630
RJ: 6.428
RS: 4.899
SP: 4.683
...
AM: 138

Então, não identificamos inconsistência aparente nas categorias de uf. Não há, por exemplo, "Santa Catarina" misturado com "SC".


```python
df["tipo_acidente"].value_counts()
```




    tipo_acidente
    Colisão traseira                  14360
    Saída de leito carroçável         10209
    Colisão transversal                9306
    Colisão lateral mesmo sentido      7885
    Tombamento                         6351
    Colisão com objeto                 5109
    Colisão frontal                    4739
    Queda de ocupante de veículo       3450
    Atropelamento de Pedestre          3057
    Colisão lateral sentido oposto     2152
    Incêndio                           1771
    Capotamento                        1373
    Engavetamento                      1233
    Atropelamento de Animal            1133
    Eventos atípicos                    287
    Derramamento de carga               107
    Sinistro pessoal de trânsito          7
    Name: count, dtype: int64



Foram encontradas 17 categorias.
Aqui não aparece nenhuma categoria obviamente inconsistente, como valores vazios, N/A, "não informado" ou categorias duplicadas por diferença de escrita.


```python
df["causa_acidente"].value_counts()
```




    causa_acidente
    Ausência de reação do condutor                               11469
    Reação tardia ou ineficiente do condutor                     10799
    Acessar a via sem observar a presença dos outros veículos     7097
    Condutor deixou de manter distância do veículo da frente      4413
    Velocidade Incompatível                                       4088
                                                                 ...  
    Faróis desregulados                                              9
    Redutor de velocidade em desacordo                               8
    Semáforo com defeito                                             8
    Sistema de drenagem ineficiente                                  2
    Sinalização encoberta                                            2
    Name: count, Length: 69, dtype: int64



O que vale investigar é se existem:

categorias muito parecidas que poderiam representar a mesma situação;
diferenças de maiúsculas/minúsculas;
espaços extras;
valores como "Não informado", "Ignorado" etc.;
categorias que possam dificultar uma análise posterior.


```python
df["km"].head(20)
```




    0       225
    1     546,2
    2      88,2
    3        74
    4       471
    5       669
    6       376
    7     207,4
    8     708,5
    9       7,4
    10      327
    11      530
    12     51,8
    13    708,5
    14    117,8
    15      125
    16    105,6
    17     1102
    18    137,7
    19    627,5
    Name: km, dtype: object



Agora encontramos um problema de qualidade de dados bem claro. 🔎

A coluna km está como: object

Por que isso acontece?

O problema provavelmente está no separador decimal.

Na base brasileira, os valores decimais aparecem com vírgula:

O Pandas, porém, normalmente espera ponto para reconhecer um número decimal:

Por isso ele acabou tratando km como texto (object) em vez de número.

### Problema identificado:
 a coluna km, apesar de representar uma medida numérica, foi carregada como tipo object, pois apresenta valores decimais utilizando vírgula como separador decimal. Essa característica deverá ser tratada na etapa de limpeza.


```python
df["km"].unique()[:30]
```




    array(['225', '546,2', '88,2', '74', '471', '669', '376', '207,4',
           '708,5', '7,4', '327', '530', '51,8', '117,8', '125', '105,6',
           '1102', '137,7', '627,5', '826', '16', '104', '73,2', '46,5',
           '331', '292', '124', '316', '257', '347,8'], dtype=object)




```python
df["km"].value_counts().tail(20)
```




    km
    850,1    1
    578,6    1
    691,4    1
    642,6    1
    351,7    1
    566,2    1
    940,7    1
    248,9    1
    400,1    1
    796,8    1
    1107     1
    664,4    1
    475,4    1
    287,1    1
    1004     1
    487,6    1
    689,2    1
    353,1    1
    786,7    1
    996,4    1
    Name: count, dtype: int64




```python
df["km"].str.replace(",", ".", regex=False).astype(float).lt(0).sum()
```




    np.int64(0)



Por enquanto, nosso diagnóstico da coluna km fica:

km está armazenada como texto (object), embora represente uma variável numérica. A coluna utiliza vírgula como separador decimal em alguns registros, por exemplo 546,2 e 940,7. Será necessário avaliar a conversão para formato numérico na etapa de limpeza.


```python
(df[["pessoas", "mortos", "feridos_leves", "feridos_graves",
     "ilesos", "ignorados", "feridos", "veiculos"]] < 0).sum()
```




    pessoas           0
    mortos            0
    feridos_leves     0
    feridos_graves    0
    ilesos            0
    ignorados         0
    feridos           0
    veiculos          0
    dtype: int64




```python
(
    df["pessoas"]
    != df["mortos"]
    + df["feridos_leves"]
    + df["feridos_graves"]
    + df["ilesos"]
    + df["ignorados"]
).sum()
```




    np.int64(3823)



Esse resultado é bem importante para o diagnóstico. 🔎

pessoas ≠ mortos + feridos_leves + feridos_graves + ilesos + ignorados

Isso não significa automaticamente que os dados estão errados. Pode haver uma particularidade na forma como a PRF contabiliza essas categorias. Mas é uma inconsistência que merece ser investigada e registrada para a etapa de limpeza.


```python
df.loc[
    df["pessoas"] != (
        df["mortos"]
        + df["feridos_leves"]
        + df["feridos_graves"]
        + df["ilesos"]
        + df["ignorados"]
    ),
    ["id", "pessoas", "mortos", "feridos_leves", "feridos_graves", "ilesos", "ignorados", "feridos"]
].head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos_leves</th>
      <th>feridos_graves</th>
      <th>ilesos</th>
      <th>ignorados</th>
      <th>feridos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>652519</td>
      <td>6</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>652569</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>652760</td>
      <td>5</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>30</th>
      <td>652812</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>33</th>
      <td>652829</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>652830</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>652869</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>43</th>
      <td>652873</td>
      <td>8</td>
      <td>5</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>3</td>
      <td>0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>652885</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>48</th>
      <td>652918</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



Agora ficou bem mais claro. 👍 E tem uma descoberta importante aqui.

Veja o primeiro registro:

pessoas = 6
mortos = 1
feridos_leves = 1
feridos_graves = 0
ilesos = 1
ignorados = 4

Somando as categorias:

1 + 1 + 0 + 1 + 4 = 7 pessoas

Mas a coluna pessoas informa 6.

Isso acontece em vários dos exemplos. Portanto, realmente existem inconsistências entre essas colunas.

Porém, perceba uma coisa interessante: feridos também não deve simplesmente ser somado às demais categorias, porque ele é um total derivado dos feridos leves + feridos graves. Então não devemos incluí-lo nessa soma.

### Foram identificados 3.823 registros em que o total de pessoas não corresponde à soma das categorias de condição das pessoas (mortos, feridos leves, feridos graves, ilesos e ignorados). 
A inconsistência deverá ser avaliada na etapa de limpeza.


```python
df["diferenca_pessoas"] = (
    df["pessoas"]
    - (
        df["mortos"]
        + df["feridos_leves"]
        + df["feridos_graves"]
        + df["ilesos"]
        + df["ignorados"]
    )
)

df.loc[df["diferenca_pessoas"] != 0, "diferenca_pessoas"].value_counts().sort_index()
```




    diferenca_pessoas
    -80       1
    -14       1
    -13       1
    -11      11
    -10       5
    -9        1
    -8        9
    -7       11
    -6       14
    -5       61
    -4       85
    -3      206
    -2     1083
    -1     2334
    Name: count, dtype: int64



A diferença é sempre negativa, ou seja, nesses registros a soma das categorias de pessoas é maior que o valor informado em pessoas.

📊 O que isso nos mostra?

Dos 3.823 registros inconsistentes:

2.334 têm diferença de apenas 1 pessoa.
1.083 têm diferença de 2 pessoas.
Os demais apresentam diferenças maiores.
Existe 1 registro com diferença de -80, um caso extremamente discrepante.

Portanto, não parece ser simplesmente um pequeno erro isolado.

E tem uma pista interessante: lembra daquele acidente com 82 veículos e apenas 2 pessoas? Pois aqui aparece uma diferença de -80. 👀

Isso provavelmente está relacionado ao mesmo registro.

Essa coluna diferenca_pessoas foi criada apenas para investigação.


```python
df.loc[df["diferenca_pessoas"] == -80, [
    "id",
    "tipo_acidente",
    "pessoas",
    "mortos",
    "feridos_leves",
    "feridos_graves",
    "ilesos",
    "ignorados",
    "veiculos"
]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>tipo_acidente</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos_leves</th>
      <th>feridos_graves</th>
      <th>ilesos</th>
      <th>ignorados</th>
      <th>veiculos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>Incêndio</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>81</td>
      <td>82</td>
    </tr>
  </tbody>
</table>
</div>



🎯 Agora encontramos a origem daquela diferença de -80.

O registro é o mesmo que já tínhamos identificado como extremo:

| Campo            |    Valor |
| ---------------- | -------: |
| `id`             |   707992 |
| `tipo_acidente`  | Incêndio |
| `pessoas`        |        2 |
| `mortos`         |        0 |
| `feridos_leves`  |        0 |
| `feridos_graves` |        0 |
| `ilesos`         |        1 |
| `ignorados`      |       81 |
| `veiculos`       |       82 |

A soma das categorias de pessoas é:

0 + 0 + 0 + 1 + 81 = 82

Mas a coluna pessoas informa 2.

Então:

2 - 82 = -80

🔎 O que isso revela?

Temos um registro em que existe uma inconsistência muito grande entre pessoas e as categorias de pessoas.

E é interessante porque o mesmo acidente também possui o maior número de veículos da base, 82.

Isso sugere que pode haver alguma particularidade na forma como esse registro foi preenchido ou contabilizado. Não devemos concluir que é erro sem investigar a regra da fonte.


```python
(
    df.loc[df["diferenca_pessoas"] != 0, "ignorados"] > 0
).sum()
```




    np.int64(3823)



🎯 O resultado foi 3.823, exatamente o total de registros inconsistentes.

Isso revela uma pista muito forte:

Todos os 3.823 registros em que pessoas não corresponde à soma das categorias possuem pelo menos uma pessoa na categoria ignorados.

Isso pode explicar as diferenças, porque ignorados representa pessoas cuja situação não foi informada. Portanto, não podemos simplesmente considerar esses 3.823 registros como erros. Precisamos entender a regra da base antes de qualquer correção.

📌 Diagnóstico até aqui
Foram identificados 3.823 registros em que o número de pessoas não corresponde à soma de mortos, feridos_leves, feridos_graves, ilesos e ignorados. Em todos esses registros há pelo menos uma pessoa classificada como ignorados. O maior desvio encontrado foi de 80 pessoas, em um registro com 82 veículos. Dessa forma, os casos devem ser investigados antes de qualquer tratamento.


```python
df["ignorados"].describe()
```




    count    72529.000000
    mean         0.394739
    std          0.860584
    min          0.000000
    25%          0.000000
    50%          0.000000
    75%          1.000000
    max         81.000000
    Name: ignorados, dtype: float64



👍 Esse resultado fecha bem essa investigação.

👥 Coluna ignorados

Temos:

Mínimo: 0
Mediana: 0
75% dos acidentes: têm até 1 pessoa ignorada
Média: 0,39
Máximo: 81 pessoas ignoradas

E aquele registro extremo que encontramos tem justamente 81 ignorados.

📌 Conclusão do diagnóstico

Temos uma relação clara:

Os 3.823 registros em que pessoas não corresponde à soma das categorias apresentam pessoas classificadas como ignorados. A maior diferença ocorre no registro 707992, que possui 81 pessoas ignoradas, 82 veículos e apenas 2 pessoas registradas na coluna pessoas.

Isso é um achado de qualidade de dados, não necessariamente um erro. A pessoa 2 deverá investigar a regra de preenchimento da base antes de decidir qualquer tratamento.



```python
(df["feridos"] != df["feridos_leves"] + df["feridos_graves"]).sum()
```




    np.int64(0)



| Verificação                                | Resultado                 |
| ------------------------------------------ | ------------------------- |
| Valores negativos nas variáveis de vítimas | **Nenhum**                |
| `feridos` ≠ leves + graves                 | **Nenhum**                |
| `pessoas` ≠ soma das categorias            | **3.823 registros**       |
| Duplicidade de linhas                      | **Nenhuma**               |
| Duplicidade de `id`                        | **Nenhuma**               |
| `km` como variável numérica                | **Está como `object`**    |
| Valores negativos em `km`                  | **Nenhum**                |
| Valores ausentes                           | **63 registros no total** |



```python
(df["pessoas"] == 0).sum(), (df["veiculos"] == 0).sum()
```




    (np.int64(0), np.int64(0))



O resultado significa:
0 acidentes com pessoas = 0
0 acidentes com veiculos = 0

Isso é coerente com a natureza da base, porque cada registro representa uma ocorrência de acidente.

Até agora, não encontramos valores obviamente impossíveis nas contagens de pessoas e veículos. A principal inconsistência numérica encontrada continua sendo aquela dos 3.823 registros em que pessoas não bate com a soma das categorias.


```python
df["data_inversa"].min(), df["data_inversa"].max()
```




    ('2025-01-01', '2025-12-31')



O resultado mostra:

Data inicial: 01/01/2025
Data final: 31/12/2025

Então a base está corretamente limitada ao ano de 2025. Não encontramos, nesse teste, registros fora do período esperado.


```python
pd.to_datetime(df["data_inversa"], errors="coerce").isna().sum()
```




    np.int64(0)



No diagnóstico das datas, estamos tranquilos:

período: 01/01/2025 a 31/12/2025
datas inválidas: 0
registros fora de 2025: não identificados


```python
df["horario"].unique()[:30]
```




    array(['06:20:00', '07:50:00', '08:45:00', '11:00:00', '09:30:00',
           '10:40:00', '12:23:00', '17:45:00', '18:40:00', '17:00:00',
           '20:20:00', '20:00:00', '21:00:00', '05:45:00', '19:00:00',
           '07:20:00', '07:15:00', '06:42:00', '06:30:00', '08:20:00',
           '08:19:00', '11:57:00', '13:30:00', '13:20:00', '13:01:00',
           '14:30:00', '11:30:00', '06:50:00', '14:00:00', '19:20:00'],
          dtype=object)




```python
pd.to_datetime(df["horario"], format="%H:%M:%S", errors="coerce").isna().sum()
```




    np.int64(0)



Os primeiros 30 valores mostram um padrão consistente:

formato HH:MM:SS
exemplos: 06:20:00, 12:23:00, 21:00:00
não apareceu nenhum formato estranho nessa amostra.

Todos os 72.529 horários estão em um formato válido HH:MM:SS.

### A coluna horario apresenta formato consistente e não foram identificados horários inválidos.


```python
df["dia_semana"].value_counts()
```




    dia_semana
    sábado           11554
    domingo          11470
    sexta-feira      11197
    segunda-feira    10285
    quarta-feira      9556
    quinta-feira      9405
    terça-feira       9062
    Name: count, dtype: int64



A coluna dia_semana apresenta exatamente os 7 dias da semana, sem categorias estranhas ou variações de escrita.

Não encontramos valores ausentes nem categorias inconsistentes nessa coluna.

📌 Diagnóstico: dia_semana apresenta categorias consistentes e completas.


```python
df["fase_dia"].value_counts()
```




    fase_dia
    Pleno dia      40375
    Plena Noite    24781
    Anoitecer       3926
    Amanhecer       3447
    Name: count, dtype: int64



A coluna fase_dia também está bem consistente. Encontramos apenas quatro categorias.

Não apareceu categoria estranha, vazia ou com grafia aparentemente inconsistente.

📌 Diagnóstico: a variável fase_dia apresenta categorias bem definidas e não possui valores ausentes.


```python
df["condicao_metereologica"].value_counts()
```




    condicao_metereologica
    Céu Claro           46375
    Nublado             11435
    Chuva                6438
    Sol                  4201
    Garoa/Chuvisco       2422
    Ignorado             1000
    Nevoeiro/Neblina      553
    Vento                 104
    Neve                    1
    Name: count, dtype: int64



O ponto que chama atenção é Neve com apenas 1 ocorrência. ❄️

Isso não significa que seja um erro. É perfeitamente possível existir um registro de acidente com neve, então não devemos eliminar ou alterar simplesmente por ser raro.

Também temos Ignorado, que é uma categoria válida da própria base, mas representa informação não determinada.

📌 Diagnóstico: a variável condicao_metereologica possui 9 categorias. Não foram identificadas categorias claramente duplicadas ou inconsistentes, porém há categorias de baixa frequência, como Neve (1 registro) e Vento (104 registros), que deverão ser consideradas na análise. A categoria Ignorado possui 1.000 registros.


```python
df["tipo_pista"].value_counts()
```




    tipo_pista
    Simples     34733
    Dupla       30782
    Múltipla     7014
    Name: count, dtype: int64



A coluna tipo_pista também está bem consistente. Encontramos apenas três categorias.

Não aparecem valores ausentes nem categorias evidentemente inconsistentes.

📌 Diagnóstico: tipo_pista apresenta categorias padronizadas e sem valores ausentes.


```python
df["tracado_via"].value_counts()
```




    tracado_via
    Reta                                                        40298
    Curva                                                        8055
    Reta;Declive                                                 2059
    Reta;Aclive                                                  1963
    Interseção de Vias                                           1735
                                                                ...  
    Viaduto;Reta;Interseção de Vias;Rotatória                       1
    Declive;Rotatória;Interseção de Vias                            1
    Retorno Regulamentado;Rotatória;Reta;Interseção de Vias         1
    Em Obras;Retorno Regulamentado;Aclive;Interseção de Vias        1
    Viaduto;Interseção de Vias;Reta                                 1
    Name: count, Length: 605, dtype: int64



A coluna tracado_via tem 605 categorias diferentes. Isso é bastante, principalmente porque muitas categorias são combinações de características da via.

E existem combinações muito específicas que aparecem apenas 1 vez, como:

Viaduto;Interseção de Vias;Reta

📌 Diagnóstico: a variável tracado_via apresenta 605 categorias distintas, incluindo diversas combinações de características da via. Algumas categorias possuem frequência muito baixa, inclusive registros únicos. Não foram consideradas erros nesta etapa, mas a alta cardinalidade deverá ser avaliada durante a análise e limpeza.


```python
df["sentido_via"].value_counts()
```




    sentido_via
    Crescente        38715
    Decrescente      33647
    Não Informado      167
    Name: count, dtype: int64



A coluna sentido_via está bastante consistente.

Não há valores ausentes, e as categorias fazem sentido para a variável.

O único ponto a registrar é Não Informado, com 167 registros. Isso não é necessariamente erro, pois pode ser uma informação que não estava disponível no momento do registro.

📌 Diagnóstico: sentido_via possui três categorias, sem valores ausentes. A categoria Não Informado representa 167 registros e deverá ser considerada na etapa de análise/tratamento.


```python
df["uso_solo"].value_counts()
```




    uso_solo
    Não    41444
    Sim    31085
    Name: count, dtype: int64



A coluna uso_solo também está bem consistente.

Não apareceu nenhuma categoria inesperada, como SIM, sim, S, Não informado etc.

📌 Diagnóstico: a variável uso_solo possui duas categorias padronizadas (Sim e Não), sem valores ausentes ou inconsistências aparentes.


```python
df["classificacao_acidente"].value_counts(dropna=False)
```




    classificacao_acidente
    Com Vítimas Feridas    56181
    Sem Vítimas            11138
    Com Vítimas Fatais      5209
    NaN                        1
    Name: count, dtype: int64



A coluna classificacao_acidente tem 3 categorias válidas.

📌 Diagnóstico: categoria bem padronizada, com apenas 1 valor ausente. Esse registro apresenta informação suficiente para uma possível imputação, que deverá ser avaliada na etapa de limpeza.


```python
df["causa_acidente"].value_counts().tail(10)
```




    causa_acidente
    Deixar de acionar o farol da motocicleta (ou similar)    15
    Restrição de visibilidade em curvas verticais            13
    Faixas de trânsito com largura insuficiente              12
    Modificação proibida                                     11
    Transitar na calçada                                     11
    Faróis desregulados                                       9
    Redutor de velocidade em desacordo                        8
    Semáforo com defeito                                      8
    Sistema de drenagem ineficiente                           2
    Sinalização encoberta                                     2
    Name: count, dtype: int64



Aqui vemos as 10 causas menos frequentes.

Não aparece nenhuma categoria obviamente errada. As causas menos frequentes são apenas muito específicas.

📌 Diagnóstico: existem muitas categorias e algumas apresentam baixa frequência, mas não foram identificadas, até aqui, categorias claramente inválidas ou duplicadas.


```python
df.info(memory_usage="deep")
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 72529 entries, 0 to 72528
    Data columns (total 30 columns):
     #   Column                  Non-Null Count  Dtype 
    ---  ------                  --------------  ----- 
     0   id                      72529 non-null  int64 
     1   data_inversa            72529 non-null  object
     2   dia_semana              72529 non-null  object
     3   horario                 72529 non-null  object
     4   uf                      72529 non-null  object
     5   br                      72529 non-null  int64 
     6   km                      72529 non-null  object
     7   municipio               72529 non-null  object
     8   causa_acidente          72529 non-null  object
     9   tipo_acidente           72529 non-null  object
     10  classificacao_acidente  72528 non-null  object
     11  fase_dia                72529 non-null  object
     12  sentido_via             72529 non-null  object
     13  condicao_metereologica  72529 non-null  object
     14  tipo_pista              72529 non-null  object
     15  tracado_via             72529 non-null  object
     16  uso_solo                72529 non-null  object
     17  pessoas                 72529 non-null  int64 
     18  mortos                  72529 non-null  int64 
     19  feridos_leves           72529 non-null  int64 
     20  feridos_graves          72529 non-null  int64 
     21  ilesos                  72529 non-null  int64 
     22  ignorados               72529 non-null  int64 
     23  feridos                 72529 non-null  int64 
     24  veiculos                72529 non-null  int64 
     25  latitude                72529 non-null  object
     26  longitude               72529 non-null  object
     27  regional                72527 non-null  object
     28  delegacia               72507 non-null  object
     29  uop                     72491 non-null  object
    dtypes: int64(10), object(20)
    memory usage: 97.3 MB
    

A base ocupa:

97,3 MB de memória

E continua confirmando:

72.529 linhas
30 colunas
20 colunas object
10 colunas int64
1 coluna com ausência: classificacao_acidente
regional: 2 ausentes
delegacia: 22 ausentes
uop: 38 ausentes

Um detalhe interessante: agora o info() mostrou latitude e longitude como object, enquanto anteriormente tínhamos visto esses campos como numéricos. Isso indica que precisamos investigar essas duas colunas, porque coordenadas normalmente deveriam ser numéricas e pode haver algum detalhe de formatação.


```python
df["latitude"].unique()[:30]
```




    array(['-23,48586772', '-7,812288', '-23,182565', '-25,36517687',
           '-16,46801304', '-16,04148578', '-30,739714', '-27,60001226',
           '-21,16328873', '-8,47503105', '-28,42800492', '-20,17327539',
           '-6,846113', '-27,370375', '-25,59505309', '-7,2075',
           '-9,77791545', '-25,5216371', '-20,60236514', '-11,9396379',
           '-8,09414193', '-25,56472785', '-20,41066352', '-7,87406004',
           '-22,867662', '-3,67390203', '-3,54575653', '-29,65140716',
           '-22,58631777', '-30,367035'], dtype=object)




```python
df["longitude"].unique()[:30]
```




    array(['-46,54075317', '-39,08333306', '-50,637228', '-49,04223028',
           '-43,43121303', '-57,25884017', '-51,62594', '-48,6226467',
           '-42,37968988', '-41,0137105', '-48,88171497', '-44,37545454',
           '-38,366341', '-51,993419', '-49,31630659', '-35,5248',
           '-54,90365982', '-53,5943858', '-43,80699874', '-55,51573103',
           '-35,03716601', '-49,15859603', '-40,88380166', '-34,90588126',
           '-43,195337', '-40,88486128', '-44,57616778', '-54,53287505',
           '-44,01407302', '-53,606186'], dtype=object)




```python
pd.to_numeric(
    df["latitude"].str.replace(",", ".", regex=False),
    errors="coerce"
).isna().sum()
```




    np.int64(0)




```python
pd.to_numeric(
    df["latitude"].str.replace(",", ".", regex=False),
    errors="coerce"
).isna().sum()
```




    np.int64(0)




```python
df["latitude"].str.replace(",", ".", regex=False).astype(float).describe()
```




    count    72529.000000
    mean       -18.765845
    std          7.699352
    min        -33.689326
    25%        -25.001189
    50%        -20.363575
    75%        -12.641008
    max          4.461419
    Name: latitude, dtype: float64




```python
df["longitude"].str.replace(",", ".", regex=False).astype(float).describe()
```




    count    72529.000000
    mean       -46.379188
    std          6.190702
    min        -72.665988
    25%        -50.104374
    50%        -46.859333
    75%        -42.267399
    max        -34.827891
    Name: longitude, dtype: float64



📍 Latitude e longitude
As duas estão como object, embora representem números.
Os valores usam vírgula como separador decimal.
Exemplo: -23,48586772.
Portanto, será necessário converter o formato durante a etapa de limpeza.

As faixas observadas de latitude e longitude são plausíveis para a área geográfica abrangida pela base.


| Coluna                         | Situação                                                        |
| ------------------------------ | --------------------------------------------------------------- |
| `latitude`                     | `object`, embora contenha valores numéricos com vírgula decimal |
| `longitude`                    | `object`, embora contenha valores numéricos com vírgula decimal |
| Valores inválidos na longitude e latitude | **0**                                                           |



```python
(df["mortos"] > df["pessoas"]).sum()
```




    np.int64(0)



A coluna mortos não apresenta inconsistências em relação à coluna pessoas, pois nenhum acidente possui número de mortos superior ao número total de pessoas envolvidas.


```python
df["veiculos"].describe()
```




    count    72529.000000
    mean         1.998125
    std          1.126154
    min          1.000000
    25%          1.000000
    50%          2.000000
    75%          2.000000
    max         82.000000
    Name: veiculos, dtype: float64



Esse resultado merece investigação, mas ainda não podemos dizer que 82 veículos é um erro.

🚗 Coluna veiculos

Temos:

Mínimo: 1 veículo
Mediana: 2 veículos
Média: 1,99 veículo
75% dos acidentes: até 2 veículos
Máximo: 82 veículos

O 82 é bastante distante do padrão da base e, portanto, é um forte candidato a outlier.

⚠️ Mas atenção: outlier não significa automaticamente erro. Pode ser, por exemplo, um engavetamento envolvendo muitos veículos. Como o projeto exige que os outliers sejam analisados antes de qualquer decisão, vamos descobrir qual acidente tem esses 82 veículos e qual é o tipo de acidente.


```python
df.loc[df["veiculos"].idxmax(), [
    "id",
    "data_inversa",
    "uf",
    "municipio",
    "tipo_acidente",
    "classificacao_acidente",
    "pessoas",
    "veiculos",
    "mortos",
    "feridos"
]]
```




    id                                707992
    data_inversa                  2025-07-07
    uf                                    GO
    municipio                 PADRE BERNARDO
    tipo_acidente                   Incêndio
    classificacao_acidente       Sem Vítimas
    pessoas                                2
    veiculos                              82
    mortos                                 0
    feridos                                0
    Name: 10260, dtype: object




```python
(df["veiculos"] > 10).sum()
```




    np.int64(66)




```python
df.loc[df["veiculos"] > 10, "tipo_acidente"].value_counts()
```




    tipo_acidente
    Engavetamento                     19
    Colisão traseira                   9
    Incêndio                           7
    Colisão lateral sentido oposto     6
    Colisão lateral mesmo sentido      6
    Tombamento                         6
    Colisão frontal                    6
    Colisão com objeto                 3
    Saída de leito carroçável          2
    Colisão transversal                1
    Eventos atípicos                   1
    Name: count, dtype: int64




```python
df.loc[df["veiculos"].idxmax(), [
    "causa_acidente",
    "tracado_via",
    "condicao_metereologica",
    "fase_dia"
]]
```




    causa_acidente            Demais falhas mecânicas ou elétricas
    tracado_via                                               Reta
    condicao_metereologica                               Céu Claro
    fase_dia                                             Pleno dia
    Name: 10260, dtype: object



📌 Diagnóstico:
A variável veiculos apresenta valores extremos, com máximo de 82 veículos. Foram identificados 66 acidentes com mais de 10 veículos. Esses registros estão associados a diferentes tipos de acidentes, com destaque para engavetamentos (19 casos).

O ponto mais importante é a inconsistência aparente entre 82 veículos e apenas 2 pessoas. Pode ser um problema de registro, mas também pode haver uma explicação específica para a forma como a PRF contabilizou esse acidente.

O 82 é bastante distante do padrão da base e, portanto, é um forte candidato a outlier. Merece investigação específica na fase da limpeza.

Temos um bom diagnóstico aqui

Até agora encontramos:

km, latitude e longitude armazenados como texto apesar de representarem números.

Pouquíssimos valores ausentes.

Nenhuma duplicata.

Nenhum valor negativo nas variáveis de contagem.

Uma inconsistência entre pessoas e a soma das categorias de pessoas em 3.823 registros.

Um valor extremo de 82 veículos, entre outros 66 acidentes com mais de 10 veículos.

Uma classificação de acidente ausente em 1 registro, justamente em um acidente com 1 morto.


```python
df.loc[
    df["tipo_acidente"] == "Incêndio",
    "veiculos"
].describe()
```




    count    1771.000000
    mean        1.604743
    std         2.206221
    min         1.000000
    25%         1.000000
    50%         1.000000
    75%         2.000000
    max        82.000000
    Name: veiculos, dtype: float64



| Estatística |  Valor | O que significa                                   |
| ----------- | -----: | ------------------------------------------------- |
| `count`     |  1.771 | Há 1.771 registros considerados nessa análise     |
| `mean`      |   1,60 | Média de aproximadamente 1,6 veículo por acidente |
| `std`       |   2,21 | Existe bastante dispersão                         |
| `min`       |      1 | O mínimo é 1 veículo                              |
| `25%`       |      1 | 25% dos acidentes têm 1 veículo                   |
| `50%`       |      1 | A mediana é 1 veículo                             |
| `75%`       |      2 | 75% têm até 2 veículos                            |
| `max`       | **82** | Um registro apresenta **82 veículos**             |

🚨 O ponto importante

O 82 chama bastante atenção.

Veja a diferença:

Mediana: 1
75% dos registros: até 2
Média: 1,60
Máximo: 82

Isso sugere que 82 pode ser um possível outlier.


```python
df[df['veiculos'] == 82][
    ['id', 'data_inversa', 'uf', 'municipio', 'veiculos', 'pessoas', 'mortos',
     'feridos', 'latitude', 'longitude']
]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>data_inversa</th>
      <th>uf</th>
      <th>municipio</th>
      <th>veiculos</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos</th>
      <th>latitude</th>
      <th>longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>2025-07-07</td>
      <td>GO</td>
      <td>PADRE BERNARDO</td>
      <td>82</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>-15,17278873</td>
      <td>-48,39040339</td>
    </tr>
  </tbody>
</table>
</div>



O registro 707992 realmente apresenta uma inconsistência forte:

Veículos: 82
Pessoas: 2
Mortos: 0
Feridos: 0
Município: Padre Bernardo/GO
Data: 07/07/2025
🚨 Por que esse registro merece ser sinalizado?

Se um acidente possui 82 veículos, seria esperado encontrar um número de pessoas muito maior que 2, porque a própria coluna pessoas representa as pessoas envolvidas no acidente.

Ter 82 veículos e somente 2 pessoas é extremamente improvável e indica uma possível inconsistência no dado.

Além disso, a mediana de veiculos é 1 e 75% dos registros possuem no máximo 2 veículos. Portanto, o valor 82 está muito distante do comportamento normal da coluna.


```python
Q1 = df['veiculos'].quantile(0.25)
Q3 = df['veiculos'].quantile(0.75)

IQR = Q3 - Q1

limite_inferior = Q1 - 1.5 * IQR
limite_superior = Q3 + 1.5 * IQR

Q1, Q3, IQR, limite_inferior, limite_superior
```




    (np.float64(1.0),
     np.float64(2.0),
     np.float64(1.0),
     np.float64(-0.5),
     np.float64(3.5))



Encontramos:

Q1 = 1
Q3 = 2
IQR = 1
Limite inferior = -0,5
Limite superior = 3,5
Então, qual é a conclusão?

Pelo método IQR, todo registro com:

veiculos > 3,5, ou seja, 4 veículos ou mais, é considerado um outlier estatístico.

Agora precisamos saber quantos registros são outliers e, principalmente, em qual grupo de comparação eles devem ser avaliados.


```python
outliers_veiculos = df[df['veiculos'] > limite_superior]

len(outliers_veiculos)

```




    5219



Se fizermos IQR na base inteira, estamos comparando acidentes muito diferentes entre si. Precisamos fazer a análise por grupo.

Primeiro, vamos descobrir quais grupos fazem sentido

Para veiculos, eu sugiro inicialmente verificar a distribuição por tipo_acidente ou classificacao_acidente. Mas não quero escolher arbitrariamente, porque isso precisa estar de acordo com a lógica do projeto.

Análise inicial: aplicação do método IQR global identificou 5.219 possíveis outliers na variável veiculos. Entretanto, essa identificação não é suficiente para determinar anomalias, pois os acidentes devem ser comparados dentro de grupos homogêneos. Será realizada análise estratificada pelo grupo de comparação definido para a variável.


```python
df['tipo_acidente'].value_counts()
```




    tipo_acidente
    Colisão traseira                  14360
    Saída de leito carroçável         10209
    Colisão transversal                9306
    Colisão lateral mesmo sentido      7885
    Tombamento                         6351
    Colisão com objeto                 5109
    Colisão frontal                    4739
    Queda de ocupante de veículo       3450
    Atropelamento de Pedestre          3057
    Colisão lateral sentido oposto     2152
    Incêndio                           1771
    Capotamento                        1373
    Engavetamento                      1233
    Atropelamento de Animal            1133
    Eventos atípicos                    287
    Derramamento de carga               107
    Sinistro pessoal de trânsito          7
    Name: count, dtype: int64




```python
df['classificacao_acidente'].value_counts()
```




    classificacao_acidente
    Com Vítimas Feridas    56181
    Sem Vítimas            11138
    Com Vítimas Fatais      5209
    Name: count, dtype: int64



O agrupamento mais coerente entre essas duas opções é tipo_acidente, porque estamos comparando acidentes do mesmo tipo. Por exemplo, uma colisão traseira pode naturalmente ter uma distribuição de veículos diferente de um atropelamento ou de um incêndio.

Então vamos fazer o IQR por tipo_acidente


```python
def identificar_outliers(grupo):
    Q1 = grupo['veiculos'].quantile(0.25)
    Q3 = grupo['veiculos'].quantile(0.75)
    IQR = Q3 - Q1

    limite_superior = Q3 + 1.5 * IQR

    return grupo[
        (grupo['veiculos'] < Q1 - 1.5 * IQR) |
        (grupo['veiculos'] > limite_superior)
    ]

outliers_por_tipo = (
    df.groupby('tipo_acidente', group_keys=False)
      .apply(identificar_outliers)
)
```

    C:\Users\amali\AppData\Local\Temp\ipykernel_14972\2878775826.py:15: FutureWarning: DataFrameGroupBy.apply operated on the grouping columns. This behavior is deprecated, and in a future version of pandas the grouping columns will be excluded from the operation. Either pass `include_groups=False` to exclude the groupings or explicitly select the grouping columns after groupby to silence this warning.
      .apply(identificar_outliers)
    


```python
len(outliers_por_tipo)
```




    7452




```python
outliers_por_tipo[outliers_por_tipo['id'] == 707992]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>data_inversa</th>
      <th>dia_semana</th>
      <th>horario</th>
      <th>uf</th>
      <th>br</th>
      <th>km</th>
      <th>municipio</th>
      <th>causa_acidente</th>
      <th>tipo_acidente</th>
      <th>...</th>
      <th>ilesos</th>
      <th>ignorados</th>
      <th>feridos</th>
      <th>veiculos</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>regional</th>
      <th>delegacia</th>
      <th>uop</th>
      <th>diferenca_pessoas</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>2025-07-07</td>
      <td>segunda-feira</td>
      <td>07:30:00</td>
      <td>GO</td>
      <td>80</td>
      <td>62,9</td>
      <td>PADRE BERNARDO</td>
      <td>Demais falhas mecânicas ou elétricas</td>
      <td>Incêndio</td>
      <td>...</td>
      <td>1</td>
      <td>81</td>
      <td>0</td>
      <td>82</td>
      <td>-15,17278873</td>
      <td>-48,39040339</td>
      <td>SPRF-DF</td>
      <td>DEL03-DF</td>
      <td>UOP02-DEL03-DF</td>
      <td>-80</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 31 columns</p>
</div>




```python
df[df['id'] == 707992][
    ['id', 'tipo_acidente', 'classificacao_acidente',
     'veiculos', 'pessoas', 'mortos', 'feridos']
]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>tipo_acidente</th>
      <th>classificacao_acidente</th>
      <th>veiculos</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>Incêndio</td>
      <td>Sem Vítimas</td>
      <td>82</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



O registro 707992 é:

tipo_acidente: Incêndio
classificacao_acidente: Sem Vítimas
veiculos: 82
pessoas: 2
mortos: 0
feridos: 0

E isso muda um pouco a nossa análise.

O grupo correto

Para avaliar veiculos, podemos comparar o registro com outros acidentes do mesmo tipo_acidente, neste caso:

Incêndio

Isso é mais adequado do que comparar os 82 veículos com todos os 72 mil acidentes, porque tipos de acidente diferentes podem ter comportamentos muito diferentes.

Agora precisamos calcular o IQR somente para tipo_acidente = 'Incêndio'.


```python
df_incendio = df[df['tipo_acidente'] == 'Incêndio']

Q1_incendio = df_incendio['veiculos'].quantile(0.25)
Q3_incendio = df_incendio['veiculos'].quantile(0.75)

IQR_incendio = Q3_incendio - Q1_incendio

limite_superior_incendio = Q3_incendio + 1.5 * IQR_incendio

Q1_incendio, Q3_incendio, IQR_incendio, limite_superior_incendio
```




    (np.float64(1.0), np.float64(2.0), np.float64(1.0), np.float64(3.5))




```python
outliers_incendio = df_incendio[
    df_incendio['veiculos'] > limite_superior_incendio
]

len(outliers_incendio)
```




    85




```python
outliers_incendio[
    ['id', 'data_inversa', 'uf', 'municipio',
     'veiculos', 'pessoas', 'mortos', 'feridos']
].sort_values('veiculos', ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>data_inversa</th>
      <th>uf</th>
      <th>municipio</th>
      <th>veiculos</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>2025-07-07</td>
      <td>GO</td>
      <td>PADRE BERNARDO</td>
      <td>82</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4945</th>
      <td>685337</td>
      <td>2025-04-12</td>
      <td>MT</td>
      <td>NOBRES</td>
      <td>15</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5926</th>
      <td>689744</td>
      <td>2025-05-08</td>
      <td>BA</td>
      <td>ITATIM</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6789</th>
      <td>693721</td>
      <td>2025-05-25</td>
      <td>BA</td>
      <td>PONTO NOVO</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9805</th>
      <td>706211</td>
      <td>2025-07-21</td>
      <td>PE</td>
      <td>CABROBO</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>16645</th>
      <td>734589</td>
      <td>2025-11-26</td>
      <td>MS</td>
      <td>BATAGUASSU</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17270</th>
      <td>737345</td>
      <td>2025-12-08</td>
      <td>MA</td>
      <td>IMPERATRIZ</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17902</th>
      <td>739860</td>
      <td>2025-12-18</td>
      <td>GO</td>
      <td>PIRENOPOLIS</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17935</th>
      <td>739980</td>
      <td>2025-12-19</td>
      <td>RS</td>
      <td>CACAPAVA DO SUL</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18385</th>
      <td>742510</td>
      <td>2025-12-30</td>
      <td>BA</td>
      <td>CORRENTINA</td>
      <td>4</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>85 rows × 8 columns</p>
</div>



🎯 Agora temos a confirmação estatística.

Para o grupo tipo_acidente = Incêndio:

Q1 = 1
Q3 = 2
IQR = 1
Limite superior = 3,5

Portanto:

Para acidentes classificados como Incêndio, valores de veiculos > 3,5, ou seja, 4 veículos ou mais, são considerados outliers pelo método IQR.

E o registro 707992 tem 82 veículos.

Conclusão do diagnóstico

O ID 707992 é, portanto, um outlier estatístico dentro do próprio grupo de comparação correto (tipo_acidente = Incêndio).

Isso é bem mais forte do que a nossa primeira análise global. Não estamos dizendo apenas que 82 é muito alto comparado à base inteira. Ele também é muito alto entre os próprios acidentes do tipo Incêndio.

⚠️ Uma distinção importante

Nós podemos afirmar:

ID 707992 é um outlier estatístico.

Mas ainda não podemos afirmar que o dado está errado.

Isso porque outlier ≠ erro de preenchimento.

No entanto, esse caso merece atenção especial porque temos:

82 veículos × 2 pessoas × 0 mortos × 0 feridos

Esse conjunto de informações é uma possível inconsistência, além de ser um outlier estatístico.

Outliers na variável veiculos: foi utilizado o método do Intervalo Interquartil (IQR), aplicado dentro do grupo de comparação tipo_acidente. Para os acidentes classificados como Incêndio, foram obtidos Q1 = 1, Q3 = 2 e IQR = 1, resultando em limite superior de 3,5 veículos. Assim, registros com 4 ou mais veículos foram classificados como outliers, totalizando 85 registros nesse grupo. Entre eles, destaca-se o registro de ID 707992, que apresenta 82 veículos, 2 pessoas, 0 mortos e 0 feridos. O registro deve ser investigado como possível inconsistência.


```python
def calcular_limites(grupo):
    Q1 = grupo['veiculos'].quantile(0.25)
    Q3 = grupo['veiculos'].quantile(0.75)
    IQR = Q3 - Q1

    limite_inferior = Q1 - 1.5 * IQR
    limite_superior = Q3 + 1.5 * IQR

    return pd.Series({
        'Q1': Q1,
        'Q3': Q3,
        'IQR': IQR,
        'limite_inferior': limite_inferior,
        'limite_superior': limite_superior
    })

limites_por_tipo = df.groupby('tipo_acidente').apply(calcular_limites)

limites_por_tipo
```

    C:\Users\amali\AppData\Local\Temp\ipykernel_14972\3095996379.py:17: FutureWarning: DataFrameGroupBy.apply operated on the grouping columns. This behavior is deprecated, and in a future version of pandas the grouping columns will be excluded from the operation. Either pass `include_groups=False` to exclude the groupings or explicitly select the grouping columns after groupby to silence this warning.
      limites_por_tipo = df.groupby('tipo_acidente').apply(calcular_limites)
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Q1</th>
      <th>Q3</th>
      <th>IQR</th>
      <th>limite_inferior</th>
      <th>limite_superior</th>
    </tr>
    <tr>
      <th>tipo_acidente</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Atropelamento de Animal</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Atropelamento de Pedestre</th>
      <td>2.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>2.00</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>Capotamento</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Colisão com objeto</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Colisão frontal</th>
      <td>2.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.50</td>
      <td>4.50</td>
    </tr>
    <tr>
      <th>Colisão lateral mesmo sentido</th>
      <td>2.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.50</td>
      <td>4.50</td>
    </tr>
    <tr>
      <th>Colisão lateral sentido oposto</th>
      <td>2.0</td>
      <td>4.0</td>
      <td>2.0</td>
      <td>-1.00</td>
      <td>7.00</td>
    </tr>
    <tr>
      <th>Colisão transversal</th>
      <td>2.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>2.00</td>
      <td>2.00</td>
    </tr>
    <tr>
      <th>Colisão traseira</th>
      <td>2.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.50</td>
      <td>4.50</td>
    </tr>
    <tr>
      <th>Derramamento de carga</th>
      <td>2.0</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.50</td>
      <td>4.50</td>
    </tr>
    <tr>
      <th>Engavetamento</th>
      <td>3.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>1.50</td>
      <td>5.50</td>
    </tr>
    <tr>
      <th>Eventos atípicos</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>-0.50</td>
      <td>3.50</td>
    </tr>
    <tr>
      <th>Incêndio</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>-0.50</td>
      <td>3.50</td>
    </tr>
    <tr>
      <th>Queda de ocupante de veículo</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Saída de leito carroçável</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.00</td>
      <td>1.00</td>
    </tr>
    <tr>
      <th>Sinistro pessoal de trânsito</th>
      <td>1.0</td>
      <td>1.5</td>
      <td>0.5</td>
      <td>0.25</td>
      <td>2.25</td>
    </tr>
    <tr>
      <th>Tombamento</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>-0.50</td>
      <td>3.50</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Identifica os outliers de veículos dentro de cada tipo de acidente

df_out = df.merge(
    limites_por_tipo[['limite_inferior', 'limite_superior']],
    left_on='tipo_acidente',
    right_index=True,
    how='left'
)

df_out['outlier_veiculos'] = (
    (df_out['veiculos'] < df_out['limite_inferior']) |
    (df_out['veiculos'] > df_out['limite_superior'])
)

df_out['outlier_veiculos'].value_counts()
```




    outlier_veiculos
    False    65077
    True      7452
    Name: count, dtype: int64




```python
df_out[df_out['outlier_veiculos']][
    ['id', 'tipo_acidente', 'veiculos',
     'pessoas', 'mortos', 'feridos']
].sort_values('veiculos', ascending=False).head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>tipo_acidente</th>
      <th>veiculos</th>
      <th>pessoas</th>
      <th>mortos</th>
      <th>feridos</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10260</th>
      <td>707992</td>
      <td>Incêndio</td>
      <td>82</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18547</th>
      <td>748976</td>
      <td>Colisão com objeto</td>
      <td>31</td>
      <td>33</td>
      <td>0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>9474</th>
      <td>704931</td>
      <td>Colisão frontal</td>
      <td>21</td>
      <td>11</td>
      <td>1</td>
      <td>8</td>
    </tr>
    <tr>
      <th>4193</th>
      <td>670979</td>
      <td>Colisão lateral mesmo sentido</td>
      <td>18</td>
      <td>8</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>62630</th>
      <td>729013</td>
      <td>Colisão traseira</td>
      <td>18</td>
      <td>19</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11831</th>
      <td>714599</td>
      <td>Engavetamento</td>
      <td>17</td>
      <td>15</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5492</th>
      <td>687933</td>
      <td>Engavetamento</td>
      <td>16</td>
      <td>11</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>7738</th>
      <td>697678</td>
      <td>Engavetamento</td>
      <td>15</td>
      <td>17</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4945</th>
      <td>685337</td>
      <td>Incêndio</td>
      <td>15</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7469</th>
      <td>696512</td>
      <td>Colisão transversal</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12728</th>
      <td>718500</td>
      <td>Saída de leito carroçável</td>
      <td>14</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9585</th>
      <td>705324</td>
      <td>Colisão frontal</td>
      <td>14</td>
      <td>4</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>14509</th>
      <td>725849</td>
      <td>Colisão traseira</td>
      <td>14</td>
      <td>4</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>13923</th>
      <td>723498</td>
      <td>Engavetamento</td>
      <td>14</td>
      <td>11</td>
      <td>1</td>
      <td>6</td>
    </tr>
    <tr>
      <th>12455</th>
      <td>717295</td>
      <td>Engavetamento</td>
      <td>13</td>
      <td>9</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>10158</th>
      <td>707474</td>
      <td>Tombamento</td>
      <td>13</td>
      <td>7</td>
      <td>1</td>
      <td>3</td>
    </tr>
    <tr>
      <th>9043</th>
      <td>703139</td>
      <td>Incêndio</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>11344</th>
      <td>712651</td>
      <td>Engavetamento</td>
      <td>13</td>
      <td>7</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9805</th>
      <td>706211</td>
      <td>Incêndio</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>10560</th>
      <td>709276</td>
      <td>Incêndio</td>
      <td>13</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df['tipo_acidente'] == 'Incêndio']['veiculos'].value_counts().sort_index()
```




    veiculos
    1     1162
    2      378
    3      146
    4       74
    5        1
    6        1
    7        1
    10       1
    13       5
    15       1
    82       1
    Name: count, dtype: int64




```python
df[df['tipo_acidente'] == 'Incêndio']['veiculos'].describe()
```




    count    1771.000000
    mean        1.604743
    std         2.206221
    min         1.000000
    25%         1.000000
    50%         1.000000
    75%         2.000000
    max        82.000000
    Name: veiculos, dtype: float64



O registro 707992 foi identificado como um outlier estatístico na variável veiculos, considerando o grupo tipo_acidente = Incêndio. O valor de 82 veículos apresenta forte discrepância em relação aos demais registros desse grupo e também em relação ao número de pessoas envolvidas. O registro deve ser investigado antes de qualquer tratamento ou exclusão.

### Diagnóstico de outliers na variável `veiculos`

Para identificar valores atípicos na quantidade de veículos envolvidos, foi utilizado o método do Intervalo Interquartil (IQR), considerando `tipo_acidente` como grupo de comparação. Essa abordagem evita comparar diretamente acidentes de naturezas diferentes.

No grupo `Incêndio`, foram encontrados Q1 = 1 e Q3 = 2, resultando em IQR = 1 e limite superior de 3,5 veículos. Dessa forma, registros com mais de 3,5 veículos foram classificados como outliers nesse grupo.

A distribuição dos 1.771 registros de incêndio mostra forte concentração entre 1 e 4 veículos. O maior valor encontrado é 82 veículos, registrado no acidente de ID 707992. Esse registro apresenta uma discrepância particularmente elevada, pois informa 82 veículos, mas apenas 2 pessoas envolvidas, sem mortos ou feridos.

O registro 707992 foi, portanto, identificado como um outlier estatístico e um caso prioritário para investigação da qualidade dos dados. Neste momento, o registro não foi excluído ou alterado, pois a identificação de um outlier não implica necessariamente erro no dado.


Método utilizado: IQR

Grupo de comparação: tipo_acidente

Quantidade encontrada: 7.452 registros

Percentual: aproximadamente 10,3%

Caso extremo destacado: ID 707992

Justificativa para investigação: 82 veículos × apenas 2 pessoas
