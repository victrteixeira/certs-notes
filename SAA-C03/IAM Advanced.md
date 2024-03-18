# Organizations
- Organizations são um serviço global
- Permite o gerenciamento de múltiplas contas AWS
- Há a conta principal chamada *management account*. Todas as outras são *member accounts*.
- *Member Accounts* só podem fazer parte de uma única *organization*
- Faturação consolidada em todas as contas com um único método de pagamento
- Benefícios de preço para uso agregado de contas - descontos de volumes para EC2, S3, etc
- *Reserved Instances* e *Saving Plans discounts* compartilhados em todas as contas
- API está disponível para automatizar a criação de contas AWS

## Organization Unit (OU)
- Há a *Root Organizational Unit* e dentro dela é possível configurar a *Management Account*.
- Dentro de uma *OU* é possível configurar *Sub-OUs*, que seriam pequenos conglomerados de contas AWS dentro de uma mesma Organization: OU de Dev; OU de Prod; OU de Finanças; OU de RH; etc.

## Advantages
- Multi Account Multi VPCs utilization
- Utilização de *tagging standards* para propósitos de cobrança/faturamento
- É possível habilitar CloudTrail para todas as contas e enviar todos os logs para um S3 central.
- Enviar todos os Logs CloudWatch para uma conta central para logging por exemplo
- Estabilizar em todas as contas *roles* para propósitos administrativos.

### Security
- Service Control Policies (SCP) são políticas do IAM aplicadas a OU ou às contas para restringir usuários e funções
- SCP não se aplica a *management account* que tem, sempre, *full admin power*.
- É necessário ter autorizações explícitas, pois, diferentemente do IAM, políticas em Organizations não permitem nada por padrão.
- ![[Pasted image 20240228211814.png]]


# Resource-based Policies and IAM Roles

- Quando uma *role* é assumida, o usuário desiste de suas permissões originais e fica somente com as permissões configuradas na *role*.
- Ao contrário, quando utilizada uma resource-based policy, o *principal*, ou usuário, não precisa desistir de suas permissões
	- Exemplo: Usuário na Conta A precisa escanear uma tabela no DynamoDB na Conta A e dar o resultado em um bucket S3 na Conta B
- Resource-based policies são suportadas por diversos serviços

## EventBridge Security Example
- Quando um evento executa, ele precisa de permissões no alvo para concluir com sucesso sua função.
- *Resource-based Policy*: são necessárias em recursos como Lambda, SNS, SQS, CloudWatch Logs, API Gateway.
	- Dessa forma, o recurso que o EventBridge Rule precisa executar, necessita ter uma policy que permita o EventBridge.
- *IAM Role*: são necessárias em recursos como Kinesis Stream, Systems Manager Run Command, ECS Task
	- Nestes casos, o próprio EventBridge precisa assumir uma IAM Role que os permita executar suas ações nos determinados serviços.

# Policy Evaluation Logic
## Permission Boundaries
- IAM Permission Boundaries são suportadas apenas para usuários e *roles*, não a grupos.
- Boundaries é uma feature avançada utilizada para gerenciar uma política que determina o máximo de permissões que uma entidade no IAM pode ter.
	- Exemplo: Um usuário X têm AdministratorAccess Policy em si, mas um limite de permissão configurado, que lhe dá apenas acesso ao S3. Mesmo com a AdministratorAccess Policy anexada, o usuário, devido ao limite de permissão configurado, só terá acesso ao S3. 
- Boundaries podem ser utilizados em combinação com AWS Organizations SCPs
- *Use Cases*
	- Delegar responsabilidades a não-administradores dentro de seus limites de permissão, por exemplo, criar novos usuários IAM.
	- Permitir que os desenvolvedores se auto-atribuam permissões e gerencia sua próprias permissões, sem permitir que eles escalem seus privilégios, como fazerem de si mesmos administradores.
	- Útil para restringir um usuário específico, invés da conta inteira utilizando Organizations e SCP



# Identity Center
- Identity Center é o sucessor do AWS Single Sign-On
- Providencia um único login (single sign-on para todas as contas em uma *Organization*; *business cloud applications*; *SAML2.0-enabled applications*; *EC2 Windows Instances*
- Identity Providers
	- *built-in identity store* no IAM Identity Center;
	- 3rd party como *Active Directory*, *One Login*, *Okta*

## Fine-Grained Permissions and Assignmnets
### Multi-Account Permissions
- Gerencia o acesso de todas as contas AWS dentro da *Organization*
- *Permission Sets*: uma coleção de um ou mais policies atribuídas a usuários e grupos que definem o acesso na AWS
### Application Assignmnets
- Acesso SSO a diversos *SAML 2.0 business applications* - Salesforce, Box, Microsoft 365
- Providencia URLs necessárias, certificados e metadados

### Attribute-Based Access Control (ABAC)
- Permissões detalhadas baseadas nos atributos do usuário armazenados no IAM Identity Center Identity Store
	- Example: cost center, title, locale, etc
- *Use Case*
	- Definir permissões uma única vez, e então modificar o acesso à AWS mudando os atributos.

# Directory Services
## Microsoft Active Directory (AD)
- Active Directory é um serviço da Microsoft encontrado em qualquer Windows Server com AD Domain Services
- Basicamente é um banco de dados de objetos: contas de usuários, computadores, impressoras, *file shares*, grupos de segurança
- Utilizado para centralizar o gerenciamento da segurança, criar contas, atribuir permissões
- Objetos são organizados em *trees*; um grupo de *trees* é uma *forest*

## AWS Directory Services
- Directory Services é uma forma de criar AD na AWS de três jeitos.
1. AWS Managed Microsoft AD
   - Cria seu próprio AD na AWS, gerencia os usuários localmente com suporte a MFA
   - Estabelece *trust connections* com seu *on-premise AD*
2. AD Connector
   - *Directory Gateway* (Proxy) para redirecionar ao *on-premise AD* com suporte a MFA
   - Usuários são gerenciados no *on-premise AD*
3. Simple AD
   - AD-compatible managed directory on AWS
   - Não pode ser integrado com um *on-premise AD*
   - ![[Pasted image 20240228223343.png]]

# Control Tower
- Forma fácil de configurar e governar um ambiente de diversas contas na AWS de forma segura e conforme baseado em melhores práticas
- AWS Control Tower utiliza AWS Organizations para criar contas
- Benefícios
	- Automate the set up of your environment in a few clicks
	- Automate ongoing policy management using guardrails
	- Detect policy violations and remediate them
	- Monitor compliance through an interactive dashboard

## GuardRails
- Providencia governança contínua para seus ambientes na Control Tower
- *Preventive GuardRail*: utiliza SCPs
	- Como o nome afirma, previne ações, como restringir regiões para todas as contas
- *Detective GuardRail*: utiliza AWS Config
	- Utiliza para verificação de conformidades o AWS Config e verifica não-conformidades em todas as contas, por exemplo, identificar recursos sem tag.
- ![[controltower.png]]