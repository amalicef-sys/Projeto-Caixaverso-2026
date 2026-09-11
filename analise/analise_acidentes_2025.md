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


