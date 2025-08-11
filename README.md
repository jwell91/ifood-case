# Case Técnico de Arquitetura de Dados - iFood

## Descrição do Projeto

Este projeto implementa um pipeline de dados ponta a ponta para o desafio técnico de Arquitetura de Dados do iFood. Esta versão aprimorada refatora a solução original para seguir padrões de código de produção, enriquece a análise de dados com exploração visual e implementa validações de qualidade de dados.

A solução realiza a ingestão, o processamento, a validação e a disponibilização para análise dos dados de corridas de táxis amarelos de Nova York (Janeiro a Maio de 2023). O pipeline foi desenvolvido em PySpark no ambiente Databricks, resultando em uma tabela Delta limpa, otimizada e confiável, pronta para consumo analítico.

## Arquitetura da Solução

O pipeline foi reestruturado para separar o código de engenharia (produção) da análise exploratória (investigação).

1. **Engenharia de Dados (`src/main.py`):** A lógica de ETL foi migrada de um notebook para um script Python modular e parametrizado. Isso simula um ambiente de produção onde o código é versionado, testável e pode ser agendado em jobs automatizados. O script executa a ingestão, transformação e validação dos dados.

2. **Análise de Dados (`analysis/analises_aprimorado.ipynb`):** Um novo notebook de análise que começa com uma Análise Exploratória de Dados (EDA) para entender a distribuição e a qualidade dos dados. Em seguida, responde às perguntas de negócio com o suporte de visualizações gráficas para facilitar a interpretação dos resultados.

## Justificativas das Escolhas Técnicas

Além das justificativas originais, esta versão incorpora as seguintes decisões:

- **Refatoração para Script Python (`.py`):** A migração da lógica de ETL de um notebook para um script Python (`src/main.py`) é uma prática padrão da indústria. Scripts são mais adequados para automação (jobs), testes unitários e integração contínua (CI/CD), separando o código de produção do ambiente interativo de análise.

- **Parametrização do Pipeline:** O script de ingestão foi parametrizado para aceitar caminhos de entrada e nomes de tabela como argumentos. Isso o torna mais flexível e reutilizável, permitindo que o mesmo código seja usado para diferentes ambientes (desenvolvimento, produção) ou fontes de dados sem modificações.

- **Análise Exploratória e Visualização:** A adição de uma seção de EDA e gráficos (histogramas, gráficos de barras) no notebook de análise é crucial. Isso não apenas valida a qualidade dos dados, mas também enriquece a comunicação dos resultados, tornando os insights mais acessíveis para um público não técnico.

- **Gerenciamento de Dependências (`requirements.txt`):** O arquivo de dependências foi atualizado para incluir bibliotecas de visualização (`matplotlib`, `seaborn`), tornando o ambiente de análise totalmente reprodutível.

## Estrutura do Repositório

```
ifood-case/
├── src/
│   └── main.py
├── analysis/
│   └── analises_aprimorado.ipynb
├── README.md
└── requirements.txt
```

## Instruções para Execução

Para replicar esta solução aprimorada, siga os passos abaixo:

1. **Pré-requisitos:**
   
   - Uma conta ativa no Databricks Community Edition.
   
   - Cluster configurado com Databricks Runtime que suporte as bibliotecas do `requirements.txt`.

2. **Dados de Origem:**
   
   - Baixar os dados **"Yellow Taxi Trip Records"** de Janeiro a Maio de 2023 no formato **Parquet** do site oficial [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page "null").

3. **Configuração do Ambiente Databricks:**
   
   - No **Catalog Explorer**, dentro do catálogo `workspace` e do schema `default`, crie um **Managed Volume** com o nome `ifood_landing_zone`.
   
   - Faça o upload dos 5 arquivos `.parquet` baixados para dentro deste volume.
   
   - Importe o script `src/main.py` e o notebook `analysis/analises_aprimorado.ipynb` para seu workspace.

4. **Execução do Pipeline de Ingestão:**
   
   - Crie um novo notebook no Databricks para orquestrar a execução do script.
   
   - Adicione a seguinte célula e execute-a. O comando `%run` executa o script Python como se fosse um job, passando os parâmetros necessários.

```
# Parâmetros para o pipelinelanding_path = "/Volumes/workspace/default/ifood_landing_zone/"target_table = "ifood_challenge.yellow_taxi_trips"# Executando o script de ingestão com os parâmetrosdbutils.notebook.run(    path = "./src/main.py",    timeout_seconds = 600,    arguments = {        "landing_path": landing_path,        "target_table": target_table    })
```

5. **Execução das Análises:**
   
   - Abra o notebook `analysis/analises_aprimorado.ipynb`.
   
   - Anexe o notebook a um cluster que tenha as bibliotecas `matplotlib` e `seaborn` instaladas.
   
   - Execute as células para visualizar a EDA e as respostas para as perguntas do desafio.

## Análises Realizadas

O notebook `analysis/analises_aprimorado.ipynb` contém:

1. **Análise Exploratória de Dados (EDA):**
   
   - Verificação de schema e tipos de dados.
   
   - Estatísticas descritivas das colunas numéricas.
   
   - Análise de valores nulos.
   
   - Visualização da distribuição do `total_amount` e `passenger_count`.

2. **Respostas às Perguntas de Negócio (com visualização):**
   
   - Qual a média de valor total (`total_amount`) recebido por mês?
   
   - Qual a média de passageiros (`passenger_count`) por hora do dia no mês de Maio?
