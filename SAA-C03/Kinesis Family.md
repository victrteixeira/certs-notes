# Overview
- Makes it easy to collect, process, and analyze streaming data in real-time
- Ingest real-time data such as: Application logs, Metrics, Website clickstreams, IoT telemetry data
- K. D. Streams: Capture, process, and store data streams
- K. D. Firehose: Load data stream into AWS data stores
- K. D. Analytics: analyze data streams with SQL or Apache Flink
- K. Video Streams: capture, process, and store video streams

# Kinesis Data Streams
## Producers
- Producers podem ser vários: applications, client, SDK, KPL, Kinesis Agent
- Cada producer envia um **Record** para o Kinesis. O record é composto de uma Partition Key e um Data Blob de até 1MB
	- A proporção entre consumers e records possíveis é de 1MB/sec ou 1000 msg/sec per shard

## Shards
- Shards em Kinesis define seu **stream capacity** em termos de ingestão de dados e frequência/taxa de consumo.
- Data Blobs serão partidos por todos os shards que compõem a Stream

## Consumers
- Os consumers são as aplicações possíveis de utilizarem os shards da Stream, e elas são: **Apps (KCL, SDK), Lambda Functions, Kinesis Data Firehose, Kinesis Data Analytics**
- Um consumer também recebe um record, que é distinto do producer. O record é composto por uma Partition Key, Sequence Number, e um Data Blob.
	- Sequence Number representa onde o record estava no shard
	- Data Blob é o dado a ser recuperado em si
- Kinesis tem dois tipos diferentes de consumption: Shared e Enhanced
	- Shared: 2 MB/sec per shard all consumers
	- Enhanced: 2MB/sec per shard per consumer
- **EXAM TIP**: basicamente consumption modes se resumem em, todos os consumers dividem e "lutam" pelo mesmo poder computacional no Kinesis, e o segundo é que cada consumer tem seu próprio poder computacional no Kinesis

## Properties of Kinesis Data Streams
- Retention between 1 day to 365 days
- Ability to reprocess (replay) data
- Once data is inserted in Kinesis, it can't be deleted (immutability)
- Data that shares the same partition goes to the same shard (ordering)

# Kinesis Data Firehose
## Producers
- Producers podem ser todos aqueles vistos no K.D.Stream, mas também o próprio K.D.Streams, Amazon CloudWatch (Logs and Events), e AWS IoT
- Cada record tem até 1MB
- Lambda functions podem ser integradas para **Data Transformation**, mas é opcional

## Writes
- Kinesis Data Firehose sabe como escrever dados em outros lugares, diferentemente do K.D. Streams que somente **disponibiliza os dados** para consumidores diversos
- K.D. Firehose realiza **Batch Writes** para diferentes destinos
	- **AWS Destinations**: AWS S3; AWS Redshift (COPY through S3); AWS OpenSearch
	- **3rd-party Destinations**: Datadog; Splunk; New Relic; MongoDB
	- **Custom Destinations**: HTTP Endpoint
- Como opção, é possível enviar all or failed data para um **S3 Backup Bucket**: all or failed data
-  Near Real Time
	- Buffer interval: 0 seconds (no buffering) to 900 seconds
	- Buffer size: minimum 1MB
- Supports many data formats, conversions, transformations, compression
- Supports custom data transformations using AWS Lambda
- Can send failed or all data to a backup S3 bucket