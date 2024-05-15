# KMS
- AWS gerencia chaves de criptografia para nós
- Completamente integrado com IAM para autorização
- Auditoria disponível utilizando CloudTrail para todas utilizações da KMS Key
- Integração contínua com a maioria dos serviços da AWS
- KMS Encryption está também disponível através de API Calls - SDK, CLI
- *Encrypted secrets* podem ser armazenadas no código ou em variáveis de ambiente

## Key Types
- KMS Keys é o novo nome para *KMS Customer Master Key*
1. Symmetric (AES-256 keys)
   - Uma única chave de criptografia que é utilizada para encriptar e decriptar
   - Serviços da AWS que são integrados com o KMS utilizam Symmetric CMKs - Customer Master Keys
   - Não é possível ter acesso a KMS Key descriptografada, é necessário chamar a API do KMS
2. Asymmetric (RSA & ECC Key Pairs)
   - Par de chave pública (Encriptar) e privada (Descriptar)
   - Utilizadas para encriptar e descriptografar; ou utilizada para operações de *Sign/Verify*
   - A chave pública é *downloadable*, mas não é possível obter a chave privada decriptada.
   - *Use Case*
	   - Encriptação fora da AWS por usuários que não podem chamar a API do KMS

### Types
- KMS API calls custam $0.003/10000 per call.
- KMS Keys tem escopos definidos dentro de regiões
- AWS Owned Keys
	- Grátis
	- SSE-S3, SSE-SQS, SSE-DDB - keys padrões de serviços
