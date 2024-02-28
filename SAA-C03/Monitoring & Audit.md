# CloudWatch
## Metrics
- Cloudwatch providência métricas para todos os serviços da AWS
- Métricas são uma variável a monitorar - como CPUUtilization, NetworkIn, etc.
- Métricas pertencem a *namespaces*
- *Dimension* é um atributo de uma métrica - como instanceId de CPUUtilization de uma EC2
- Até 30 *dimensions per metric* 
- Métricas têm *timestamps*
- Há a possibilidade de criar dashboards de métricas
- *CloudWatch Custom Metrics* são possíveis de serem criadas - como RAM Utilization de uma EC2 por exemplo

### Metric Streams
- Contínuo streaming de CloudWatch Metrics para um destino de sua escolha, com *near-real-time delivery and low latency*
	- Kinesis Data Firehose; or 3rd party service providers como Datadog, Dynatrace, New Relic, Splunk, Sumo Logic
- É possível filtrar métricas para realizar o streaming de apenas um *subset* delas.

## Logs
- Log Groups: nomes arbitrários, usualmente representam a aplicação
- Log Streams: instâncias dentro da aplicação; log files; containers
- *Log Expiration Policy* pode ser definida entre: nunca expirar ou de 1 dia a 10 anos
- Logs podem ser exportados para: S3; Kinesis Data Streams; Kinesis Data Firehose; Lambda; OpenSearch
- Logs são criptografados por padrão
	- Contudo, é possível definir KMS-based encryption para utilizar suas próprias keys

### Sources
- SDK; CloudWatch Logs Agent; e CloudWatch Unified Agent (deprecated)
- Elastis Beanstalk: coleção de logs da aplicação
- ECS: coleção de logs dos containers
- Lambda: coleção de logs das funções
- VPC Flow Logs: logs específicos de VPC
- API Gateway: logs de cada request
- CloudTrail baseado em filtros
- Route53: Logs de DNS Queries

### Logs Insights
- Ferramenta para procurar e analisar *log data* armazenados no CloudWatch Logs
- Providência uma *purpose-built query language*
	- Automaticamente descobre campos de serviços da AWS e log de eventos JSON
	- Obtém os eventos dos campos desejados, filtra baseado em condições, calcula estatísticas agregadas, limita a um número específico de eventos, etc.
	- Podem ser salvas as queries escritas para adicioná-las ao CloudWatch Dashboards
- Pode pesquisar por múltiplos log groups, mesmo em diferentes contas das AWS
- É uma *query engine*, e não uma *real-time engine*

### Logs Subscriptions
- Para o S3 *Log data* pode tomar até 12 horas para se tornar disponível para export. A chamada de API a se fazer é *CreateExportTask*. Não é *near-real time* ou *real-time*. Para isso utilize Logs Subscriptions
- Administra *log events* que vem do CloudWatch Logs em tempo real para processamento e análise.
- Envia para o Kinesis Data Streams/Data Firehose, ou Lambda
- *Subscription Filter*
	- Filtra os log events que devem ser enviados para o seu destino específico.
- Subscriptions permite criar *log aggregation* de múltiplas contas, ou múltiplas regiões para um único destino comum a tudo isso.
	- Para isso é preciso criar um *Cross-Account Subscription* para enviar os *log events* para recursos numa conta AWS diferente
- ![[Pasted image 20240226203428.png]]

### Agent & Logs Agent
- Por padrão, nenhuma instância EC2 envia logs para o CloudWatch. É necessário instalar um CloudWatch Agent para isso, com as devidas permissões IAM configuradas
- O CloudWatch Agent pode ser configurado em servidores on-premise também

1. **CloudWatch Logs Agent - Older**
   - Versão antiga do Agent
   - Pode enviar apenas CloudWatch Logs
