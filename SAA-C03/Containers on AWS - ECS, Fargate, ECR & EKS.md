# Docker Containers Management on AWS
- **ECS** (Elastic Container Service): Plataforma de container da própria Amazon
- **EKS** (Elastic Kubernetes Service): Kubernetes gerenciado pela Amazon (Open Source)
- **Fargate**: Plataforma de container serverless da Amazon
	- Funciona com ECS e EKS
- **ECR** (Elastic Container Registry): Armazena as imagens dos containers
## Amazon ECS
### Launch Types
1. EC2 Launch Type
   - Lançar uma infraestrutura usando EC2 Launch Type, você precisa provisionar e manter ela (as instâncias EC2).
   - Cada instância EC2 precisa rodar o ECS Agent para ser registrada no ECS Cluster.
   - AWS toma conta de começar ou parar os containers.
2. Fargate Launch Type
   - Não é necessário provisionar infraestrutura, pois Fargate é completamente *Serverless*.
   - Somente necessário criar as task definitions, e definir a quantidade de CPU e Memória necessárias, AWS toma conta do restante.
   - Para escalar, é necessário aumentar a CPU/RAM na task definition.

### IAM Roles for ECS
1. EC2 Instance Profile (EC2 Launch Type apenas)
   - Usado pelo agente do ECS
   - Faz chamadas de API para o ECS Service
   - Quem envia os containers logs para o CloudWatch Logs
   - 'Pull' docker images do ECR
   - Referencia dados sensíveis no Secrets Manager or SSM Parameter Store
2. ECS Task Role
   - Permite que cada task tenha sua função específica
	   - Task A Role -> S3 API Calls
	   - Task B Role -> Dynamo DB API Calls
   - É recomendável utilizar diferentes "roles" para diferentes ECS services
   - Task Role é definida na **task definition**

### Load Balancer Integrations
- Application Load Balancer: Suportado e funciona para a maioria dos *Use Cases*
- Network Load Balancer: Recomendado somente para *High Throughput*/*High Performance* use cases, ou para parear com um AWS Private Link
- Classic Load Balancer: Suportado, mas não recomandado (*No advance features - No Fargate*)

### Data Volumes (EFS)
- Mapear EFS File Systems em ECS Tasks
	- Quando há, no mesmo cluster, serviços de Fargate Launch Type e EC2 Launch Type e eles precisam compartilhar o mesmo file system.
- Funciona para ambos EC2 e Fargate Launch Types
- Tasks rodando em qualquer AZ irão compartilhar os mesmos dados que estão incluídos no EFS File System
- Fargate + EFS = Serverless
- *Use Case*: Persistent multi-Az shared storage for your container
	- Amazon S3 não pode ser *mounted* como um file system.

### Service AutoScaling
1. ECS Service Auto Scaling
	- Automaticamente aumenta ou diminui o número desejado de Tasks
	- ECS AutoScaling usa o AWS Applicaton AutoScaling
		- Média de utilização de CPU
		- Média de utilização de Memória
		- Métricas vindas do ALB -> ALB Request Count per Target
	- Target Tracking
		- Escala baseado em um *target value* para uma métrica específica do CloudWatch
	- Step Scaling
		- Escala baseado em um alarme específico do CloudWatch
	- Scheduled Scaling
		- Escala baseado em um *date/time* específico.
		- Utilizado para mudanças previsíveis
	- ECS Service AutoScaling (Task Level) é diferente do EC2 AutoScaling (EC2 Instance Level)
2. EC2 Launch Type - Auto Scaling EC2 Instances
   - *Accommodate ECS Service Scaling by adding underlying EC2 Instances*
   - Auto Scaling Group Scaling
	   - Escala o AutoScaling Group baseado em alguma métrica como CPU Utilization
	   - Adiciona instâncias EC2 com o tempo, baseado em alguma necessidade, como um *Alarm Trigger*
   - Capacity Provider
	   - Usado para automaticamente provisionar e escalar uma infraestrutura para as ECS Tasks
	   - Capacity Provider é emparelhado com o ASG
	   - Adiciona instâncias EC2 quando há capacidade em falta (CPU/RAM/...)

## Amazon ECR
- Armazena e gerencia Docker images na AWS
- Repositórios privados e público (Amazon ECR Public Gallery)
- Completamente integrado com o ECS, *backed by S3*
- Acesso ao ECR é controlado pelo IAM (Permission errors => policy)
- Suporta *image vulnerability scanning*, versionamento, image tags, e *image lifecycle*

## Amazon EKS
- EKS é uma forma de lançar e gerir Kubernetes Clusters na AWS
- EKS é uma alternativa ao ECS. Objetivos similares, mas APIs diferentes
- EKS suporta instâncias EC2 como worker nodes ou Fargate caso seja requerido o deploy serverless de containers.
- *Use Case*: uma empresa já utiliza K8s on-premises ou em outra cloud, e deseja migrar para a AWS usando o K8s.
- K8s é *cloud-agnostic*: pode ser utilizado em qualquer Cloud (Azure, GCP, etc).
### Node Types
1. Managed Node Groups
   - Cria e gerencia Nodes (Instancias EC2) para mim.
   - Nodes são parte de um ASG gerenciado pelo EKS
   - Suporta *On-Demand* ou *Spot Instances*.
2. Self-Managed Nodes
   - Nodes criados pelo usuário e registrados ao EKS cluster e gerenciados por um ASG.
   - É possível utilizar uma AMI pre-criada especialmente para o EKS: Amazon EKS Optimized AMI
   - Suporta *On-Demand* ou *Spot Instances*.
3. AWS Fargate
   - *No maintenance required; no nodes managed*
### Data Volumes
- É preciso especificar a *StorageClass manifest* no EKS cluster.
- Utiliza uma *Container Storage Interface (CSI) compliant driver*
- Dá suporte a:
	- EBS
	- EFS
		- EFS é o único file system que funciona com o Fargate
	- FSx for Lustre
	- FSx for NetApp ONTAP


## Amazon App Runner
- Serviço completamente gerenciável que facilita o deployment de web applications e APIs em escala.
- Não é necessária a experiência com infraestrutrura.
- Começa apenas com o source code ou um container, o App Runner automaticamente *builds and deploy* a web application
- *Scaling* automático, *highly available*, load banacer e encriptação
- Suporte para acesso com VPC: conexão com banco de dados, cache, e *message queue services*.
- *Use Case*: web applications, APIs, Microserviços, deployments rápidos para produção