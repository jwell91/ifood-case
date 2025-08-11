# Case Técnico de Arquitetura de Dados - iFood

## Descrição do Projeto

Este projeto implementa um pipeline de dados ponta a ponta para o desafio técnico de Arquitetura de Dados do iFood. A solução realiza a ingestão, o processamento, a validação e a disponibilização para análise dos dados de corridas de táxis amarelos de Nova York (Janeiro a Maio de 2023).

O pipeline foi desenvolvido inteiramente em PySpark no ambiente Databricks, utilizando notebooks para garantir clareza e reprodutibilidade. O resultado final é uma tabela Delta limpa, otimizada e confiável, pronta para consumo analítico.

## Arquitetura da Solução

O pipeline foi estruturado em dois notebooks distintos para separar as responsabilidades de engenharia e análise:

1. **Engenharia de Dados (`src/ingestao_e_transformacao.ipynb`):** Este notebook contém toda a lógica de ETL (Extração, Transformação e Carga). Ele é responsável por ler os dados brutos da landing zone, aplicar as transformações e regras de qualidade, e carregar o resultado na tabela Delta final.

2. **Análise de Dados (`analysis/analises_aprimorado.ipynb`):** Este notebook consome a tabela gerada pelo pipeline de engenharia. Ele inicia com uma Análise Exploratória de Dados (EDA) para entender a distribuição e a qualidade dos dados e, em seguida, responde às perguntas de negócio com o suporte de visualizações gráficas.

## Justificativas das Escolhas Técnicas

- **Abordagem baseada em Notebooks:** Para este desafio técnico, a utilização de notebooks (`.ipynb`) para o ETL é uma escolha eficaz. Permite que cada etapa do processo (leitura, limpeza, salvamento) seja executada e documentada em células separadas, tornando o fluxo de dados extremamente claro e fácil de depurar.

- **Análise Exploratória e Visualização:** A adição de uma seção de EDA e gráficos (histogramas, gráficos de barras) no notebook de análise é crucial. Isso não apenas valida a qualidade dos dados, mas também enriquece a comunicação dos resultados, tornando os insights mais acessíveis para um público não técnico.

- **Gerenciamento de Dependências (`requirements.txt`):** O arquivo de dependências garante que o ambiente de análise seja totalmente reprodutível, listando todas as bibliotecas Python necessárias para a execução dos notebooks.

## Estrutura do Repositório

```
ifood-case/
├── src/
│   └── ingestao_e_transformacao.ipynb
├── analysis/
│   └── analises_aprimorado.ipynb
├── README.md
└── requirements.txt
```

## Instruções para Execução

Para replicar esta solução, siga os passos abaixo:

1. **Pré-requisitos:**
   
   - Uma conta ativa no Databricks Community Edition.
   
   - Cluster configurado com Databricks Runtime que suporte as bibliotecas do `requirements.txt`.

2. **Dados de Origem:**
   
   - Baixar os dados **"Yellow Taxi Trip Records"** de Janeiro a Maio de 2023 no formato **Parquet** do site oficial [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page "null").

3. **Configuração do Ambiente Databricks:**
   
   - No **Catalog Explorer**, dentro do catálogo `workspace` e do schema `default`, crie um **Managed Volume** com o nome `ifood_landing_zone`.
   
   - Faça o upload dos 5 arquivos `.parquet` baixados para dentro deste volume.
   
   - Importe os notebooks `src/ingestao_e_transformacao.ipynb` e `analysis/analises_aprimorado.ipynb` para seu workspace.

4. **Execução do Pipeline de Ingestão:**
   
   - Abra o notebook `src/ingestao_e_transformacao.ipynb`.
   
   - Anexe-o a um cluster e execute todas as células. Isso irá criar a tabela `ifood_challenge.yellow_taxi_trips`.

5. **Execução das Análises:**
   
   - Abra o notebook `analysis/analises_aprimorado.ipynb`.
   
   - Anexe o notebook a um cluster que tenha as bibliotecas `matplotlib` e `seaborn` instaladas.
   
   - Execute as células para visualizar a EDA e as respostas para as perguntas do desafio.

## Análises Realizadas

O notebook `analysis/analises_aprimorado.ipynb` contém:

1. **Análise Exploratória de Dados (EDA):**
   
   - Verificação de schema e tipos de dados.
   
   - Estatísticas descritivas das colunas numéricas.
   
   - Visualização da distribuição do `total_amount` e `passenger_count`.

2. **Respostas às Perguntas de Negócio (com visualização):**
   
   - Qual a média de valor total (`total_amount`) recebido por mês?
   
   - Qual a média de passageiros (`passenger_count`) por hora do dia no mês de Maio?
