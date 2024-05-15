É um novo paradigma em que os desenvolvedores não precisam se preocupar em gerenciar servidores. Inicialmente, serverless significava Function as a Service (FaaS).

- Serverless na AWS:
  1. AWS Lambda
  2. DynamoDB
  3. AWS Cognito
  4. AWS API Gateway
  5. Amazon S3
  6. AWS SNS & SQS
  7. AWS Kineses Data Firehose
  8. Aurora Serverless
  9. Step Functions
  10. Fargate

# Lambda
- Virtual Functions: sem servidores para gerenciar
- Limitado por tempo (15 min): tempo curto de execução
- Roda sob demanda (*On-Demand*)
- *Scaling* é automático, realizado pela própria AWS

### Benefits
1. Pricing
   - *Pay per request and Compute Time* 
   - Free tier de 1 milhão de requisições Lambda e 400.000 GB/sec de *Compute Time* por mês. 
2. Fácil de adquirir mais recursos *per function* (Até 10GB de RAM)
	- Aumentar a RAM, também aumenta os recursos de CPU e Network da function.

### Language Support
- Node.JS (Javascript)
- Python
- Java (Java 8 Compatible)
- C# (.NET Core)
- Golang
- C# / Powershell
- Ruby
- Custom Runtime API
	- Suporte dado pela comunidade, por exemplo Rust
- Lambda Container Image
	- A imagem do container precisa implementar o *Lambda Runtime API*
	- ECS/Fargate é preferível para rodar imagens Docker de uma Lambda.
	- O container Docker não é para a Lambda, é para o ECS / Fargate

### Price
1. *Pay per calls*
   - O primeiro milhão de requests são grátis.
   - $0.20 a cada milhão de request, após o free tier ($0.0000002 por request)
2. *Pay per request*
   - 400.000 GB/sec de *compute time* por mês é grátis
	   - == 400.000 segundos se a função tem 1GB de RAM
	   - == 3.200.000 segundos se a função tem 128MB de RAM
   - Após isso, $1.00 a cada 600.000 GB/sec

### Limits
- Lambda functions tem certos limites **por região**.
1. Execution
   - Memory allocation: 128 - 10GB ( 1MB Increments)
   - Maximum execution time: 900 seconds (15 minutes)
   - Environment variables (4 KB)
   - Disk capacity in the "function container" (in /tmp): 15 MB to 10GB
   - Concurrency Executions: 1000 (can be increased)
2. Deployment
   - Lambda function deployment size (compressed .zip): 50MB
   - Size of uncompressed deployment (code + dependencies): 250MB
   - Can use the /tmp directory to load other files at startup
   - Size of environment variables: 4KB

### SnapStart
- Java Only!
- Melhora a performance de funções Lambda em até 10x sem custos extras para execuções com Java 11 ou acima.
- Quando o SnapStart está habilitado, a função é invocada de um estado "pre-inicializado". Esta step remove a inicialização da função *from scratch*.
![[Pasted image 20240218162845.png]]

### Lambda@Edge & CloudFront Functions
1. Customization At The Edge
   - Muitas aplicações modernas executam alguma forma de lógica em *Edge Locations*
   - Uma Edge Functions é um código escrito e atrelado a uma *CloudFront Distribution*. Executa próximo do usuário final e minimiza a latência.
   - Cloudfront disponibiliza dois tipos de *Edge Functions*: CloudFront Functions e Lambda@Edge
	   - Ambas as opções são serverless, ou seja, sem a necessidade de gerenciamento de um servidor, as funções são *deployed* globalmente.
   - *Use Case*: customizar um *CDN content*
2. Use Cases
   -  Website Security and Privacy
   - Dynamic Web Application at the Edge
   - Search Engine Optimization (SEO)
   - Intelligently Route Across Origins and Data Centers
   - Bot Mitigation at the Edge
   - Real-Time Image Transformation
   - A/B Testing
   - User Authentication and Authorization
   - User Prioritization
   - User Tracking and Analytics
