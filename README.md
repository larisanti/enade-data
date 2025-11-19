# Análise de Desempenho dos Cursos de Computação (ENADE 2021 e 2023)

Este projeto de Análise de Dados e ETL utiliza os microdados oficiais do Exame Nacional de Desempenho dos Estudantes (ENADE) dos anos de 2021 e 2023 para avaliar e visualizar o desempenho geral dos cursos de graduação da área de Computação no Brasil. 
O objetivo foi processar dados brutos e massivos para gerar insights estratégicos sobre a qualidade e as tendências da educação superior em Tecnologia no país. 

*Este trabalho é parte das pesquisas realizadas no Projeto de Pesquisa intitulado "Explorando Horizontes: Tecnologias Computacionais Aplicadas à Comunidade através dos Cursos do Núcleo de Computação da Politécnica Uninter (N-CPU)".*

---

### Metodologia

1. Extração e Consolidação: Carregamento dos arquivos de microdados brutos do ENADE (Arquivos 1 e 3 de 2021 e 2023). A coluna NT_GER (Nota Geral) foi consolidada com as informações de curso e instituição.
2. Filtragem e Limpeza: Aplicação de filtros rigorosos para isolar apenas os cursos de Computação e Tecnologia e instituições de ensino relevantes, além de mapear as categorias administrativas (CO_CATEGAD) para simplificar a classificação em Pública e Privada.
3. Análise: Geração de um dataframe final limpo (df_final.csv) contendo os dados consolidados de desempenho dos anos 2021 e 2023, pronto para o cálculo de médias e a identificação de padrões.
4. Comunicação: Os resultados da análise foram comunicados por meio de elaboração de um resumo acadêmico, criação de visualizações no Power BI e apresentação da pesquisa no evento acadêmico XVIII ENFOC.
