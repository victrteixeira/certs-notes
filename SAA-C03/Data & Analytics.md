# Athena
### Overview
- Serviço de query serverless para analisar dados armazenados no Amazon S3
- Utiliza *standard SQL Language* para realizar as consultas nos arquivos (*built on Presto*)
- Suporta CSV, JSON, ORC, Avro e Parquet
- Preço de $5.00 per TB de dados escaneados
- Usado comumente com o Amazon Quicksight para desenvolvimento de reportes e dashboards
- *Use Cases:* Business Intelligence; Analytics; Reporting, Analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc
- *Exam Tip*: analisar dados no S3 usando *serverless SQL*, use Athena

### Performance Improvement
- Utilizar *columnar data types* para salvar custos (*less scan*)
	- Apache Parquet ou ORC é recomendado
	- Grande melhoria de performance
	- Utilizar o Glue para converter os dados para Parquet ou ORC
- Comprimir os dados *for smaller retrievals* (bzip2, gzip, lz4, snappy, zlip, zstd)
- Particionar os *datasets* no S3 para buscas mais fáceis na colunas virtuais
```js
s3://myBucket/pathToTable/
<PARTITION_COLUMN_NAME>=<VALUE>/
	<PARTITION_COLUMN_NAME>=<VALUE>/
		<PARTITION_COLUMN_NAME>=<VALUE>/
			<PARTITION_COLUMN_NAME>=<VALUE>
				/etc...
```
```js
s3://athena-examples/flight/parquet/year=1991/month=1/day=1
```

- Utilize arquivos grandes ( > 128 MB) para minimizar *overheads*

### Federated Query
- Permite rodar SQL queries através de dados armazenados em relational, non-relational, object e *custom data sources* (AWS ou On-Premise)
- Utiliza *Data Source Connectors* que roda em uma AWS Lambda para rodar as *Federated Queries* (e.g. CloudWatch Logs, DynamoDB, RDS, etc)
- Armazena os resultados de volta no Amazon S3
![[Pasted image 20240221211552.png]]

# Redshift
### Overview

- Redshift é baseado no PostgreSQL, mas não é utilizado para OLTP
- Redshift é OLAP - Online Analytical Processing (*analytics and data warehouse*)
- Performance 10x melhor que qualquer outro *data warehouse*, escala a Petabytes de dados
- *Columnar storage data* (invés de ser baseado em linhas - row based) e *parallel query engine*
- *Pay as you go* baseado na instancias provisionadas
- Tem uma interface SQL para performar queries
- BI tools como o Amazon Quicksight ou Tableau se integram com ele
- vs Athena: queries, joins e aggregations mais rápidos

### Cluster
1. Leader Node
   - Para planejamento de queries, e *result aggregation*
2. Compute Node
   - Para performar as queries em si, e enviar os resultados ao *leader*
- É preciso provisionar o tamanho dos nodes antecipadamente, e pode-se usar *Reserved Instances* para salvar custos

### Snapshots & DR
- Redshift tem *Multi-AZ* modo para alguns clusters
- Snapshots são *point-in-time backups* de um cluster, armazenados internamente no S3
- Snapshots são incrementais: somente coisas que mudam são salvas
- É possível restaurar uma snapshot em um novo cluster.
- Snapshots são
	- Automáticas: a cada 8 horas, a cada 5GB, or baseada em um cronograma. É necessário selecionar o período de retenção
	- Manuais: snapshot permanece até que você as delete
- É possível configurar o Redshift para automaticamente copiar as snapshots (automáticas ou manuais) de um cluster para uma outra região da AWS

### Loading Data into Redshift
- A melhor forma de inserir dados no Redshift é com *Large inserts*
- Kineses Data Firehose: insere dados no Redshift cluster através de s3 Copy command
- S3: insere dados no Redshift utilizando o COPY command
- EC2 Instance: insere dados no redshift utilizando JDBC Driver.
	- Melhor escrever os dados em lotes
	  
### Spectrum
- Realiza queries dos dados que já estão armazenados no S3 sem ter que carregá-los no Redshift
- É mandatório ter um Redshift cluster disponível para começar a query
- As queries são submetidas a milhares de Redshift Spectrum Nodes que performam certo poder computacional, e envia os resultados aos nossos *Compute Nodes*.
![[Pasted image 20240221214226.png]]

# Amazon OpenSearch
### Overview
- Amazon OpenSearch é o sucessor do Amazon ElasticSearch
- No OpenSearch é possível procurar por qualquer coisa, até mesmo combinações parciais
- É comum utilizar o OpenSearch como um complemento de outro banco de dados
- OpenSearch Cluster Provision Modes
  1. Managed Cluster
  2. Serverless Cluster
- Não suporta SQL nativamente (pode ser habilitado com um plugin)
- Ingere dados do Kinesis Data Firehose, AWS Iot, and CloudWatch Logs
- A segurança do serviço é feita pelo Cognito, IAM, KMS Encryption, e TLS
- Vem com o OpenSearch Dashboards (para visualização)


