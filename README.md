# Case Técnico de Arquitetura de Dados - iFood

## Descrição do Projeto

Este projeto implementa um pipeline de dados ponta a ponta para o desafio técnico de Arquitetura de Dados. A solução realiza a ingestão, o processamento e a disponibilização para análise dos dados de corridas de táxis amarelos de Nova York, referentes ao período de Janeiro a Maio de 2023.

O pipeline foi desenvolvido inteiramente em PySpark no ambiente Databricks Community Edition e segue as melhores práticas para lidar com dados inconsistentes e ambientes de nuvem modernos e seguros. O resultado final é uma tabela Delta limpa, otimizada e pronta para ser consumida por analistas de dados via SQL.

## Estrutura do Pipeline de Dados

O pipeline de ingestão, contido no notebook `src/ingestao_e_transformacao.ipynb`, segue uma abordagem resiliente em 5 etapas principais:

1. **Leitura Individual e Isolada:** Os arquivos Parquet são lidos um por um, dentro de um loop, para evitar falhas de leitura causadas por inconsistências de esquema entre os diferentes meses.

2. **Normalização Imediata:** Para cada arquivo lido, os nomes das colunas são imediatamente padronizados para letras minúsculas, resolvendo conflitos de maiúsculas/minúsculas (`airport_fee` vs. `Airport_fee`).

3. **Seleção e Tipagem:** Apenas as colunas necessárias para a análise são selecionadas e seus tipos de dados são convertidos para um padrão consistente (`IntegerType`, `DoubleType`, etc.).

4. **União Inteligente:** Os DataFrames de cada mês, já limpos e padronizados, são unidos de forma segura usando `unionByName`.

5. **Carregamento e Gerenciamento:** O DataFrame final e limpo é salvo como uma tabela Delta gerenciada pelo Unity Catalog usando o comando `.saveAsTable()`, garantindo compatibilidade com o ambiente Databricks.

## Justificativas das Escolhas Técnicas

As decisões de arquitetura foram tomadas para superar desafios específicos encontrados nos dados de origem e no ambiente Databricks Serverless:

- **Abordagem de Leitura (Looping Individual):** A tentativa inicial de ler todos os arquivos de uma só vez (`spark.read.load(diretorio)`) falhou repetidamente com erros de `CANNOT_MERGE_SCHEMAS`. Isso ocorreu devido a severas inconsistências nos esquemas dos arquivos Parquet de cada mês. A solução de ler cada arquivo de forma isolada dentro de um loop foi a mais robusta, pois evitou a necessidade de o Spark "adivinhar" um esquema unificado durante a leitura.

- **Armazenamento (Unity Catalog Volumes):** O projeto utiliza Volumes do Unity Catalog para a `landing zone`. Esta escolha foi uma necessidade imposta pelo ambiente Databricks Community Edition, que desabilita por segurança o acesso de escrita ao DBFS root (`/FileStore`). O uso de Volumes é a prática moderna e compatível para manipulação de arquivos neste ambiente seguro.

- **Criação da Tabela (`.saveAsTable()`):** A tabela Delta final foi criada com o comando `.saveAsTable()` em vez do tradicional SQL `CREATE TABLE ... LOCATION`. As tentativas com o comando SQL falharam devido a políticas de segurança do Unity Catalog que não suportam a cláusula `LOCATION` apontando para caminhos `dbfs:`. O comando `.saveAsTable()` é a API nativa do PySpark para criar tabelas gerenciadas, deixando que o Databricks gerencie a localização física dos dados, o que é mais seguro e totalmente compatível com o ambiente.

## Estrutura do Repositório

```
ifood-case/
├── src/
│   └── ingestao_e_transformacao.ipynb
├── analysis/
│   └── analises.ipynb
├── README.md
└── requirements.txt
```

## Instruções para Execução

Para replicar esta solução, siga os passos abaixo:

1. **Pré-requisitos:** Uma conta ativa no Databricks Community Edition.

2. **Dados de Origem:** Baixar os dados **"Yellow Taxi Trip Records"** de Janeiro a Maio de 2023 no formato **Parquet** do site oficial [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page "null").

3. **Configuração do Ambiente Databricks:**
   
   - No **Catalog Explorer**, dentro do catálogo `workspace` e do schema `default`, crie um **Managed Volume** com o nome `ifood_landing_zone`.
   
   - Faça o upload dos 5 arquivos `.parquet` baixados para dentro deste volume.

4. **Execução do Pipeline de Ingestão:**
   
   - Importe o notebook `src/ingestao_e_transformacao.ipynb` para o seu workspace no Databricks.
   
   - Anexe um cluster e execute o notebook do início ao fim. Ao final, a tabela `ifood_challenge.yellow_taxi_trips` estará criada e pronta para uso.

5. **Execução das Análises:**
   
   - Importe o notebook `analysis/analises.ipynb`.
   
   - Execute as células de código SQL para visualizar as respostas para as perguntas do desafio.

## Análises Realizadas

O notebook `analysis/analises.ipynb` responde às seguintes questões de negócio:

1. Qual a média de valor total (`total_amount`) recebido por mês?

2. Qual a média de passageiros (`passenger_count`) por hora do dia no mês de Maio?
