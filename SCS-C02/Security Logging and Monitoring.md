# Amazon Inspector
- Automated Security Assessments
- **For EC2 Instances**
	- Leveraging the **AWS System Manager (SSM) agent**
	- Analyze against **unintended network accessibility**
	- Analyze the **running OS** against known vulnerabilities
- **For Container Images push to ECR**
	- Assessment of Container images as they are pushed
- **For Lambda Functions**
	- Identifies software vulnerabilities in function code and package dependencies
	- Assessment of functions as the are deployed
- Reporting & integration with AWS Security Hub
- Send findings to Amazon Event Bridge
- Inspector is **only for EC2 instances, Container Images, and Lambda functions**
	- Continuous scanning of the infrastructure, only when needed
	- Package vulnerabilities (EC2, ECR & Lambda) - database of CVE
	- Network Reachability (EC2)
	- A risk score is associated with all vulnerabilities for prioritization
# Logging for Security and Compliance
- To help compliance requirements, AWS provides many service-specific security and audit logs
- Service Logs include
	- ==CloudTrail trails==: trace all API calls
	- ==Config Rules==: for config and compliance over time
	- ==CloudWatch Logs==: for full data retention
	- ==VPC Flow Logs==: IP traffic within your VPC
	- ==CloudFront Logs==: web distribution  access logs
	- ==WAF Logs==: full logging of all requests analyzed by the service
- **Logs can be analyzed using AWS Athena if they're stored in S3**
- **You should encrypt logs in S3, control access using IAM & Bucket Policies, MFA**
- **Move logs to Glacier for cost savings**
# Systems Manager
- Helps you manage your EC2 and On-Premises systems at scale
- Get operational insights about the state of your infrastructure
- Easily detect problems
- **Patching automation for enhanced compliance**
- Works for both Windows and Linux OS
- Integrated with CloudWatch metrics / dashboards
- Integrated with AWS Config
- Free Service
### Features
- **Resource Groups**
- *Operations Management*
	- Explorer
	- OpsCenter
	- CloudWatch Dashboard
	- PHD
	- Incident Manager
- *Shared Resources*
	- **Documents**
- *Change Management*
	- Change Manager
	- **Automation**
	- Change Calendar
	- **Maintenance Windows**
- *Application Management*
	- Application Manager
	- AppConfig
	- **Parameter Store**
- *Node Management*
	- Fleet Manager
	- Compliance
	- **Inventory**
	- Hybrid Activation
	- **Session Manager**
	- **Run Command**
	- **State Manager**
	- **Patch Manager**
	- Distributer
## Tags & SSM Resource Groups
- You can add text key-value pairs called Tags to many AWS resources
- Commonly used in EC2
- Free naming, common tags are Name, Environment, Team
- They're used for
	- *Resource Grouping*
	- *Automation*
	- *Cost allocation*
- Create, view or manage logical group of resources thanks to **tags**
- Allows creation of logical groups of resources such as
	- *Applications*
	- *Different layers of an application stack*
	- *Production versus development environments*
- Regional service
- Works with EC2, S3, DynamoDB, Lambda, etc.
## Documents
- Documents can be in JSON or YAML
- You define parameters
- You define actions
- Many documents already exist in AWS
![[Pasted image 20240904221547.png]]
### Run Command
- Execute a document (= script) or just run a command
- Run command across multiple instances (using resource groups)
- Rate Control / Error Control
- Integrated with IAM & CloudTrail
- No need for SSH
- Command Output can be shown in the Console, sent to S3 bucket or CloudWatch Logs
- Send notifications to SNS about command statues (In progress, Success, Failed)
- Can be invoked using EventBridge
![[Pasted image 20240904222900.png|400]]
## Automations
- Simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources
- ==Example==: *restart instances; create an AMI; EBS snapshot*
- **Automation Runbook**
	- *SSM Documents of type Automation*
	- *Defines actions preformed on your EC2 instances or AWS resources*
	- *Pre-defined runbooks (AWS) or create custom runbooks*
- **Triggered by**
	- *Manually using AWS Console. AWS CLI or SDK*
	- *By Amazon EventBridge*
	- *On a schedule using Maintenance Windows*
	- *By AWS Config for rules remediations*
![[Pasted image 20240906120347.png|400]]
### Perplexity Answer: Automation and Documents
#### Key Differences

