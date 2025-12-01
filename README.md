# Pipeline ETL e Análise Exploratória de Dados: Microdados ENADE

Este projeto de Análise de Dados e ETL utiliza os microdados oficiais do Exame Nacional de Desempenho dos Estudantes (ENADE) das edições de 2021 e 2023 para investigar quantitativamente a correlação entre o sucesso acadêmico (medido pela Nota Geral) e diversos fatores socioeconômicos e institucionais dos estudantes da área de Computação no Brasil.


O trabalho é estruturado em duas fases principais:
- Fase 1 [(enade_data_v1.ipynb)](https://github.com/larisanti/enade-data/blob/main/c%C3%B3digo/enade_data_v1.ipynb): Foco na consolidação e filtragem de bases de duas edições da prova (2021 e 2023) para cursos de Computação, resultando em uma base unificada e um dashboard para análise exploratória inicial.

- Fase 2 [(enade_data_v2.ipynb)](https://github.com/larisanti/enade-data/blob/main/c%C3%B3digo/enade_data_v2.ipynb): Esta fase contém mais etapas de tratamento de dados e resulta em uma análise exploratória de extremos (1º vs 4º quartil de desempenho) para investigar disparidades relacionadas à renda familiar, motivação de escolha da IES e categoria administrativa.

Este trabalho é parte das pesquisas realizadas no Projeto de Pesquisa *"Explorando Horizontes: Tecnologias Computacionais Aplicadas à Comunidade através dos Cursos do Núcleo de Computação da Politécnica Uninter (N-CPU)."*


---


### Fase 1: Desempenho Consolidado (2021 e 2023)
O dashboard da primeira fase, que consolida os dados das edições de 2021 e 2023 do ENADE para diversos cursos da área de Computação, exibe uma análise por Categoria Administrativa. O resultado sugere uma disparidade de desempenho, com as Instituições Públicas alcançando uma média maior (39 pontos) em comparação com Instituições Privadas (32 pontos).

![screenshot](enade.jpg)

Antes da criação do dashboard em Power BI, foi feito tratamento de dados em Python, sendo a maior dificuldade a falta de uma chave única contornada pela ordenação determinística dos dataframes com base nas colunas 'NU_ANO' e 'CO_CURSO' antes de concatenar ou mesclar as informações de nota e questionário. Esta estratégia garantiu o alinhamento correto dos dados de desempenho (NT_GER) de diferentes arquivos.



```python
# Ordenar as linhas pelos valores das colunas 'NU_ANO' e 'CO_CURSO' e resetar o índice para organizar os dados
df_microdados2021_arq1_sorted = df_microdados2021_arq1.sort_values(by=['NU_ANO', 'CO_CURSO']).reset_index(drop=True)
df_microdados2021_arq3_sorted = df_microdados2021_arq3.sort_values(by=['NU_ANO', 'CO_CURSO']).reset_index(drop=True)

df_microdados2023_arq1_sorted = df_microdados2023_arq1.sort_values(by=['NU_ANO', 'CO_CURSO']).reset_index(drop=True)
df_microdados2023_arq3_sorted = df_microdados2023_arq3.sort_values(by=['NU_ANO', 'CO_CURSO']).reset_index(drop=True)

# Para 2021: Concatenar NT_GER ao df_microdados2021_arq1_sorted
df_microdados2021_arq1_sorted['NT_GER'] = df_microdados2021_arq3_sorted['NT_GER']
df_2021 = df_microdados2021_arq1_sorted # Renomeia para df_2021

# Para 2023: Concatenar NT_GER ao df_microdados2023_arq1_sorted
df_microdados2023_arq1_sorted['NT_GER'] = df_microdados2023_arq3_sorted['NT_GER']
df_2023 = df_microdados2023_arq1_sorted # Renomeia para df_2023
```

Veja o código completo no notebook: [(enade_data_v1.ipynb)](https://github.com/larisanti/enade-data/blob/main/c%C3%B3digo/enade_data_v1.ipynb).


---


### Fase 2

Esta versão aprofundou a análise, focando exclusivamente no curso de **Ciência da Computação (Bacharelado)** (`CO_GRUPO: 4004`) da edição de **2021** e introduzindo uma comparação de extremos. O foco foi investigar a relação entre o **desempenho extremo** (alunos do **1º Quarto** - pior desempenho - vs. **4º Quarto** - melhor desempenho) e fatores como **renda familiar** (`CO_RS_I8`), **motivação de escolha da IES** (`QE_I26`) e **categoria administrativa da IES** (`CO_CATEGAD`).

#### Resultados

| Fator de Disparidade | Indicador Analisado | 1º Quarto (Pior Desempenho) | 4º Quarto (Melhor Desempenho) | Conclusão |
| :--- | :--- | :--- | :--- | :--- |
| **Renda Familiar** | Proporção de alunos de Renda Alta ($\ge 6$ SM) | $\approx 5.4\%$ | $\approx 10.1\%$ | A proporção de alunos de **renda alta** é significativamente **maior** no grupo de **melhor desempenho**. |
| **Motivação de Escolha** | Proporção de alunos que priorizaram "Qualidade/Reputação da IES" | $\approx 30.3\%$ | $\approx 40.2\%$ | A priorização de **qualidade/reputação** está mais associada ao **sucesso acadêmico**. |
| **Categoria Administrativa** | Proporção de alunos em IES **Privadas** | $\approx 57.7\%$ | $\approx 42.3\%$ | IES **Privadas** apresentam maior concentração de alunos no **pior quartil** de desempenho. |

Assim como na primeira fase, o tratamento de dados foi realizado utilizando Python:

```python
## 1. Extração de dados de três bases diferentes
# arq1: informações da edição, curso e IES
df_arq1 = pd.read_csv('microdados2021_arq1.txt', sep=';', encoding='iso-8859-1', 
                      usecols=['NU_ANO', 'CO_GRUPO', 'CO_ORGACAD', 'CO_CATEGAD', 'CO_CURSO'],
                      low_memory=False)
# arq3: nota e renda familiar
df_arq3 = pd.read_csv('microdados2021_arq3.txt', sep=';', encoding='iso-8859-1', 
                      usecols=['NU_ANO', 'CO_CURSO', 'NT_GER', 'CO_RS_I8'],
                      dtype={'CO_RS_I8': str, 'NT_GER': str},
                      low_memory=False)
# arq32: questionário (pergunta 26)
df_arq32 = pd.read_csv('microdados2021_arq32.txt', sep=';', encoding='iso-8859-1', 
                       usecols=['NU_ANO', 'CO_CURSO', 'QE_I26'],
                       low_memory=False)

# 2. Ordenação das linhas por curso
df_arq1 = df_arq1.sort_values(by=['CO_CURSO']).reset_index(drop=True)
df_arq3 = df_arq3.sort_values(by=['CO_CURSO']).reset_index(drop=True)
df_arq32 = df_arq32.sort_values(by=['CO_CURSO']).reset_index(drop=True)

# Verificação de alinhamento das linhas
if not (df_arq1['CO_CURSO'].equals(df_arq3['CO_CURSO']) and df_arq1['CO_CURSO'].equals(df_arq32['CO_CURSO'])):
    raise ValueError("ERRO: Não foi possível alinhar as linhas dos três arquivos com base em CO_CURSO.")
print("Linhas alinhadas com sucesso entre os três arquivos.")

# 3. Merge
df = df_arq1.copy()
df['NT_GER'] = df_arq3['NT_GER']
df['CO_RS_I8'] = df_arq3['CO_RS_I8']
df['QE_I26'] = df_arq32['QE_I26']

# 4. Filtro: Ciência da Computação
df = df[df['CO_GRUPO'] == 4004] 

# 5. Limpeza de dados nulos e conversão de tipos
df['NT_GER'] = pd.to_numeric(df['NT_GER'].astype(str).str.replace(',', '.'), errors='coerce')
df = df[df['CO_RS_I8'] != '*']
df = df.dropna(subset=['NT_GER', 'CO_RS_I8', 'QE_I26'])

# 6. Definição dos quartos
df['QUARTO'] = pd.qcut(df['NT_GER'], q=[0, 0.25, 0.5, 0.75, 1.0], labels=['1º Quarto (Pior)', '2º Quarto', '3º Quarto', '4º Quarto (Melhor)'])

## 7. Mapeamento de variáveis
df['TIPO_IES'] = df['CO_CATEGAD'].map({1: 'Pública', 2: 'Pública', 3: 'Pública', 4: 'Privada', 5: 'Privada', 6: 'Privada', 7: 'Privada'})
df['RENDA_ALTA'] = df['CO_RS_I8'].isin(['E', 'F', 'G'])
df['MOTIVACAO_QUALIDADE'] = df['QE_I26'] == 'F'

# 8. Filtro: 1o e 4o quartos
df_extremos = df[df['QUARTO'].isin(['1º Quarto (Pior)', '4º Quarto (Melhor)'])].copy()
df_extremos['QUARTO'] = df_extremos['QUARTO'].cat.remove_unused_categories()
```

Veja o código completo e a análise no notebook: [(enade_data_v2.ipynb)](https://github.com/larisanti/enade-data/blob/main/c%C3%B3digo/enade_data_v2.ipynb)

---

### Tecnologias

O projeto foi desenvolvido em **Python** e requer as seguintes bibliotecas:

* **pandas:** Para manipulação e tratamento dos dados.
* **matplotlib** e **seaborn:** Para a análise exploratória e visualização.