3. CloudFront Functions
   - Cloudfront functions são funções leves escritas em JavaScript
   - Usadas para *high-scale, latency-sensitive CDN customizations*
   - *Sub-ms startup times*, escala até milhões de requests/segundo
   - Usado para mudar as Viewer Requests and Responses
	   - *Viewer Request*: após o CloudFront receber a request de um viewer
	   - *Viewer Response*: antes do CloudFront enviar a response para o viewer
   - CloudFront Functions é uma feature nativa do CloudFront é completamente gerenciado nele, código incluso.
	![[Pasted image 20240218164240.png]]
4. Lambda@Edge
   - Funções Lambda escritas em NodeJS ou Python
   - Escala até milhares de requests/segundo
   - Usado para alterar CloudFront requests e responses
	   - *Viewer Request*: após o CloudFront receber a request de um viewer
	   - *Origin Request*: antes do CloudFront enviar a request para uma origin
	   - *Origin Response*: após o CloudFront receber uma response de uma origin
	   - *Viewer Response*: antes do CloudFront enviar uma response para um viewer
   - Crie a função em uma única AWS region, então o CloudFront irá replicá-la para as suas *locations*.
   ![[Pasted image 20240218164724.png]]

### Lambda in VPC
- Por padrão, funções lambdas são executas fora de sua (usuário) própria VPC. Elas são executas em uma *AWS-owned VPC*. Portanto, acessar AWS resources dentro de uma VPC que não seja pública, não funcionará.
- Para acessar um recurso interno a partir de uma Lambda, é necessário definir um VPC ID, as Subnets e o Security Group
	- A lambda criará um ENI em cada subnet definida, que permitirá o acesso da Lambda a rede interna.

1. RDS Proxy
   - Se funções lambda acessarem diretamente um RDS, pode ocorrer delas abrirem conexões demais causando sobrecarga.
   - O RDS Proxy resolve o problema, pois ele faz *pooling and sharing DB connections*. Melhorando a escalabilidade da aplicação
   - Melhora a disponibilidade reduzindo em 66% o *failover time* e preservando conexões.
   - Melhora a segurança, pois utiliza IAM authentication e armazena credenciais no Secrets Manager.
   - **Lambda Functions precisam ser *deployed* dentro da VPC, pois RDS Proxy NUNCA é publicamente acessível**

### Lambda from RDS & Aurora
- Chamadas Lambda permitem processar eventos de dentro da DB instance.
- Feature disponível apenas para **RDS PostgreSQL e Aurora MySQL**
- É imprescindível a configuração de *Outbound traffic* para a função Lambda de dentro do banco (Public, NAT GW, VPC Endpoints, ...)
- A instância do banco **precisa** ter as permissões necessárias para invocar uma função lambda (*Lambda Resource-based Policy & IAM Policy*).


# DynamoDB
- *Fully Managed, highly-available* com replicação através de multiplas AZs
- NoSQL database, com *transaction support*
- Escala a *workloads* massivos, *distributed database*
- Milhões de requests/segundo, trilhões de linhas, e centenas de Terabytes em capacidade de armazenamento.
- Performance rápida e consistente (*single-digit millisecond*)
- Integrado com o IAM para segurança: autorização e administração
- Baixo custo e com *auto-scaling capabilities*
- Sem manutenção ou patching - *always available*
- Standard and Infrequent Access (IA) Table Classes

### Basics
- DynamoDB é feito de **tabelas**
	- Não há um banco, ele já está lá.
- Cada tabela tem uma **Primary Key** que precisa ser decidida no momento da criação da tabela.
- Cada tabela pode ter um número infinito de itens (= rows)
- Cada item tem atributos, que podem ser adicionados com o passar do tempo, e também podem ser nulos.
- Cada item pode ter no máximo **400KB**
1. Data Types
   - Scalar Types
	   -  String, Number, Binary, Boolean, Null
   - Document Types
	   - List, Map
   - Set Types
	   - String Set, Number Set, Binary Set

