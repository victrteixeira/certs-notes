- O curso aborda muitíssimas coisas, mas nesta seção, foi anotado apenas o que eu NÃO sabia

# NAT Instances
- NAT Instances != NAT Gateway
	- NAT Instances são obsoletos, e serão substituídos pelo NAT Gateway
- Permite que instâncias EC2 em subredes privadas tenham acesso à internet.
- A instância precisa ser lançada em uma subnet pública
- É necessário desabilitar Source/Destination Check nas configurações da EC2
- Precisa ter um EIP alocado
- Route Tables precisam ser configuradas para rotear o tráfico da subnet privada para o NAT Instance
- Amazon Linux AMI pre-configurada disponível
- NAT Instances não são configuradas de forma que sejam *highly available / resilient* per si.
	- Para alcançar isso, é necessário criar um ASG em multi-AZ com um *resilient user-data script*
- Internet Traffic Bandwidth depende do tipo de instância EC2

# NACLs
- NACL são como um firewall que controla o tráfego de e para as subnets
- Um NACL por subnet. Novas subnets terão o Default NACL atribuído a elas
- NACL Rules definition
	- Rules tem um número entre 1 e 32766 com maior precedência para o menor - ou seja 1 tem maior precedência que 10
	- O primeiro match lidera a decisão: se #100 ALLOW 10.0.0.10/32 e #200 DENY 10.0.0.10/32, o endereço será permitido, pois 100 tem maior precedência que 200
	- A última regra é um asterisco (\*) e nega uma requisição em casos de *no rule match*
	- AWS recomenda adicionar rules em incrementos de 100
- Novos NACLs criados irão negar qualquer coisa por padrão
- NACL é uma excelente forma de bloquear um endereço de IP específico em subnet level
## Stateless and Stateful
- NACLs são Stateless, isso significa que eles não mantém as permissões de entrada ou de saída, elas precisam ser configuradas independentemente.
- Allow NACL Inbound Rule não autoriza automaticamente uma NACL Outbound Rule mesmo que seja para a mesma requisição.
- Security Groups em contrapartida são stateful, isso significa que se uma inbound request for permitida, ela terá seu Outbound permitido
## Default NACLs
- O NACL padrão de toda subnet aceitará qualquer requisição inbound ou outbound
- É recomendável não modificar o NACL default, mas criar um novo invés disso, para caso seja necessário criar subnet rules para gerenciamento de trafégo
## NACL with Ephemeral Ports
- Para quaisquer dois endpoints estabelecerem uma conexão, eles precisam utilizar portas
- Clientes se conectam a uma porta específica (como 443 para HTTPS) e esperam uma resposta em uma porta efêmera
- OS usam diferentes *port ranges*
	- MS Windows 10 -> 49152 - 65535
	- Linux Kernels -> 32768 - 60999
- Quando conectados uma EC2 com um RDS, após a conclusão da request, o response do RDS precisa autorizar uma Outbound TCP Rule nas portas 1024-65535, assim como a EC2 precisa autorizar uma Inbound TCP Rule nas portas 1024-65535 para receber o response do RDS

# VPC Peering
- Administra conexões privadas entre duas VPCs utilizando a rede da AWS
- Facilita com que as aplicações se comportem como se elas estivessem todas na mesma rede
- CIDRs não podem colidir uns com os outros: VPC-A CIDR 10.0.0.0/8 não pode ser igual ao VPC-B CIDR 10.0.0.0/8. Esta situação causará problemas
- A conexão através do VPC Peering não é transitiva, cada VPC precisa ter seu próprio VPC Peering para se conectar com uma outra
- É preciso atualizar as tabelas de rotas de cada subnet das VPCs para garantir que instâncias EC2 podem se comunicar entre si
- É possível realizar VPC Peering através de diferentes regiões e também através de diferentes contas AWS
- É possível referenciar um Security Group em uma *peered VPC* - funciona para diferentes contas que estejam na mesma região
	- SG Source: accountId/sg-027ad1...

# VPC Endpoints
- Todo serviço da AWS é publicamente exposto com uma URL pública
- VPC Endpoints permite conexão com os serviços da AWS utilizando uma rede privada invés de utilizar a rede pública (VPC Endpoints is powered by AWS PrivateLink)
- São redundantes e escalam horizontalmente
- Remove a necessidade de IGW, NATGW para acessar serviços da AWS
- Em caso de problemas cheque: DNS Setting Resolution in your VPC; Route Tables

