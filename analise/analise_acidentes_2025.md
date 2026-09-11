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
