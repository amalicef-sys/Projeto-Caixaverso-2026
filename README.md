# Dados

**A base utilizada neste projeto é a base de acidentes de trânsito nas rodovias federais brasileiras, disponibilizada pela Polícia Rodoviária Federal (PRF).**

## Base utilizada

- Ano: 2025
- Tipo: Acidentes agrupados por ocorrência
- Formato: CSV
- Fonte: Dados Abertos da Polícia Rodoviária Federal

A base original não será armazenada neste repositório. Para reproduzir as análises, é necessário realizar o download da base diretamente na fonte oficial da PRF.

Fonte oficial:
https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf

Download direto da base 2025: https://drive.google.com/file/d/1-G3MdmHBt6CprDwcW99xxC4BZ2DU5ryR/view?usp=sharing/download
- Salve o arquivo descompactado como `datatrans2025.csv` dentro da pasta `dados/. 

**Arquivo utilizado na análise:**
`datatran2025.csv`

---

## Perguntas de análise

A partir da base de acidentes de trânsito nas rodovias federais brasileiras em 2025, definimos as seguintes perguntas que irão orientar nossa análise exploratória:

1. **Quais estados concentram o maior número de acidentes nas rodovias federais em 2025?**
2. **Quais são os tipos de acidentes mais frequentes nas rodovias federais brasileiras?**
3. **Quais tipos de acidentes apresentam maior gravidade, considerando o número de mortos e feridos?**
4. **Quais condições meteorológicas estão associadas à maior ocorrência de acidentes?**
5. **Em quais meses e períodos do dia ocorre a maior concentração de acidentes?**

*Essas perguntas foram definidas antes do início da análise dos dados e servirão como base para as etapas de diagnóstico, tratamento, transformação e análise exploratória da base.*

---

## Diagnóstico

**Dimensões:** a base possui 72.529 registros e 30 variáveis.

**Tipos de dados:** foram identificadas inicialmente 10 variáveis do tipo int64, 20 do tipo object. Os tipos numéricos estão adequados para as variáveis de contagem e localização. As variáveis textuais estão armazenadas como object, incluindo campos que representam datas, horários e categorias, os quais serão avaliados posteriormente durante a etapa de limpeza.

**Uso da memória:** a base ocupa aproximadamente 92,7 MB quando carregada com a configuração original (sep=";", encoding="latin1" e low_memory=False). As variáveis km, latitude e longitude são inicialmente interpretadas como object, devido ao formato dos valores decimais, contribuindo para um maior consumo de memória.

**Faltantes por coluna:** a análise inicial da qualidade dos dados identificou valores ausentes em apenas quatro variáveis: classificacao_acidente (1), regional (2), delegacia (22) e uop (38). A maior proporção de dados ausentes corresponde a 0,0524% na variável uop, indicando um baixo nível de ausência na base. As demais 26 variáveis não apresentam valores ausentes.

**Duplicados:** a identificação dos acidentes por meio do id apresenta unicidade, não sendo observada duplicação evidente dos registros.

**Categorias inconsistentes:** foram encontradas 3.823 ocorrências em que o total de pessoas não corresponde à soma das categorias de situação das vítimas, o que será investigado. Já a variável feridos apresenta coerência com a soma de feridos_leves e feridos_graves. Nenhum valor negativo nas variáveis de contagem.

**Outliers na variável veiculos:** foi utilizado o método do Intervalo Interquartil (IQR), aplicado dentro do grupo de comparação tipo_acidente. Para os acidentes classificados como Incêndio, foram obtidos Q1 = 1, Q3 = 2 e IQR = 1, resultando em limite superior de 3,5 veículos. Assim, registros com 4 ou mais veículos foram classificados como outliers, totalizando 85 registros nesse grupo. Entre eles, destaca-se o registro de ID 707992, que apresenta 82 veículos, 2 pessoas, 0 mortos e 0 feridos. O registro deve ser investigado como possível inconsistência.

---

## Limpeza e tratamento

**Tratamento e tipagem:** substituição de vírgula por ponto nas colunas km, latitude e longitude para conversão de string para float64, e  data_inversa de string para datetime64.

**Tratamento de dados faltantes:** imputação via .loc na coluna classificacao_acidente do registro ID 652519 como "Com Vítimas Fatais", seguindo a regra oficial do manual da PRF (mortos = 1).

**Auditoria de integridade e outliers:** comprovação via código de que 100% das divergências da coluna pessoas eram ocupantes ignorados pela PRF. Investigação do outlier atípico do ID 707992 (82 veículos em incêndio real), mantendo 100% dos 72.529 registros sem nenhuma exclusão.

**Engenharia de features e transformações:** criação das variáveis faixa_horaria, final_de_semana, mes, indice_gravidade, adição da coluna regiao_brasil via .map() com dicionário e nivel_gravidade_rotulo via np.select().

**Dummies e exportação:** uso de get_dummies com drop_first=True para modelos matemáticos e gravação da base tratada em formato Apache Parquet (acidentes_tratados.parquet).

**Relatório das etapas de tratamento**

---

## Conclusões
### Resumo
1. *Minas Gerais é o estado que concentrou mais acidentes(9.570) em 2025, seguido por Santa Catarina (8.186) e Paraná (7.630). Juntos, concentraram 35% dos acidentes.*
2. *Os tipos de acidentes mais frequentes são colisão traseira (14.360), saída de leito carroçável (10.209) e colisão transversal (9.306) foram os mais frequentes, somando 46,7% dos registros.*
3. *O tipo de acidente que apresentou maior gravidade foi colisão frontal, com 1.863 mortes, tanto em termos relativos quanto absolutos.*
4. *Céu claro concentrou a maioria dos acidentes (63,9%), seguido por tempo nublado e chuva. Isso não significa maior risco, pois não considera a exposição dos veículos.*
5. *Dezembro foi o mês que registrou o maior número de acidentes(6.788), enquanto fevereiro teve o menor (5.287), uma diferença de 28,4%.*

### Principais achados
1.  A maior parte dos acidentes registrados ocorreu em condições e situações que envolvem o comportamento dos condutores, com destaque para causas como ausência de reação do condutor e reação tardia ou ineficiente. Esse resultado reforça a importância de ações preventivas voltadas à atenção e ao comportamento na condução.
2.  Os acidentes apresentam concentração diferente entre os estados, com destaque para Minas Gerais, Santa Catarina e Paraná em número de registros. Essa distribuição pode auxiliar no direcionamento de ações de fiscalização, prevenção e educação no trânsito.
3.  A ocorrência de acidentes varia ao longo do ano e entre os dias da semana, indicando que períodos específicos podem concentrar maior quantidade de registros. Essa informação pode contribuir para o planejamento de ações preventivas e de fiscalização em períodos de maior ocorrência.
4.  A gravidade dos acidentes é heterogênea, havendo registros sem vítimas, com vítimas feridas e com vítimas fatais. Portanto, a quantidade de acidentes, isoladamente, não representa o impacto desses eventos sobre as pessoas.

## Limitações da análise
Os dados permitem identificar padrões e características dos acidentes registrados nas rodovias federais em 2025, mas não permitem afirmar, isoladamente, que determinado fator causou os acidentes. Também não é possível concluir que um estado ou período é necessariamente mais perigoso apenas pela quantidade de registros, pois seria necessário considerar fatores como fluxo de veículos, extensão da malha rodoviária e exposição ao risco.

## Próximos passos
Com mais tempo e dados complementares, seria interessante cruzar os acidentes com dados de fluxo de veículos, características das rodovias(pista simples ou dupla) e condições meteorológicas externas, permitindo uma avaliação mais completa dos fatores associados aos acidentes.