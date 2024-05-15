- High Performance Computing é excelente para performar sequenciamento de genoma, química computacional, modelos de riscos financeiros, previsão do tempo, machine learning, deep learning, *autonomous driving* e mais.

# Data Management & Transfer
- O primeiro tópico de HPC na AWS é como transferir e gerenciar quantidades enormes de dados em Cloud de forma ultra performática, utilizando-se de High Performance Computation.
### AWS Direct Connect
- Move GB/s de dados na nuvem utilizando uma *private secure network*
### AWS Snowball & AWS Snowmobile
- Move PB de dados para a nuvem
### AWS DataSync
- Ajuda a mover enormes quantidades de dados entre servidores on-premise e AWS S3, EFS, FSx for Windows

# Compute and Networking
### EC2 Instances
- Com máquinas otimizadas para performar tarefas com CPU ou GPU workloads
- Spot instances / Spot fleets para salvar custos + Auto Scaling
- EC2 Placement Groups, como o *Cluster* para otimizar *networking performance*
	- Colocando todas as instâncias na mesma AZ e no mesmo Rack, alcançando *low latency 10Gbps network*

### EC2 Enhanced Networking - SR-IOV (ENA)
- *Higher bandwidth, higher PPS* (*Packets per second*), e *lower latency*
- Elastic Network Adapter vai até 100 Gbps para esta interface
- A segunda opção, legado, é o *Intel 82599 VF* que vai a 10 Gbps
### EC2 Enhanced Networking (EFA)
- EFA é um ENA melhorado para HPC, e funciona somente em máquinas Linux
- Workloads do tipo *inter-node communications, tighly coupled*
- *Leverages Message Passing Interface (MPI) standard*
- *Bypasses the underlying Linux OS to provide low-latency, reliable transport*

# Storage
### Instance-attached Storage
- EBS escala até 256.000 IOPS com io2 Block Express
- Instance Store escala a milhões de IOPS, anexado a uma instância EC2, com baixa latência, porém de conteúdo efêmero

### Network Storage
- S3: *large blob, not a file system*
- EFS: *scale IOPS based on total size, or use provisioned IOPS*
- FSx for Lustre: *HPC optimized distributed file system, millions of IOPS*

# Automation and Orchestration
### AWS Batch
- Suporta *jobs* paralelos em multi-nodes, o que permite executar uma única tarefa que *span multiple EC2 instances*
- Fácil de organizar os *jobs* e iniciar instância EC2 de acordo com a necessidade

### AWS ParallelCluster
- Ferramenta de gerenciamento Open-Source de cluster para realizar implantação de HPC na AWS
- Configurado utilizando *text files*
- Automatiza a criação de VPC, Subnet, Cluster Type e Instance Types
- Permite ativar EFA no cluster (melhora a performance da rede)