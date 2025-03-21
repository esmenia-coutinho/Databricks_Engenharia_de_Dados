Este repositório contém projeto de engenharia de Dados, usando as seguintes tecnologias:

    - Databricks Community Edition
    
    - PySpark
    
    - Delta Lake
    
    - Arquitetura Medallion (Bronze, Silver e Gold)
    
    - Armazenamento DBFS (Databricks File System)
    
    - Processamento incremental com SCD2


Os códigos ou notebooks foram programados no Databricks e abrangem: pipelines de dados, carga incremental nas tabelas Delta e consultas de análises avançadas.


Análise do Negócio da empresa PanEx. 

A empresa Panex é uma empresa do ramo de varejo internacional. Vamos analisar as vendas desta empresa através das informações que temos sobre: Produto, Categoria, Vendedores, Localização (Cidade e País) e Data. 

![image](https://github.com/user-attachments/assets/adbd1396-1369-44b7-89c7-92e37ac437d3)

Exemplo de uma transação de Venda:

![image](https://github.com/user-attachments/assets/30ee9bdb-ea2f-4503-86cb-838fd38385fd)

As informações de saída desejadas pelo nosso cliente seriam os seguintes indicadores:
  1. Total Vendas (R$) 
  2. Total Desconto (R$) 
  3. Total Quantidade (Unidades) 
  4. Ticket médio de vendas 
  5. Variações Mês sobre Mês (MoM) das métricas acima. 
  6. Valor da percentagem % Share de vendas 

Analisando as informações que temos:

![image](https://github.com/user-attachments/assets/2d7a4d02-5a1b-42eb-89ba-cac234e456da)

A Modelagem Conceitual se apresenta como um SnowFlake. A estrutura de banco relacional é simples. A tabela Fato seria a Venda. As tabelas descritivas ou dimensionais seriam: produtos, categoria, país, cidade, cliente e vendedor.

![image](https://github.com/user-attachments/assets/30d1c303-d112-4214-a553-f24fdfe969ca)

![image](https://github.com/user-attachments/assets/4e453241-ea5c-456d-a66f-b908889c6085)

O Repositório LakeHouse, seria:

![lake_house](https://github.com/user-attachments/assets/5a418db5-1c3a-49e2-ad6c-1f2848de895e)

O Mapa estratégico por entidade, levando em conta suas características do modelo dimensional, tipo de carga suportada nas três camadas e planejamento de periodicidade dessas cargas, seria:

![entidades](https://github.com/user-attachments/assets/ea16485a-3e6b-4ab9-ad85-4fa11c07df2f)

Os códigos, deste projeto, foram organizados e estruturados da seguinte forma:
  
  1️. 000_Preparacao_Ambiente: Neste código contém a configuração do ambiente DBFS para atender a arquitetura Medallion:
  
      "/mnt/panex/lhdw/landingzone/processar/"
      
      "/mnt/panex/lhdw/landingzone/processado/"
  
      "/mnt/panex/lhdw/bronze"
      
      "/mnt/panex/lhdw/silver"
      
      "/mnt/panex/lhdw/gold"
      
  2️. 001_Landing_Zone: Neste código é realizado a importação do código fonte para Landing_Zone/processar. Os arquivos de origens brutos são carregados nesta camada e são organizados em pastas distintas 'Fato'x'Dimensão'. Esta camada será o ponto de partida do código da Camada Bronze. 

  3. 002_Bronze: Neste código é programado a Camada Bronze para Ingestão dos dados no formato Delta. Os arquivos de dados, carregados a partir Landing_Zone/processar, são transformados em tabelas Delta e salvos em arquivos do tipo parquet na Camada Bronze. Os arquivos fonte brutos do tipo csv são movidos para Landing_Zone/processados para controle histórico dos aqrquivos que foram processados para Bronze.

  4. 003_Silver: Neste código é programado a Camada Silver para Transformações e limpeza dos dados. Os arquivos de dados parquet são carregados. O dado é transformado, tratado, adicionado informação de "data da carga", adicionado também colunas de Ano/Mês com base na "data da venda". O dado trabalhado é salvo persistido na Camada Silver.

  5. 004_Gold: Neste código é programado a Camada Gold para Estruturação final dos dados para consumo. O código implementa carga incremental, quer dizer que após a primeira carga de dados, apenas os dados atualizados ou dados novos são carregados na Camada Gold. Os dados são carregados a partir da camada Silver, são enriquecidos com chave substituta e aplicados SCD2. Os arquivos são salvos particionados por Ano/Mês da "data da venda".

  6. 005_Delta_tables: Neste notebook é implemetado o Lake house. As tabelas Delta são carregados com os arquivos da camada Gold para o metastore.
     
  7. 006_Consultas_Pyspark: Neste notebook utilizamos PySpark para realizar consultas nas tabelas Delta. A saída destas consultas são as informações propostas 
  
  