### Read/Write Capacity Modes
- Controle como gerenciar a capacidade das tabelas (read/write throughput)
1. Provisioned Mode (default)
   - O usuário precisa especificar o número de reads/writes por segundo na tabela
   - É preciso planejar capacidade antes da criação
   - *Pay per **provisioned*** Read Capacity Units (RCU) e Write Capacity Units (WCU)
   - Possibilidade de adicionar *auto-scaling* mode para RCUs e WCUs
2. On-Demand Mode
   - Read/writes automaticamente *scale up/down* com a carga de trabalho
   - Sem necessidade de planejar capacidade
   - *Pay for what you use*. É uma abordagem mais cara.
   - Ótimo para *workloads* imprevisíveis, com picos súbitos e acentuados.

### DAX (DynamoDB Accelerator)
- *Fully-Managed, Highly Available, Seamless in-memory cache for DynamoDB*
- Ajuda a resolver *reads* gargalos com caching
- **Microseconds** de latência para dados *cached*
- Não requer mudanças de lógica na aplicação para ser implementado
- 5 minutos de TTL para o cache (default)

### Stream Processing
- *Ordered stream of item-level modifications (create/update/delete) in a table*
- *Use Cases*
	- React to changes in real-time (welcome email to users)
	- Real-time usage analytics
	- Insert into derivative tables
	- Implement cross-region replication
	- Invoke AWS Lambda on changes to your DynamoDB table

### Global Tables
- Uma tabela global, é uma tabela que é replicada em diferentes regiões.
- Criado para fazer uma DynamoDB table acessível com baixa latência em múltiplas regiões.
- As aplicações podem *READ and WRITE* na tabela em qualquer região, e as mudanças serão replicadas em todas as outras (*Two-way Replication*)
- Mandatório a habilitação do DynamoDB Streams

### Time to Live (TTL)
- Automaticamente deleta certos itens após o período de expiração
- *Use Case*: reduzir o número de dados armazenados; aderir a obrigações regulamentares; web session handling

### Backups for DR
1. Continuous Backup using Point-In-Time Recovery (PITR)
   - Opcionalmente ativado pelos últimos 35 dias.
   - Point-in-time recovery pra qualquer momento dentro da janela de backup
   - O *recovery process* cria uma nova tabela
2. On-Demand Backups
   - Backups completos para retenções de longo período, até ser explicitamente deletado
   - Não afeta a performance ou a latência do banco
   - Pode ser configurado e gerenciado no AWS Backup (habilita *cross-region copy*)
   - O *recovery process* cria uma nova tabela

### Integrations with Amazon S3
1. Export to S3 (must enable PITR)
   - Funciona para qualquer *point of time* nos últimos 35 dias
   - Não afeta a *read capacity* da tabela
   - *Perform data analysis on top of DynamoDB*
   - Mantém snapshots para auditorias
   - *ETL on top of S3 data* antes de importar novamente para o DynamoDB
   - Exporta em DynamoDB JSON ou ION format.
1. Import to S3
   - Importa em CSV, DynamoDB JSON ou ION formatos
   - Não consome qualquer *write capacity* (WCU)
   - Cria uma nova tabela
   - Erros de import são armazenados no CloudWatch Logs


# API Gateway
- Integra AWS Lambda com API Gateway: zero infraestrutura para gerenciar
- Suporta WebSocket Protocol
- Lida com versionamento de APIs (v1, v2, ...)
- Lida com diferentes environments (dev, prod, test, ...)
- Lida com segurança (Authentication and Authorization)
- Cria API keys para lidar com *request throttling*
- Swagger / Open API imports para rapidamente definir APIs
- Transforma e valida requisições e respostas (*requests and responses*)
- Gera especificações SDK e API
- *Cache API responses*

### Integrations High Level
1. Lambda Function
   - Aciona Lambda Functions
   - A maneira mais fácil de expor uma REST API *backed by* AWS Lambda