# EMR
### Overview
- EMR significa Elastic MapReduce
- Ajuda na criação de Hadoop Clusters (para Big Data) para analizar e processar grandes quantidades de dados
- Os clusters podem ser feitos de centenas de instancias EC2
- EMR vem em conjunto com Apache Spark, HBase, Presto, Flink, etc
- EMR toma conta de todo provisionamento e configuração
- Auto-scaling e integrado com Spot Instances
- *Use Case*: data processing, machine learning, web indexing, big data

### Node Types & Purchasing
- Master Node: gerencia o cluster, coordena e gerencia a saúde - long running
- Core Node: Roda as tarefas e armazena os dados - long running
- Task Node (optional): Somente roda as tasks
- Purchase Options
  1. On-Demand: reliable, predictable, won't be terminated
  2. Reserved (min 1 year): cost savings (EMR will automatically use if available)
  3. Spot Instances: cheaper, can be terminated, less reliable
- Pode-se ter *long-running* cluster, ou *transient* (temporário) cluster


# QuickSight
### Overview
- *Serverless machine learning-powered business intelligence* service para criar dashboards interativos
- Rápido, com scaling automático, incorporado com *per-session pricing* 
- Integrado com RDS, Aurora, Athena, Redshift, S3
- In-memory computation utilizando *SPICE engine* se os dados são importados para o QuickSight
- Enterprise edition: possibilidade de configurar Column-Level Security (CLS)

### Dashboard & Analysis
- Define-se os usuários (standard version) e os grupos (enterprise version). Estes usuários e grupos só existem dentro do QuickSight e não no IAM.
- Um dashboard é uma *read-only snapshot* de uma analise que pode ser compartilhada
- Preserva-se a configuração analise (filtros, parâmetros, controles, ordem, etc)
- Compartilha-se a análise ou o dashboard com os usuários ou grupos, e para compartilhar é preciso primeiramente publicá-lo.
- Os usuários que veem o dashboard, podem também ver *the underlying data*.

# Glue
### Overview
- *Managed extract, transform, and load (ETL) service*
- Útil para preparar e transformar dados para *analytics*
- Serviço completamente serverless
- *Glue Job Bookmarks*: previne o reprocessamento de dados antigos
- *Glue Elastic Views*
	- Combina e replica dados através de múltiplos armazenamentos de dados usando SQL
	- Sem código customizado, Glue monitora por mudanças nas fontes de dados
	- Utiliza uma "virtual table" - *materialized view*
- *Glue DataBrew*: limpa e normaliza dados usando *pre-built transformation*
- *Glue Studio*: nova GUI para criar, rodar e monitorar ETL jobs no Glue
- *Glue Streaming ETL*
	- Invés de ler os dados em *batch mode*, ele lê e transforma os dados em *streaming mode*.
	- Compatível com Kineses Data Streaming, Kafka, MSK
	![[glue.png]]

# Lake Formation
### Overview
- Um *Data Lake* é um local central onde se pode ter todos os dados para propósitos analíticos.
- Serviço provisiona data lakes em alguns dias
- *Discover, cleanse, transform and ingest data into Data Lake* 
- Automatiza passos manuais bem complexos - como coletar, limpar, mover ou catalogar dados e re-duplica (usando ML Transforms)
- Combina dados estruturados e não-estruturados no data lake
- *Out-of-the-box source blueprints*: S3, RDS, Relational and NoSQL Databases
- *Fine-grained Access Control* para as aplicações (row and column-level)
- Criado em cima do AWS Glue

# Kinesis Data Analytics
### For SQL Application
- Real-time analytics no Kinesis Data Streams & Firehose usando SQL
- Adiciona referência de dados do S3 para enriquecer *streaming data*
- Pay per actual consumption reate
- Output
	- Kinesis Data Streams: cria streams a partir das consultas de análises em tempo real
	- Kinesis Data Firehose: enviar o resultado de análise de queries para os seus destinos
- *Use Cases*: time-series analytics; real-time dashboards; real-time metrics

### For Apache Flink
- Utiliza Flink (Java, Scala ou SQL) para processar e analisar *streaming data*
- Roda qualquer Apache Flink application em cluster gerenciável na AWS
	- Provisiona *compute resources, parallel computation, automatic scaling*
	- Application backups (implementado como checkpoints e snapshots)
	- Utilize qualquer Apache Flink features
	- Flink não lê do Firehose - é preciso utilizar o Kinesis Analytics for SQL


# MSK - Managed Streaming for Kafka
### Overview
- Alternativa para o Kinesis
- Apache Kafka na AWS, que permite criar, atualizar, deletar clusters
- MSK cria e gerencia Kafka brokers nodes & Zookeeper nodes para você
- *Deploy* o MSK Cluster na VPC, multi-AZ (até 3 para alta disponibilidade)
- *Automatic recovery* de falhas comuns do Apache Kafka
- Os dados são armazenados em EBS volumes por tempo indeterminado

### MSK Serverless
- Provisiona MSK Cluster sem a necessidade de gerenciar capacidade.
- MSK automaticamente provisiona recursos e escala poder computacional e armazenamento