## Types of Endpoints
1. Interface Endpoints (powered by PrivateLink)
   - Provisiona um ENI (private IP address) como um entry point. É preciso anexar um security group para isso
   - Suporta a maioria dos serviços da AWS
   - $ por hora + $ por GB de dados processados
2. Gateway Endpoints
   - Provisiona um *gateway* e precisa ser utilizado como um alvo em uma route table. Não utiliza security group
   - Suporta APENAS S3 e DynamoDB
   - Grátis
- Para o S3, o Gateway endpoint é preferível de ser utilizado, exceto quando o trafego vêm de um servidor on-premise.

# VPC Flow Logs
- Captura informação acerca de trafego de IP ocorrendo nas interfaces do VPC Flow Logs, Subnet Flow Logs ou Elastic Network Interface (ENI) Flow Logs
- Ajuda a monitorar e com *troubleshooting* problemas de conexão
- Dados do Flow logs podem ir para o S3, CloudWatch Logs, e Kinesis Data Firehose
- Captura informações de rede de interfaces gerenciadas pela AWS também, como ELB, RDS, ElastiCache, Redshift, Workspaces, NATGW, Transit Gateway..

## Log Syntax
- srcaddr & dstaddr - ajuda a identificar IPs problemáticos
- srcport & dstport - ajuda a identificar portas problemáticas
- Action - sucess ou failure da requisição devido a um SG ou NACL
- Podem ser utilizados para análise com *usage patterns* ou comportamentos maliciosos
- Query VPC Flow Logs utilizando Athena no S3 ou CloudWatch Logs Insights


# Site-To-Site VPN
## Resume
1. Virtual Private Gateway - VGW
	- VPN concentrador do lado da AWS para a VPN connection
	- VGW é criado e anexado a uma VPC que você deseja criar a conexão site-to-site VPN
	- Possibilidade de customizar o ASN (*Autonomous System Number*)
2. Customer Gateway - CGW
	- Software application ou dispositivo físico no lado do cliente para a VPN connection

## Connections
- Para realizar conexões site-to-site é necessário configurar um Customer Gateway (Public IP)
### Customer Gateway Device (On-premises)
- Se o Customer Gateway for público, usa-se o respectivo Public Internet-routable IP address do Customer Gateway Device
   - Conecta o VGW com o Customer Device através do IP público
- Se o Customer Gateway for privado, ou seja, está atrás de um NAT Gateway habilitado para NAT Traversal (NAT-T) deve-se utilizar o endereço IP público do NAT device
- É mandatório habilitar *Route Propagation* para as route tables do VGW associado com suas subnets.

## CloudHub
- Providencia comunicação segura entre múltiplas instalações (site), se você tem múltiplas conexões de VPN
- *Low-cost hub-and-spoke model* para conexões de rede primárias e secundárias entre diferentes localidades
- É uma VPN, então a conexão irá pela internet pública
- Para configurá-lo, conecte múltiplas VPNs ao mesmo VGW, configure *dynamic routing* e configure as *route tables*

# Direct Connect (DX)
- Providencia uma conexão privada dedicada da *remote network* para a sua VPC
- Conexões dedicadas precisam ser configuradas entre o Direct Connect e o AWS Direct Connect Locations
- Configurar uma VGW na VPC é mandatório
- Permite acessar recursos públicos como o S3 e privados como uma EC2 utilizando a mesma conexão
- *Use Cases*
	- *Increase Bandwidth Throughput - working with large data sets - lower cost
	- More consistent network experience - applications using real-time data feeds
	- *Hybrid Environments (on premise + cloud)*
![[dxdiagram.png]]

## DX Gateway
- Caso seja necessário configurar um Direct Connect Service para mais de uma VPC em diferentes regiões, é preciso utilizar o Direct Connect Gateway
- DX Gateway ficará entre as duas regiões, e se conectará com o Direct Connection, o DX Gateway enviará o trafego para o VGW na região das subnets configuradas

## Connection Types
1. Dedicated Connections
   - 1Gbps, 10Gbps, e 100Gbps de capacidade
   - Porta ethernet física dedicada a um cliente
   - A requisição é feita para AWS primeiro, então completada pelos AWS Direct Connect Partners