2. HTTP
   - Expõe HTTP endpoints no backend
   - *Use Cases*: internal HTTP API on-premise, ALB, ...
3. AWS Service
   - Expõe qualquer serviço interno na AWS através do API Gateway
   - *Use Cases*: começar uma step function workflow; publicar uma message no SQS, e mais.

### Endpoint Types
1. Edge-Optimized (default): For Global Clients
   - Requests são roteados através do CloudFront Edge Locations (para melhor latência)
   - O API Gateway em si, é mesmo assim, ainda, localizado em uma única região.
2. Regional
   - Para clientes dentro da mesma região
   - Pode ser manualmente configurado para combinar com o CloudFront, o que dá maior controle de *caching strategies* e da distribuição
3. Private
   - Pode ser acessado somente dentro da VPC usando uma interface, VPC Endpoint, que é o ENI.
   - É necessário definir uma *resource policy* para definir o acesso ao recurso.

### Security
1. User Authentication Through
   - IAM Roles (útil para aplicações internas)
   - Cognito (*identity* para usuários externos, como mobile users)
   - Custom Authorizer (utiliza sua própria lógica)
2. Custom Domain Name HTTPS Security Through Integration with AWS Certificate Manger (ACM)
   - Se utilizando um Edge-Optimized endpoint, o certificado precisa estar em **us-east-1**
   - Se utilizando um Regional endpoint, o certificado precisa estar na mesma região do API Gateway
   - É necessário configurar o CNAME ou A-alias record no Route53 para apontar para o API Gateway endpoint.


# Step Functions
- Permite a construção de *serverless workflow* para orquestrar lambda functions.
- Features: sequence, parallel, conditions, timeouts, error handling, and more.
- Pode ser integrado com EC2, ECS Tasks, On-Premises Servers, API Gateway, SQS Queues, and more.
- Possibilidade de implementar *human approval feature*
	- Somente executa uma parte da step function, após aprovação humana, caso contrário falha ou executa um workflow distinto.
- *Use Cases*: *Order Fulfillment, Data Processing, Web Applications, Any Workflow*


# Cognito
- Dá aos usuários uma identidade para interagir com as aplicações web ou mobile
1. Cognito User Pools
   - *Sign in* funcionalidade para app users
   - Integrado com API Gateway e ALB
2. Cognito Identity Pools (Federated Identity)
   - Providencia credenciais da AWS para os usuários acessarem alguns recursos da AWS diretamente
   - Integrado com o Cognito User Pools com um *identity provider*
- Cognito vs IAM: 'Hundreds of Users'; 'Mobile Users'; 'Authenticate with SAML'

### Cognito User Pools (CUP)
1. User Features
   - Cria um *serverless database* de usuário para a web ou mobile application
   - Login simples: Username (ou e-mail) / Password combination
   - Password reset
   - Verificação de Email e Número de Telefone
   - MFA
   - Federated Identities: usuários do Facebook, Google, SAML, e mais.
2. Integrations
   - CUP se integra com o API Gateway e Application Load Balancer
	![[Pasted image 20240218200603.png]]

### Cognito Identity Pools (Federated Identities)
- Diferentemente do CUP que dá autorização para acessar um recurso através de outro, como o API Gateway ou ALB, o CIP dá acesso a alguns usuários de obter acesso temporário a credenciais da AWS
- A *Users Source* pode ser o CUP, 3pt logins, etc
- Usuários podem acessar serviços da AWS diretamente ou através do API Gateway
- As IAM Policies aplicadas as credenciais são definidas no próprio Cognito
- Pode-se customizar as credenciais baseado no user_id para um controle mais granulado
- Há IAM Roles padrão para usuários e convidados autenticados.


EXAM TIPS:
- To make it easier to remember the different between User Pools and Identity Pools, think of Users Pools as being like IAM Users or Active Directory and an Identity Pools as being like an IAM Role.