1. **Complexity**: Command documents are simpler and focus on immediate command execution, while Automation documents support complex workflows and interactions with multiple AWS services.
2. **Execution Model**: Command documents are executed on demand, whereas Automation documents can be scheduled or triggered based on events.
3. **Functionality**: Automation documents can handle retries, branching, and dependencies between steps, which Command documents do not support[1](https://stackoverflow.com/questions/70897003/what-is-the-difference-between-an-aws-systems-manager-document-of-type-automatio)[2](https://docs.aws.amazon.com/en_en/systems-manager/latest/userguide/documents.html)[3](https://repost.aws/questions/QUHIPy2I-PRDSp4XpaK4Siqg/state-manager-associations-with-ssm-command-documents-vs-ssm-automation-documents).
In summary, use Command documents for straightforward, immediate tasks on instances and Automation documents for orchestrating more complex workflows that involve multiple AWS services and require conditional logic.
## Parameter Store
- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation
### Hierarchy
- /my-department/
	- my-app/
		- dev/
			- db-url
			- db-pass
		- prod/
			- db-url
			- db-pass
	- other-app/
- /other-department
- /aws/reference/secretsmanager/secret_ID_in_Secrets_Manager
- /aws/service/ami-amazon-linux-latest/amzn2-ami-hbm-x86_64-gp2 (public)
### Parameter Tiers
- There are two tiers: **standard** and **advanced** parameter tiers
- ==Standard==: 10.000 parameters; 4KB Max Size; No Cost; Free Storage Price; No Parameter Policies
- ==Advanced==: 100.000 parameters; 8KB Max Size; With Cost; $0.05 per advanced parameter per month; With Parameter Policies
### Parameter Policies
- Only for **Advanced Parameters**
- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time
![[Pasted image 20240906122527.png]]
## Inventory
- Collect metadata from your managed instances (EC2/On-Premises)
- Metadata includes *installed software, OS drivers, configuration, installed updates, running services*
- View data in AWS Console or store in S3 and query and analyze using Athena and QuickSight
- Specify metadata collection interval (minutes, hours, days)
- Query data from multiple AWS accounts and regions
- Create Custom Inventory for your custom metadata (e.g. rack location of each managed instance)
## State Manager
- Automate the process of **keeping** your managed instances (EC2/On-Premises) in a state that you define
- ==Use Cases==: bootstrap instances with software, patch OS/software updates on a schedule
- **State Manager Association**
	- *Defines the state that you want to maintain to your managed instances*
	- *==Example==: port 22 must be closed, antivirus must be installed*
	- Specify a schedule when this configuration is applied
- **Uses SSM Documents to create an Association (e.g. SSM Document to configure CW agent)**
## Patch Manager
- Automates the process of patching managed instances
- OS updates, applications updates, security updates
- Supports both EC2 instances and on-premises servers
- Supports Linux, macOS, and Windows
- Patch on-demand or on a schedule using **Maintenance Windows**
- Scan instances and generate patch compliance report (missing patches)
- **Patch Baseline**
	- Defines which patches should and shouldn't be installed on your instances
	- Ability to create custom Patch Baselines (specify approved/rejected patches)
	- Patches can be auto-approved within days of their release
	- By default, install only critical patches and patches related to security
- **Patch Group**
	- Associate a set of instances with a specific Patch Baseline
		- ==Example==: create Patch Groups for different environments (e.g. dev, test, prod)
	- Instances should be defined with the **tag key Patch Group**
	- An instance can only be in one Patch Group
	- Patch Group can be registered with only one Patch Baseline
### Patch Baseline
- **Pre-Defined Patch Baseline**
	- Managed by AWS for different Operating Systems (can't be modified)
	- **AWS-RunPatchBaseline (SSM Document)**: apply both operating system and application patches (Linux, macOS, Windows Server)
- **Custom Patch Baseline**
	- Create your own Patch Baseline and choose which patches to auto-approve
	- Operating System, allowed patches, rejected patches
	- Ability to specify custom and alternative patch repositories
### Maintenance Windows
- Defines a schedule for when to perform actions on your instances
	- ==Example==: OS patching , updating drivers, installing software
- Maintenance Window contains
	- Schedule
	- Duration
	- Set of registered instances
	- Set of registered tasks
## Session Manager
- Allows you to start a secure shell on your EC2 and on-premises servers
- Access through AWS Console, AWS CLI, or Session Manager SDK
- **Does not need SSH access, bastion hosts, or SSH keys**
- Supports Linux, macOS, and Windows
- Log connections to your instances and executed commands
- Session log data can be sent to S3 or CloudWatch Logs
- CloudTrail can intercept **StartSession** events
- **IAM Permissions**
	- Control which users/groups can access Session Manager and which instances
	- Use tags to restrict access to only specific EC2 instances
	- Access SSM + write to S3 + write to CloudWatch
	- Optionally, you can restrict commands a user can run in a session
![[Pasted image 20240909131952.png|400]]