- AWS Managed Key
	- Grátis
	- padrão aws/*service-name*: aws/rds; ou aws/ebs
- Customer Managed Keys Created In KMS
	- Custam $1/mês
- Customer Managed Keys Imported
	- Custam $1/mês
	- Precisam ser keys simétricas

### Automatic Key Rotation
- AWS-managed KMS Key: tem rotação automática a cada 1 ano.
- Customer-managed KMS Key: tem rotação automática a cada um ano, mas é necessário habilitar
- Imported KMS Key: tem apenas rotação manual possível utilizando Alias

## Key Policies
- Controla os acessos a KMS Keys, com a diferença de que, não há como controlar o próprio acesso da key sem a key.
1. Default KMS Key Policy
   - Criada se não for providenciado uma KMS Key Policy
   - Acesso completo da key ao usuário root: toda a conta AWS
2. Custom KMS Key Policy
   - Define usuários, *roles* que podem acessar a KMS Key
   - Define quem pode administrar a KMS Key
   - Útil para *cross-account access* da KMS Key

## Multi-Region Keys
- Há uma *primary key* em uma região que será sincronizada em outras regiões, em caso de Multi-Region Key for habilitado.
- Todas as KMS Keys são idênticas em todas as diferentes regiões que podem ser utilizadas indistintamente.
- Multi-region keys tem o mesmo key ID, o mesmo material, e a mesma rotação automática, se for habilitada na primary key.
- As keys são gerenciadas independentemente em suas respectivas regiões: uma key em us-east-1 pode ter Key Policies do tipo X, e outra key, sincronizada em ap-southeast-1 pode ter Key Policies do tipo Y.
- KMS Multi-Region não é global - primary key + replicas
- *Use Cases*
	- Global client-side encryption;
	- Encryption on Global DynamoDB;
	-  Global Aurora

## S3 Replication Encryption Considerations
- Objetos não criptografados e objetos criptografados com SS3-S3 são replicados por padrão
- Objetos criptografados utilizando SS3-C (Customer Provided Key) podem ser replicados
- Para objetos criptografados com SSE-KMS, é preciso habilitar a opção.
	- Especificar que KMS Key irá encriptar os objetos dentro do bucket alvo
	- Adaptar a KMS Key Policy para o alvo
	- Uma IAM Role com permissão *kms:Decrypt* para a KMS key de origem e uma permissão *kms:Encrypt* para a KMS Key alvo
- É possível utilizar multi-regions AWS KMS Keys, mas elas são tratadas como keys independentes pelo S3

## AMI Sharing Process Encrypted via KMS
1. AMI na conta origem está encriptada com uma KMS Key da própria conta
2. Precisa modificar a imagem na origem e adicionar o atributo *Launch Permission* que corresponda a conta AWS específica
3. Precisa compartilhar a KMS Key utilizada para criptografar a AMI com a conta destino ou com a IAM Role
4. A IAM Role ou o Usuário na conta destino precisa ter as permissões: *DescribeKey*, *ReEncrypted*, *CreateGrant*, *Decrypt*
5. Quando lançando uma instância EC2 a partir desta AMI, opcionalmente a conta destino pode especificar uma nova KMS Key na sua própria conta para re-criptografar os volumes

# SSM Parameter Store
- Armazenamento seguro para configurações ou *secrets*
- Encriptação opcional ininterrupta utilizando KMS
- Serverless, escalável, durável, e com SDK fácil
- *Version tracking* de configurações e *secrets*
- Segurança através do IAM
- Notificações com EventBridge
- Integração com CloudFormation

## Parameter Policies
- Permite adicionar TTL a um parâmetro (expiration date) para forçar atualizações ou deletar dados sensíveis como senhas.
- É possível atribuir diversas policies ao mesmo tempo

# Secrets Manager
- Um novo serviço para armazenamento de *secrets*
- Capacidade de forçar a rotação de *secrets* a cada X dias
- É possível automatizar a geração da rotação de *secrets* utilizando Lambda
- Integrado com RDS - MySQL, PostgreSQL, Aurora
- *Secrets* são encriptados utilizando KMS

## Multi-Region Secrets
- Secrets Manager pode replicar *Secrets* em múltiplas regions
- Secrets Manager mantém *read replicas* em sincronia com uma *Primary Key*
- É possível promover uma *read replica Secret* para uma *Standalone Secret*
- *Use Cases*
	- Multi-region apps;
	- Disaster Recover Strategies;
	- Multi-Region DB;

# AWS Certificate Manager - ACM
- Provisiona, gerencia, e implantação de certificados TLS
- Providencia in-flight encryption para websites - HTTPS
- Suporta certificados TLS públicos e privados
	- Certificados públicos são livres de cobranças
- Renovação automática do certificado
- Integrações com - ELB, CloudFront, APIs on ApiGateway, e mais.
- ACM não pode ser utilizado com EC2s - não podem ser extraídos

## Requesting Public Certificates
1. List domain names to be included in the certificate
   - Fully Qualified Domain Name (FQDN): corp.example.com
   - Wildcard Domain: \*.example.com
2. Select Validation Method: DNS or Email Validation
   - DNS Validation é preferível para propósitos de automação
   - Email validation enviará e-mails para endereços de contato no banco de dados do WHOIS
   - DNS Validation providenciará um CNAME record para o DNS Config - e.g. Route53 ou CloudFlare
   - Leva algumas horas para ser validado
3. The Public Certificate will be enrolled for automatic renewal
   - ACM automaticamente renova ACM-generated certificates 60 dias antes deles expirarem.

## Importing Public Certificates
- AWS dá a opção de gerar certificados fora do ACM e importá-los para ele
- Sem renovação automática. É necessário importar um novo certificado antes deles expirarem
- ACM envia eventos diários de expiração, começando 45 dias antes da expiração
	- The # de dias pode ser configurado
	- Eventos irão aparecer no EventBridge
- AWS Config tem uma *managed rule* chamada *acm-certificate-expiration-check* para checar por certificados expirados
	- O número de dias também pode ser configurado

# Web Application Firewall - WAF
- Protege aplicações web de *common web exploits* (Layer 7)
	- Layer 7 is HTTP
- Pode ser implementado em ELBs, API Gateways, CloudFront, AppSync GraphQL API, Cognito User Pool
1. Define Web ACL (Web Access Control List) Rules
   - IP Set: até 10.000 IP addresses - utilize *multiple rules* para mais IPs, se necessário
   - HTTP headers, HTTP body, ou URI strings protegem contra ataques comuns: SQL Injection ou XSS
   - É possível adicionar *size constraints*, e *geo-match* para bloquear países.
   - Rate-based rules para contar o número de ocorrências de um evento, serve para proteção contra DDoS
- Web ACL são regionais exceto pelo CloudFront
- Um *rule group* é um *set* de regras reutilizável que você pode adicionar a uma web ACL
- **EXAM TIP: Layer 7 focus, the WAF only works for HTTP/HTTPS applications -> NLB is layer 4 for example routing through TCP protocols**

# Shield
## AWS Shield Standard
- Serviço grátis e ativado para todo cliente da AWS
- Providencia proteção contra ataques do tipo SYN/UDP Floods, Reflection Attacks e outros layer 3, layer 4 ataques.
## AWS Shield Advanced
- Serviço de mitigação DDoS opcional
	- $3.000 per month per organization
- Realiza a proteção contra ataques mais sofisticados em EC2s, ELBs, CloudFront, Global Accelerator e Route53
- Acesso 24/7 ao time de resposta a ataques DDoS da AWS (DRP)
- Protegido contra altas cobranças durante picos de uso devido a DDoS attacks
- Shield Advanced providencia atenuação automática de DDoS na camada de aplicação. Cria, avalia, e implementa AWS WAF rules automaticamente para mitigar ataques na camada 7.

# Firewall Manager
- Gerencia *WAF rules* em todas as contas de uma Organization
1. Security Policy: common set of security rules
   - WAF Rules - ALB, API Gateway, CloudFront
   - AWS Shield Advanced - ALB, CLB, NLB, Elastic IP, CloudFront
   - SG for EC2, ALB, and ENI resources in VPC
   - AWS Network Firewall (VPC Level)
   - Route53 Resolver DNS Firewall
   - Policies são criadas no nível de uma região
- Regras são aplicadas a novos recursos enquanto eles são criados, em todas as existentes e futuras contas em uma Organization

# DDoS Best Practice
## REWATCH THE VIDEO

# GuardDuty
- *Intelligent Threat Discovery* para proteção de contas AWS
- Utiliza algoritmos de Machine Learning, detecção de anomalias, 3rd party data
- Input Data
	- CloudTrail Event Logs - unusual API calls, unauthorized deployments
	- VPC Flow Logs - unusual internal traffic, unusual IP address
	- DNS Logs - compromised EC2 instances sending encoded data within DNS queries
	- Optional Features - EKS Audit Logs, RDS and Aurora, EBS, Lambda, S3 Data Events
- EventBridge rules podem ser configuradas para notificar em caso do GuardDuty seja alertado
- Protege contra *CryptoCurrency Attacks* - GuardDuty têm uma *finding* específica para isso

# Inspector
- Avaliações automáticas de segurança
- Inspector realiza as avaliações para serviços específicos: EC2 Instances, Container Images push to ECR, e Lambda Functions
- EC2 Instances
	- Tirando proveito do AWS System Manager Agent
	- Analise contra acesso não intencionado à rede
	- Analise do OS rodando em busca de vulnerabilidades conhecidas
- Container Images push to ECR
	- Avalia as *container images* quando elas são enviadas
- Lambda Functions
	- Identifica vulnerabilidades de software no código da função ou nos pacotes de dependência
	- Avalia as funções quando elas são implementadas
- Tem relatórios e integrações com o AWS Security Hub
- Envia *findings* para o Event Bridge

# Macie
- Serviço de data security e data privacy que utiliza machine learning e pattern matching para descobrir e proteger dados sensíveis na AWS
- Macie ajuda a identificar e alertar sobre dados sensíveis, como PII (Personally Identifiable Information) em um bucket S3 por exemplo