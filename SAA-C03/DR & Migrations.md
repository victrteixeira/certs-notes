# Disaster Recovery Overview
## Concept
- Qualquer evento que tenha impacto negativo na continuidade do negócio de uma empresa ou desastres financeiros
- DR é sobre se preparar e se recuperar de um desastre
- DR Types:
	1. On Premise -> On Premise: traditional DR and very expensive
	2. On Premise -> AWS Cloud: hybrid recovery
	3. AWS Cloud Region A -> AWS Cloud Region B
- RPO se traduz para Recovery Point Objective; e RTO se traduz para Recovery Time Objective

## RPO and RTO
- RPO é basicamente o quão constantemente uma empresa executa backups de seus próprios negócios
- RTO é o período que se recupera de um desastre
- Basicamente, entre o RPO e o desastre haverá *data loss* e entre o RTO e o desastre haverá *downtime*

## Strategies
- Backup and Restore
- Pilot Light
- Warm Standby
- Hot Site / Multi Site Approach
	![[dr-strategies.png]]



# DMS - Database Migration Service
- Migração de banco de dados segura e rápida para AWS, com resiliência e *self-healing*
- O banco de dados original se mantém disponível durante a migração
- Suporta migrações homogêneas: Oracle to Oracle; ou migrações heterogêneas: MSSQL Server to Aurora
- *Continuous Data Replication using CDC*
- Uma instância EC2 precisa ser criada para executar as *replication tasks*
- O flow será: SourceDB -> EC2 Instance Running DMS Software -> Target DB
## DMS Sources and Targets
1. Sources
   - On-Premises and EC2 Instances Databases
   - Azure: Azure SQL Database
   - Amazon RDS (including Aurora)
   - Amazon S3
   - DocumentDB
2. Targets
   - On-Premises and EC2 Instances Databases
   - Amazon RDS
   - Redshift, DynamoDB, S3
   - OpenSearch Service
   - Apache Kafka
   - DocumentDB and Amazon Neptune
   - Redis and Babelfish

## Schema Conversion Tool (SCT)
- SCT serve para converter o *database's schema* de uma engine para outra
- Examples
	- OLTP: SQL Server para MySQL
	- OLAP: Oracle para Amazon Redshift
- Não é necessário utilizar SCT se a migração acontece utilizando a mesma *DB engine*

## Multi-AZ Deployment
- Quando há Multi-AZ habilitado, DMS provisiona e mantém de forma síncrona, uma *stand replica* numa AZ diferente
- As vantagens englobam: *data redundancy*; *eliminates I/O freezes*; *minimizes latency spikes*

# AWS Backup
- Gerencia e automatiza backups de forma centralizada para diversos serviços da AWS
- Sem a necessidade de criar scripts customizados e de executar processos manuais
- Suporta *cross-region backups*
- Suporta *cross-account backups*
- Serviços suportados
	- EC2 / EBS
	- RDS / Aurora / DynamoDB
	- DocumentDB / Neptune
	- EFS / FSx for Lustre and Windows File Server
	- Storage Gateway (Volume Gateway)
- Suporta PITR para o serviços suportados
- On-Demand e Scheduled backups
- Tag-based backup policies
- Backup policies conhecidas como Backup Plans
	- Backup frequency - every 12 hours, daily, weekly, monthly, cron expression
	- Backup window
- Transition to Cold Storage - never, days, weeks, months, years
- Retention Period - always, days, weeks, months, years

## Backup Vault Lock
- Reforça um WORM state (Write Once Read Many) para todos os backups que você armazena no seu backup vault
- Nível adicional de defesa para proteger os backups contra deletes maliciosos ou involuntários; e contra atualizações que encurtem ou alterem os períodos de retenção
- Nem mesmo o *root user* pode deletar backups quando habilitados

# MGN - Application Migration Service
## Application Discovery Service
- Planeje a migração de projetos adquirindo informação acerca dos *on-premise data centers*
- Utilização dos dados pelo servidor, e mapa de dependências são importantes para migrações
- Discovery Service Types
	- Agentless Discovery - Agentless Discovery Conector: VM Inventory, configuration, and performance history such as CPU, memory, and disk usage
	- Agent-based Discovery - Application Discovery Agent: System configuration, system performance, running processes, and details of the network connections between systems
- Os dados resultantes podem ser visualizados no AWS Migration Hub

## Application Migration Service
- *Lift-and-Shift (rehost) solution*  que simplifica a migração de aplicações para AWS
- Converte seu servidor físico, virtual, ou cloud-based para operar nativamente na AWS
- Suporta uma ampla extensão de plataformas, sistemas operacionais e bancos de dados
- Mínimo downtime, e custos reduzidos
![[mgn-diagram.png]]