2. **CloudWatch Unified Agent - Newer**
   - Coleta métricas adicionais de nível de sistema, como RAM, processos e mais.
	   - CPU - active, guest, idle, system, user, steal
	   - Disk Metrics - free, used, total; Disk IO - writes, reads, bytes, iops
	   - RAM - free, inactive, used, total, cached
	   - Netstat - number of TCP and UDP connections, net packets, bytes
	   - Processes - total, dead, bloqued, idle, running, sleep
	   - Swap Space - free, used, used %
   - Coleta os logs para enviar ao CloudWatch Logs
   - Configuração centralizada utilizando o SSM Parameter Store

## Alarms
- Utilizados para disparar notificações para qualquer métrica
- Diversas opções de thresholding: sampling, percentage, max, min, etc
- Alarm States
	- OK
	- INSUFFICIENT_DATA
	- ALARM
- Periods
	- Tempo em segundos para avaliar uma métrica
	- *High resolution custom metrics: 10 sec, 30 sec or multiples of 60 sec*
- Targets
	- O serviço ao qual o CloudWatch Alarm irá definir sobre o seu estado: se uma EC2 está *running*, o estado do alarme será OK, mas se a EC2 entrou em *rebooting*, o estado do alarme sera IN ALARM

### Composite Alarms
- Composite Alarms monitoram o estado de múltiplos outros alarmes
- Pode-se incluir condições lógicas: AND e OR
	- AND: um composite alarm só dispara quando, todos os alarmes sendo monitorados disparam
	- OR: um composite alarm dispara quando, um ou mais alarmes sendo monitorados disparam
- Ajuda a reduzir *alarm noise* criando *composite alarms* mais complexos

## Events (EventBridge)
- Events são um recurso do CloudWatch que responde a diversos acontecimentos com uma resposta pre-configurada
	- Schedule - cron jobs (scheduled scripts)
	- Event Pattern - event rules to react to a service doing something
	- Trigger Lambda Functions, send SQS/SNS Messages, and more
- EventBridge têm três tipos distintos de *Event Bus*
	- Default Event Bus - AWS Services
	- Partner Event Bus - AWS SaaS Partners, such as Datadog or Zendesk
	- Custom Event Bus - Custom Application can send their events
- Event Buses podem ser acessados por outras contas AWS usando *Resource-based policies*
- Eventos enviados (all/filter) a um *event bus* podem ser arquivados (indefinitely or set period)
	- Eventos arquivados têm a habilidade de *replay*

### Schema Registry
- EventBridge pode analisar eventos do *bus* e inferir a schema a partir disso
- O *Schema Registry* permite você gerar código para a aplicação que irá saber antecipadamente como os dados são estruturados no *event bus*
- A Schema pode ser versionada

### Resource-based Policy
- Gerencia as permissões de um *Event Bus* específico.
- Permite ou nega eventos de outras contas AWS ou de outras regiões
- *Use Case*
	- Aggregate all events from your AWS Organizations in a single AWS account or AWS region

## Insights
### Container Insights
- Coleta, agrega, e sumariza métricas e logs de containers
- Containers disponíveis são: ECS, EKS, Fargate e Kubernetes Platforms on EC2
- No EKS e Kubernetes, CloudWatch Insights utiliza uma versão containerizada do CloudWatch Agent para descobrir os containers

### Lambda Insights
- Solução para monitoramento e tratamento de erros para aplicações serverless rodando na Lambda
- Coleta, agrega e sumariza *system-level metrics* incluindo CPU time, memory, disk, and network
- Coleta, agrega e sumariza informação para diagnóstico de *cold starts* e *lambda worker shutdowns*
- Lambda Insights é providenciado como um Lambda Layer

### Contributor Insights
- Analise os *log data* e crie *time series* que mostram dados do *contributor*
	- Visualizar métricas sobre o *top-N contributors*
	- O número total de *contributors* únicos e seu uso
- Ajuda a achar *top talkers* e entender quem ou o que está impactando na performance do sistema
- Funciona para qualquer *AWS-generated logs*
- É possível construir as regras do zero, ou utilizar regras modelos que a AWS já criou. CloudWatch também providencia regras pre-prontas que podem ser utilizadas para analisar métricas de outros serviços AWS

