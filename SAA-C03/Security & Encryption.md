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