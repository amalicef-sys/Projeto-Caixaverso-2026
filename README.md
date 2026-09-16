\# Dados



A base utilizada neste projeto é a base de acidentes de trânsito nas rodovias federais brasileiras, disponibilizada pela Polícia Rodoviária Federal (PRF).



\## Base utilizada



\- Ano: 2025

\- Tipo: Acidentes agrupados por ocorrência

\- Formato: CSV

\- Fonte: Dados Abertos da Polícia Rodoviária Federal



A base original não será armazenada neste repositório. Para reproduzir as análises, é necessário realizar o download da base diretamente na fonte oficial da PRF.



Fonte oficial:

https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf

Download direto da base 2025: https://drive.google.com/file/d/1-G3MdmHBt6CprDwcW99xxC4BZ2DU5ryR/view?usp=sharing/download
- Salve o arquivo descompactado como `datatrans2025.csv` dentro da pasta `dados/`. 



Arquivo utilizado na análise:



`datatran2025.csv`



\## Perguntas de análise



A partir da base de acidentes de trânsito nas rodovias federais brasileiras em 2025, definimos as seguintes perguntas que irão orientar nossa análise exploratória:



1\. \*\*Quais estados concentram o maior número de acidentes nas rodovias federais em 2025?\*\*



2\. \*\*Quais são os tipos de acidentes mais frequentes nas rodovias federais brasileiras?\*\*



3\. \*\*Quais tipos de acidentes apresentam maior gravidade, considerando o número de mortos e feridos?\*\*



4\. \*\*Quais condições meteorológicas estão associadas à maior ocorrência de acidentes?\*\*



5\. \*\*Em quais meses e períodos do dia ocorre a maior concentração de acidentes?\*\*



Essas perguntas foram definidas antes do início da análise dos dados e servirão como base para as etapas de diagnóstico, tratamento, transformação e análise exploratória da base.


DIAGNÓSTICO:

Dimensões: a base possui 72.529 registros e 30 variáveis.

Tipos de dados: foram identificadas 10 variáveis do tipo int64, 3 do tipo float64 e 17 do tipo object. Os tipos numéricos estão adequados para as variáveis de contagem e localização. As variáveis textuais estão armazenadas como object, incluindo campos que representam datas, horários e categorias, os quais serão avaliados posteriormente durante a etapa de limpeza.

Uso da memória: a base ocupa aproximadamente 92,7 MB quando carregada com a configuração original (sep=";", encoding="latin1" e low_memory=False). As variáveis km, latitude e longitude são inicialmente interpretadas como object, devido ao formato dos valores decimais, contribuindo para um maior consumo de memória.

Faltantes por coluna: a análise inicial da qualidade dos dados identificou valores ausentes em apenas quatro variáveis: classificacao_acidente (1), regional (2), delegacia (22) e uop (38). A maior proporção de dados ausentes corresponde a 0,0524% na variável uop, indicando um baixo nível de ausência na base. As demais 26 variáveis não apresentam valores ausentes.

Duplicados: a identificação dos acidentes por meio do id apresenta unicidade, não sendo observada duplicação evidente dos registros.

Categorias inconsistentes: foram encontradas 3.823 ocorrências em que o total de pessoas não corresponde à soma das categorias de situação das vítimas, o que será investigado. Já a variável feridos apresenta coerência com a soma de feridos_leves e feridos_graves. Nenhum valor negativo nas variáveis de contagem.

Outliers na variável veiculos: foi utilizado o método do Intervalo Interquartil (IQR), aplicado dentro do grupo de comparação tipo_acidente. Para os acidentes classificados como Incêndio, foram obtidos Q1 = 1, Q3 = 2 e IQR = 1, resultando em limite superior de 3,5 veículos. Assim, registros com 4 ou mais veículos foram classificados como outliers, totalizando 85 registros nesse grupo. Entre eles, destaca-se o registro de ID 707992, que apresenta 82 veículos, 2 pessoas, 0 mortos e 0 feridos. O registro deve ser investigado como possível inconsistência.