### Application Insights
- Providencia dashboards automáticos que mostram problemas potenciais de aplicações monitoradas, ajudando a isolar problemas em curso.
- Melhora a visibilidade da saúde da aplicação reduzindo o tempo gasto para descobrir o problema e reparar a aplicação
- *Findings and Alerts* são enviados ao EventBridge e SSM OpsCenter

# CloudTrail
- Providencia governo, conformidade e auditoria para uma conta AWS
- CloudTrail é habilitado por padrão
- Disponibiliza um histórico de eventos / API calls feitos na conta pelo Console, SDK, CLI ou por outros serviços da própria AWS
- Os logs podem ser enviados para o CloudWatch Logs ou S3
- Um *trail* pode ser aplicado para todas as regiões (padrão) ou para uma região única

## Events
### Management Events
- Operações executadas em recursos da conta AWS
- Por padrão, *trails* são configurados *to log management events*
- É possível separar *Read Events* - que não modificam os recursos - de *Write Events* - que modificam os recursos.
- *Examples*
	- Configuring security (IAM AttachRolePolicy)
	- Configuring rules for routing data (Amazon EC2 CreateSubnet)
	- Setting up logging (AWS CloudTrail CreateTrail)
### Data Events
- Por padrão, *data events* não têm logs, pois este tipo de evento opera com um alto volume de operações
	- GetObject operations on S3 bucket per example.
- Atividades de execução de uma função Lambda (the Invoke API)
### Insight Events
- Habilite o CloudTrail Insights para detectar atividades incomuns na conta AWS
	- Inaccurate resource provisioning
	- Hitting service limits
	- Burst of AWS IAM actions
	- Gaps in periodic maintenance activity
- CloudTrail Insights analisa eventos normais de gestão para criar um histórico de atividades comuns. Após isso, ele continuamente analisa *write events* para detectar padrões incomuns
	- Anomalias aparecem no CloudTrail console
	- Eventos são enviados ao Amazon S3
	- Um EventBridge event é gerado - para possíveis necessidades de automatizações

### Event Retention
- Eventos são armazenados por padrão por 90 dias no CloudTrail
- Para manter os eventos por um período maior que 90 dias, os logs precisam ser enviados ao S3 (e talvez utilizar o Athena para pesquisar pelo que é preciso)

# Config
- Ajuda a auditar e manter gravações para conformidade nos recursos da AWS
- Ajuda a gravar configurações e visualizar mudanças durante o passar do tempo
- É possível receber alertas - SNS Notifications - para qualquer mudanças
- AWS Config é um serviço *per-region*. Mas pode ser agregado em uma única região ou conta.
- As configurações são passíveis de serem armazenadas no S3.

## Config Rules
- AWS têm cerca de 75 config rules pré-prontas
- É possível criar config rules customizadas - precisam ser definidas no AWS Lambda
	- Ex: evaluate if each EBS disk is of type gp2
	- Ex: evaluate if each EC2 instance is t2.micro
- Regras podem ser *evaluated / triggered*: para cada mudança de configuração; e / ou em um intervalo específico de tempo.
- Config Rules não impedem ações de acontecerem - não há configurações de *deny*.
- Pricing
	- No free tier
	- $0.003 per configuration item recorded per region
	- $0.001 per config rule evaluation per region

## Remediations
- Automatiza remediações de recursos não-conformes usando o SSM Automation Documents
- Há Automation Documents pré-prontos feitos pela AWS, porém é possível utilizar *Custom Automation Documents*
- É possível configurar *remediation retries* se um recurso ainda não está não-conforme após a auto-remediação (*auto-remediation*)
- ![[Pasted image 20240227191546.png]]

## Notifications
- Utilize EventBridge para disparar notificações quando um recurso está não-conforme.
- Há a habilidade de enviar notificações de mudanças de configuração e estado de conformidade para o SNS
	- Todos os eventos serão enviados, é preciso utilizar um SNS Filter para melhor seleção.