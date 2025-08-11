`# Case Técnico de Arquitetura de Dados - iFood`

`## Descrição do Projeto`

Este projeto implementa um pipeline de dados para ingerir, processar e analisar os dados de corridas de táxis amarelos de Nova York de Janeiro a Maio de 2023. A solução utiliza PySpark no ambiente Databricks Community Edition para transformar os dados brutos e disponibilizá-los em uma tabela Delta para consumo e análise via SQL.`

`## Justificativas das Escolhas Técnicas`

- **Pipeline de Leitura:** Optei por ler cada arquivo Parquet individualmente dentro de um loop. Essa abordagem foi necessária para contornar os erros de `CANNOT_MERGE_SCHEMAS` causados por inconsistências nos tipos de dados e nos nomes das colunas (maiúsculas/minúsculas) entre os diferentes arquivos mensais. Isso tornou o pipeline resiliente às variações nos dados de origem.

- **Armazenamento (Unity Catalog Volumes):** Utilizei Volumes do Unity Catalog tanto para a landing zone quanto para a camada de consumo. Esta decisão foi tomada devido às restrições de segurança do ambiente Databricks Serverless, que desabilita o acesso ao DBFS root (`/FileStore`), impedindo o salvamento de dados em locais legados.

- **Criação da Tabela (Comando .saveAsTable()):** A tabela final foi criada utilizando o comando `.saveAsTable()` do PySpark em vez do SQL `CREATE TABLE ... LOCATION`. O método `.saveAsTable()` cria uma tabela gerenciada, deixando o Databricks controlar a localização física, o que é a abordagem recomendada e compatível com as políticas de segurança do Unity Catalog que estavam bloqueando o comando SQL.

`## Instruções de Execução`

Para executar esta solução, siga os passos abaixo:`

1. **Pré-requisitos:** Ter uma conta no Databricks Community Edition.` `

2. **Dados de Origem:** Baixar os dados "Yellow Taxi Trip Records" de Janeiro a Maio de 2023 no formato Parquet do [site da NYC TLC](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page).` `

3. **Configuração do Ambiente:**` * `No Databricks, crie um Volume chamado \`ifood_landing_zone` em `/Volumes/workspace/default/`.`*`Faça o upload dos 5 arquivos Parquet para este volume. 

4. **Execução do Pipeline de Ingestão:**`*`Importe e execute o notebook `src/ingestao_e_transformacao.ipynb`. Ao final, a tabela `ifood_challenge.yellow_taxi_trips` estará criada e pronta para uso. 

5. **Execução das Análises:** `*` Importe e execute o notebook `analysis/analises.ipynb` para ver as respostas para as perguntas do desafio.`