2. Hosted Connections
   - 50Mbps, 500Mbps, até 10Gbps
   - A requisição da conexão é feita pelos AWS Direct Connect Partners
   - Capacidade pode ser adicionada ou removida sobre demanda
   - 1,2,5,10 Gbps disponíveis em AWS Direct Connect Partners
- Os prazos de entrega são geralmente mais longos que 1 mês para estabilizar uma nova conexão

## Encryption
- Os dados **não são** criptografados *in transit*, mas são privados
- Para configurar criptografia, é necessário utilizar o DX + VPN que providencia um *IPsec-encrypted private connection*

## Resiliency
- Há dois tipos de resiliência para DX: *High Resiliency for Critical Workloads* e *Maximum Resiliency for Critical Workloads*
- HR configura uma conexão em múltiplas localidades, então se um data center falha, o trafego será redirecionado para o outro.
- MR é alcançada separando ligações que terminam em dispositivos separados em mais de um local
![[hrandmr.png]]
- Site-to-Site VPN connection pode ser utilizada como uma forma de backup. Em caso de falha do DX, um Site-to-Site VPN Backup Connection pode ser ativado para manter conexão

# Transit Gateway
- Útil tendo-se necessidade de ter *transitive peering* entre milhares de VPCs e On-Premises Networks, *Hub-and-Spoke (star) connection*
- Recurso regional, funciona trans-regional
- Pode ser compartilha entre contas utilizando o *Resource Access Manager (RAM)*
- Transit Gateways podem ser emparelhado com outro Transit Gateway entre regiões
- Route Tables definem o limite de que VPC pode se comunicar com outra VPC
- Funciona com DX Gateway, VPN Connections
- Suporta IP Multicast
	- IP Multicast não é suportado por mais nenhum outro serviço da AWS

## Transit Gateway: Site-To-Site VPN ECMP
- ECMP traduz-se para *Equal-cost multi-path routing*
- É uma estratégia de roteamento que permite enviar um pacote para múltiplos *best path*
- *Use Case*
	- Create multiple site-to-site VPN connections to increase the bandwidth of your connection to AWS
![[tg-ecmp.png]]

# Traffic Mirroring
- Permite capturar trafego e inspecionar na sua VPC
- Roteia o trafego para *security appliances* que podem gerí-los
- *Capture the Traffic*
	- From (Source) - ENIS
	- To (Targets) - an ENI or a Network Load Balancer
- Captura todos os pacotes, ou captura os pacotes de interesse (opcionalmente, *truncate packets*)
- Source e Target podem estar na mesma VPC ou em diferentes VPCs (VPC Peering)
- *Use Cases*
	- Content Inspection
	- Threat Monitoring
	- Troubleshooting

# Egress-only Internet Gateway
- Usado para IPv6 paenas
- Similar a um NAT Gateway mas para endereços IPv6
- Permite que instancias na VPC realizem *outbound connections* através do IPv6 enquanto bloqueiam tentativas de conexão da internet para as instâncias utilizando IPv6
	- É necessário atualizar a tabela de rotas
![[egw-routing.png]]
# Network Firewall
- Protege a VPC inteiramente
- Do layer 3 ao layer 7 da modelo OSI
- Pode-se inspecionar em quaisquer direções
	- VPC to VPC Traffic
	- Outbound to Internet
	- Inbound from Internet
	- To / From DX and Site-to-Site VPN
- Internamente o Network Firewall utiliza o AWS Gateway Load Balancer
- Regras podem ser gerenciadas de forma centralizada entre contas pelo AWS Firewall Manager para aplicar as mesmas regras a mais de uma VPC
## Fine Grained Controls
- Suporta milhares de regras
	- IP and Port - example: 10.000s of IPs filtering
	- Protocol - example: block the SMB protocol for outbound communications
	- Stateful domain list rule groups: only allow outbound traffic to \*.mycorp.com or third-party software repo
	- General pattern matching using regex
- Filtro de trafego: allow, drop, ou alert para trafego que corresponda as regras
- *Active flow inspection*: protege contra *network threats* com *intrusion-prevention capabilities* (como Gateway Load Balancer, mas totalmente gerenciado pela AWS)
- Envia os logs de correspondências de regras para o S3, CloudWatch Logs, Kinesis Data Firehose